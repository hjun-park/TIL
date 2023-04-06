> 출처 : [모던 자바 인 액션](http://www.yes24.com/Product/Goods/77125987)

# Parallel Stream(병렬 스트림)

## 1. 병렬 스트림

#### 🔵 일반 스트림 예제

```java
public class Main {
	public long sequentialSum(long n) {
		return Stream.iterate(1L, i -> i + 1)   // 무한 자연수 스트림
			.limit(n)                           // n개의 요소로 제한   
			.reduce(0L, Long::sum);             // 모든 요소의 합
	}
}
```

<br />

#### 🔵 병렬 스트림 예제

```java
public class Main {
	public long parallelSum(long n) {
		return Stream.iterate(1L, i -> i + 1)
			.limit(n)
			.parallel()                     // 스트림을 병렬 스트림으로 변환
			.reduce(0L, Long::sum);
	}
}
```

<br />

#### 🔵 병렬 스트림 스레드 풀은 어디서 생성되고 몇 개나 생성될까 ?

- 병렬 스트림은 내부적으로 `ForkJoinPool` 을 사용한다.
- `Runtime.getRuntime().availableProcessors()` 를 사용하여 사용 가능한 프로세서 개수를 알아낼 수 있다.
- `System.setProperty("java.util.concurrent.ForkJoinPool.common.parallelism", "12");` 를 사용하여 스레드 풀의 크기를 지정할 수 있다.

<br />

## 2. 스트림 성능 측정

- 병렬화는 순차나 반복보다 무조건 성능이 좋아지는 것은 아니다.
- 책에서는 성능 최적화 시에는 `측정`을 정말 강조한다.
- 성능 `측정`은 `java MicroBenchmark Harness` 를 사용한다.

#### 🔵 JMH를 이용한 성능 측정

```java

@BenchmarkMode(Mode.AverageTime)    // 메서드 실행 평균 시간
@OutputTimeUnit(TimeUnit.MICROSECONDS)    // 결과 출력 단위 (ms)
@Fork(value = 2, jvmArgs = {"-Xms4G", "-Xmx4G"})    // 4GB 힙 공간에서 2번 벤치마크 수행
// @Warmup(iterations = 5, time = 1)    // 5번의 벤치마크를 1초 동안 수행
// @Measurement(iterations = 5, time = 1)    // 5번의 벤치마크를 1초 동안 수행
public class JMH {
	private static final long n = 10_000_000L;  // 10,000,000

	@Benchmark  // 벤치마크 대상 메서드
	public long sequentialSum() {
		return Stream.iterate(1L, i -> i + 1)
			.limit(n)
			.reduce(0L, Long::sum);
	}

	@Benchmark
	public long iterativeSum() {
		long result = 0;
		for (long i = 1L; i <= n; i++) {
			result += i;
		}
		return result;
	}

	@Benchmark
	public long parallelSum() {
		return Stream.iterate(1L, i -> i + 1)
			.limit(n)
			.parallel()
			.reduce(0L, Long::sum);
	}

	@Teardown(Level.Invocation)    // 벤치마크 메서드가 실행된 후 호출
	public void tearDown() {
		System.gc();    // 가비지 컬렉션 수행
	}

}
```

- 테스트 해 보면 오히려 병렬이 더 느리다.
- `reduce` 연산 시 일정 범위 숫자가 아닌 무한 숫자기 때문이다.
    - 이 경우 `chunk`로 분할할 수 없어 `순차처리`와 다를 바 없다.
    - 오히려 스레드를 할당하느라 오버헤드만 증가한다.

<br />

## 3. 병렬 스트림 잘 사용하기

- 단순히 데이터가 1000개 이상 있을 때 병렬 스트림 사용하라처럼 `양`을 기준으로 병렬 스트림 사용은 옳지 않다.

<br />

1. `성능을 측정해라`
    - 순차 스트림과 병렬 스트림 중 어느 것이 더 빠른지 모르겠다면 벤치마킹 해본다.
2. `boxing 주의`
    - `boxing`을 피할 수 있게 `기본형 특화 스트림(IntStream, DoubleStream, LongStream)`을 사용한다.
3. `순차 스트림보다 병렬 스트림에서 성능이 떨어지는 연산을 피한다`
    - `limit`나 `findFirst` 같이 요소의 `순서`에 의존하는 연산은 `병렬 스트림`에서는 비싸다
    - `findAny` 같이 요소의 `순서`에 의존하지 않는 연산을 사용한다.
4. `전체 파이프라인 연산 비용 고려`
    - 처리 개수 `N`, 하나 요소 처리 시에 `Q` 비용이라면 `N * Q` 비용이 발생한다.
    - `Q`가 높은 경우 병렬 스트림으로 처리 가능하다.
5. `소량의 데이터는 병렬 스트림이 오히려 느릴 수 있다`
    - 소량의 데이터는 오히려 병렬 스트림의 사용으로 발생하는 오버헤드를 상쇄시킬 만큼 이득을 얻지 못한다.
6. `스트림을 구성하는 자료구조 확인`
    - `LinkedList`는 분할하려면 모든 요소 탐색 필요하지만 `ArrayList`는 요소를 두 부분으로 쉽게 분할 가능
    - `ArrayList`, `IntStream.range` 는 병렬화에 좋은 자료구조이다.
7. `최종 연산 병합 비용 고려`
    - `reduce` 같은 최종 연산은 `병렬 스트림`에서는 `병렬로 처리한 결과를 합쳐야 한다.`
    - 이 과정에서 `병합 비용`이 발생한다. (`병합 비용`이 비싸면 병렬 스트림으로 얻은 성능 이익이 상쇄된다.)
    - `병렬 스트림`에서는 `최종 연산`을 `집계 연산`으로 사용하는 것이 좋다.

<br />

## 4. 포크/조인 프레임워크

- `ForkJoinPool`은 `ExecutorService`인터페이스를 확장한 `포크/조인 프레임워크`의 핵심이다.
- `포크/조인 프레임워크`는 재귀적으로 `작업을 분할`하고 `분할된 작업을 각각 실행`하고 `실행된 결과를 합쳐서 최종 결과를 만드는` `프레임워크`이다.

<br />

#### 🔵 포크 조인 과정?
![image](https://user-images.githubusercontent.com/70880695/230416361-96f1be4c-1985-4b26-9ac1-f6c8475ffe78.png)
> 참고 : [Hyung1 : [Java] Fork Join Pool](https://junghyungil.tistory.com/103)

<br />

### 4-1. `RecursiveTask`
- `RecursiveTask`는 `ForkJoinTask`를 상속받아 `작업의 결과를 반환`하는 `작업`을 정의한다.
- `RecursiveTask`는 `compute` 메서드를 구현해야 한다.
- `compute` 메서드 `fork/join` 과정
  1. `RecursiveTask`를 재귀적으로 충분히 작아질 때까지(`Threshold`) `분할` `(fork)`
  2. 모든 `분할된 작업`을 병렬로 수행
  3. `각 실행된 결과를 합쳐서 최종 결과를 만들어 반환`한다.

```java
public class ForkJoinSumCalculator extends RecursiveTask<Long> {
    private final long[] numbers;
    private final int start;
    private final int end;

	// 1. 재귀적으로 이 이하로 분할할 수 없는 크기 정의 (fork 수행)
    public static final long THRESHOLD = 10_000;

	// 2. 메인 태스크 생성자
    public ForkJoinSumCalculator(long[] numbers) {
        this(numbers, 0, numbers.length);
    }
	
	// 3. 재귀적으로 포크할 private 생성자
    private ForkJoinSumCalculator(long[] numbers, int start, int end) {
        this.numbers = numbers;
        this.start = start;
        this.end = end;
    }

	// 4. compute는 RecursiveTask의 추상 메서드
    @Override
    protected Long compute() {
        int length = end - start;   // 분할할 작업의 크기
        
        // 5. 분할할 작업의 크기가 임계값 이하면 순차적으로 결과를 계산
        if (length <= THRESHOLD) {
            return computeSequentially();
        }

		// 6. 배열의 첫 번째 절반만 더하는 subTask 생성
        ForkJoinSumCalculator leftTask = new ForkJoinSumCalculator(numbers, start, start + length / 2);
        leftTask.fork();    // ForkJoinPool의 다른 스레드로 첫 번째 서브태스크를 비동기 실행

        // 7. 배열의 나머지 절반을 더하는 subTask 생성
        ForkJoinSumCalculator rightTask = new ForkJoinSumCalculator(numbers, start + length / 2, end);
        Long rightResult = rightTask.compute();    // 나머지 절반에 대한 subTask 동기 실행 (끝나면 left도 이미 끝났으니 동기로 실행)
        Long leftResult = leftTask.join();         // 첫 번째 subTask의 결과를 읽는다.아직 결과가 없으면 기다린다.

        return leftResult + rightResult;    // 두 서브태스크의 결과를 조합
    }

	// 8. 더 이상 분할이 안 되는 경우 실행되며, 순차적으로 배열의 각 요소를 더한다.
    private long computeSequentially() {
        long sum = 0;
        for (int i = start; i < end; i++) {
            sum += numbers[i];
        }
        return sum;
    }
}
```
> 예제 참고 : [모던 자바 인 액션 ForkJoinSumCalculator.java]

#### 🔵 위 코드 동작방식
1. `ForkJoinSumCalculator` ==> `ForkJoinPool`로 전달
2. `ForkJoinPool`은 `Thread` 이용하여 `ForkJoinSumCalculator`의 `compute` 메서드를 실행
3. `compute` 메서드는 병렬 실행할 수 있을만큼 태스크 크기가 작아졌는지 확인한다. (length <= THRESHOLD ??)
   1. 크기가 아직 크다면 절반으로 `분할`하여 두 개의 `ForkJoinSumCalculator` 생성
4. `분할`된 `ForkJoinSumCalculator`를 `fork`하면 `ForkJoinPool`이 생서어된 두 `ForkJoinSumCalculator`를 `비동기` 실행
5. 이 과정이 재귀적으로 반복되어 조건을 만족할 때까지 `분할`한다.
6. `분할`이 다 끝나면 각 subTask는 순차적 처리되며 트리 형식의 결과를 역순으로 방문하여 최종 연산 진행
- fork join algorithm
![image](https://user-images.githubusercontent.com/70880695/230418050-e444f919-4276-4c4e-9bb9-f700d7db113a.png)
> [GeeksForGeeks : Difference Between Fork/Join Framework and ExecutorService in Java](https://www.geeksforgeeks.org/difference-between-fork-join-framework-and-executorservice-in-java/)

<br />

### 4-2. 포크/조인 사용법
1. `join()`을 `task`에서 호출하면 해당 `task`의 결과가 나오기까지 호출한 쪽을 `block`한다.
   - 그렇기 때문에 subTask가 모두 시작된 뒤에 `join` 호출 필요
2. `RecursiveTask` 내에서는 `ForkJoinPool` `invoke` 메서드를 사용할 수 없다.
    - `invoke` 메서드의 동작원리는 ? (`TODO`)
    - `RecursiveTask` 내에서는 `fork`와 `join`을 직접 호출해야 한다.
3. 한 쪽은 `fork` 다른 한 쪽은 `compute` 호출하는 것이 효율적이다.
   - 두 subTask의 한 task에는 같은 thread를 재사용할 수 있다. (pool의 thread를 재사용할 수 있다.)
4. 마찬가지로 병렬 스트림처럼 포크/조인 프레임워크도 무조건 빠른 건 아니다.

<br />

262p
