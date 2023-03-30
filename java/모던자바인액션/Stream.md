> 출처 : [모던 자바 인 액션](http://www.yes24.com/Product/Goods/77125987)

# Stream(스트림)

- 스트림이란 `데이터 처리 연산을 징원하도록 소스에서 추출된 연속된 요소`
- 컬렉션은 DVD 영화처럼 DVD의 모든 내용을 다 받을 때까지 대기 후에 재생 가능
    - 반면 스트림은 인터넷 스트리밍처럼 다 받지 않아도 필요할 때마다 조금씩 미리 계산한다.

#### 🔵 Stream 장점

1. `선언형` : 간결하고 가독성 좋다.
2. `조립 가능` : 유연성이 좋아진다.
3. `병렬화` : 성능이 좋아진다.

<br />

## 1. Stream 특징

### 1-1. 파이프라이닝

- 스트림 연산끼리 연결해서 `layziness` `short-circuiting` 최적화 가능

### 1-2. 내부 반복

- 컬렉션은 반복자를 지정하지만 stream은 내부 반복 지원

<br />

## 2. Stream 연산
- Stream의 연산은 `중간 연산` `최종 연산`으로 구분된다.

### 2-1. 중간 연산
1. 중간 연산은 다른 stream을 반환한다.
2. 중간 연산을 여러 개 연결해서 만들 수 있다.
3. `Lazy` : 스트림 파이프라인에 실행하기 전까지 아무 연산을 수행하지 않는다.
   1. `최종 연산`에서 한 번에 처리한다.

#### 🔵 중간연산 예제
```java
public class Main {
	public static void main(String[] args) {
		List<String> names = menu.stream()
            .filter(dish -> dish.getCalories() > 300)
            .map(dish -> dish.getName())
            .limit(3) 
            .collect(toList());
	}
}
```
- `limit`을 이용하여 다 가져오는 것이 아닌 처음 3개만 선택했다. (`쇼트서킷`) 
- `filter` `map`을 같이 병합하여 사용했다. (`루프 퓨전`)

<br />

### 2-2. 최종 연산 
- 파이프라인에서 결과를 도출한다.
- Stream이 아닌 자료형을 반환하는 것들이 최종 연산이다.
```java
public class Main {
	public static void main(String[] args) {
		names.stream().forEach(System.out::println);
	}
}
```

# 155 page
## 3. Stream 활용
### 3-1. 스트림 필터링 
1. `filter` : 조건에 맞는 요소만 선택
2. `distinct` : 중복 제거

```java
class Filtering {
public static void main(String[] args) {
    List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);
    numbers.stream()
        .filter(i -> i % 3 == 0)    // 3으로 나누어 떨어지는 요소만 선택
        .distinct()                // 중복 제거
        .forEach(System.out::println);
    }
}
```

### 3-2. 스트림 슬라이싱
1. `limit` : 처음 n개 요소 선택
2. `skip` : 처음 n개 요소 제외
3. `takeWhile` : 조건에 맞는 요소 선택
4. `dropWhile` : 조건에 맞는 요소 제외
 
```java
class Slicing {
    public static void main(String[] args) {
        List<Dish> menu = Arrays.asList(
            new Dish("pork", false, 800, Dish.Type.MEAT),
            new Dish("beef", false, 700, Dish.Type.MEAT),
            new Dish("chicken", false, 400, Dish.Type.MEAT),
            new Dish("french fries", true, 530, Dish.Type.OTHER),
            new Dish("salmon", false, 450, Dish.Type.FISH));

        // 칼로리 320 미만인 요소만 takeWhile
        List<Dish> dishes = menu.stream()
            .takeWhile(dish -> dish.getCalories() < 320)
            .collect(toList());
        
        // 칼로리 320 초과인 요소만 dropWhile
        List<Dish> dishes2 = menu.stream()
            .dropWhile(dish -> dish.getCalories() < 320)
            .collect(toList());
    }
}
```

### 3-3. 매핑

#### 🔵 map (매핑)
```java
// stream Map 예제
public class Mapping {
	public static void main(String[] args) {
		List<Integer> nameLengths = menu.stream()
			.map(Dish::getName)         // 이름만 가져와서
			.map(String::length)        // 길이를 구한다.
			.collect(toList());       
	}
}
```

<br />

#### 🔵 Stream FlatMap (스트림 평면화)
- flatMap은 각 배열을 스트림으로 변환한 다음에 모든 스트림을 하나의 스트림으로 연결하는 기능을 한다.
- 모던자바인 액션 `퀴즈 5-2` (165page)

```java
// 2개의 숫자 리스트가 있을 때 모든 숫자 쌍의 리스트 반환
// [1, 2, 3] [3, 4] -> [(1, 3), (1, 4), (2, 3), (2, 4), (3, 3), (3, 4)]
public class FlatMap {
	public static void main(String[] args) {
		List<Integer> numbers1 = Arrays.asList(1, 2, 3);
		List<Integer> numbers2 = Arrays.asList(3, 4);
		List<int[]> pairs = numbers.stream()
            .flatMap(num1 -> numbers2.stream()
                .map(num2 -> new int[]{num1, num2})
			)
            .collect(toList());
	}
} 
```

<br />

### 3-4. 검색과 매칭
1. `anyMatch` : 적어도 조건에 하나라도 일치하는지 확인
2. `allMatch` : 모든 요소가 조건에 일치하는지 확인
3. `noneMatch` : 모든 요소가 조건에 일치하지 않는지 확인
4. `findFirst` : 첫 번째 요소 반환
5. `findAny` : 현재 스트림에서 임의의 요소 반환

<br />

#### 🔵 특징
1. `anyMatch` `allMatch` `noneMatch`는 `쇼트서킷`을 지원한다.
   - 따라서 `findFirst` `findAny`보다 더 효율적이다.
2. `findFirst` `findAny`는 `쇼트서킷`을 지원하지 않는다.

<br />

#### 🔵 그럼에도 불구하고 `findFirst` `findAny` 사용하는 이유
1. `병렬 스트림`에서는 `findFirst` `findAny`가 더 빠르다.
2. `병렬 스트림`에서는 `쇼트서킷`을 지원하지 않는다.
    - 병렬 실행에서는 첫 번째 요소를 찾기 어렵기 때문이다.
    - `쇼트서킷`을 지원하지 않는다는 것은 `모든 요소를 검사`한다는 것이다.
    - `병렬 스트림`에서는 `모든 요소를 검사`하는 것이 더 빠르다.

<br />

### 3-5. 리듀싱
- `reduce` : 스트림의 요소를 하나로 줄인다. 즉, 모든 스트림 요소를 처리해서 값으로 도출한다.
- `reduce`는 `쇼트서킷`을 지원한다.

```java
// reduce 예제
// reduce(초기값, BinaryOperator<T> 람다 표현식)
public class Reduce {
    public static void main(String[] args) {
        int sum = numbers.stream().reduce(0, (a, b) -> a + b);
    }
}
```

<br />

### 3-6. 숫자형 스트림
- `IntStream` `LongStream` `DoubleStream` : 기본형 특화 스트림
- `mapToInt` `mapToLong` `mapToDouble` : 스트림의 요소를 특화 스트림으로 매핑

```java
// 숫자형 스트림 예제
public class NumericStream {
    public static void main(String[] args) {
        int sumProtein = menu.stream()
            .mapToInt(Food::getProtien)
            .sum();
    }
}
```

<br />

### 3-7. 스트림 만들기

#### 1) 값, 배열을 스트림으로 변환
1. `Stream.of` : 여러 값들을 스트림으로 변환
2. `Arrays.stream` : 배열을 스트림으로 변환
3. `Stream.empty` : 빈 스트림 생성
4. `Stream.builder` : 빈 스트림 생성
5. `Stream.concat` : 두 개의 스트림을 하나로 합침

<br />

#### 2) 함수로 무한 스트림 만들기
1. `Stream.iterate` : 무한 스트림 생성
2. `Stream.generate` : 무한 스트림 생성

- `iterate`, `generate` 메서드는 무한 스트림을 만들기 때문에 limit 메서드로 스트림의 크기를 제한해야 한다.

<br /> 

#### 🔵 `iterate` `generate` 차이점
- `iterate` : 이전 값을 기반으로 다음 값을 계산
- `generate` : 이전 값을 기반으로 다음 값을 계산하지 않음

<br />

#### 🔵 언제 `iterate`를 사용할까 ? 
- `iterate`는 이전 값을 기반으로 다음 값을 계산하기 때문에 `상태를 유지`해야 한다.
- `iterate`는 `상태를 유지`하기 때문에 `병렬 스트림`에서는 `순차적으로 처리`된다.
- 즉, 순서가 보장되어야할 때 사용

<br />

#### 🔵 언제 `generate`를 사용할까 ?
- `generate`는 이전 값을 기반으로 다음 값을 계산하지 않기 때문에 `상태를 유지`할 필요가 없다.
- `generate`는 `상태를 유지`할 필요가 없기 때문에 `병렬 스트림`에서 `병렬로 처리`된다.
- 순서 보장보다는 `성능`이 중요할 때 사용

### 197 페이지부터


