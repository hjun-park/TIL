> ### 출처 : [모던 자바 인 액션](http://www.yes24.com/Product/Goods/77125987)

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

## 4. 컬렉터
- `컬렉터`는 스트림의 요소를 모아서 결과로 누적하는 다양한 방법을 갖는 최종 연산이다.
- 하나의 값으로 요약하는 `reduce` 뿐만 아니라 `maxBy` `minBy` `summingInt` `averagingInt` `joining` 등 다양한 방법으로 요약할 수 있다.
- 그룹화도 가능하다. `groupingBy` `partitioningBy` `mapping` 등 다양한 방법으로 그룹화할 수 있다.
- `Collector` 인터페이스에 정의된 메서드를 구현해서 커스텀 컬렉터를 만들 수 있다.

<br />

### 4-1. 요약
1. 최소 최대 구하기 : `maxBy` `minBy`
2. 합계 구하기 : `summingInt` `summingLong` `summingDouble`
3. 평균 구하기 : `averagingInt` `averagingLong` `averagingDouble`
4. 최소, 최대, 합계, 평균 모두 구하기  : `summarizingInt` `summarizingLong` `summarizingDouble`
5. 문자열 연결 : `joining`

### 4-2. 리듀싱 
- `reducing`으로도 위 연산들이 가능하다. 하지만 가독성을 위해 구현돼 있는 것들을 사용하자
- `reducing`은 `BinaryOperator`를 인수로 받는다.
- `reducing(초기값, T -> R, BinaryOperator<R>)` : 초기값, 변환 함수, 두 값을 합치는 함수

```java 
// reducing 예제
public class Reducing {
    public static void main(String[] args) {
        int sum = menu.stream()
            .collect(reducing(0, Food::getProtein, (i, j) -> i + j));
    }
}
```

### 4-3. 그룹화
- `그룹화` : 스트림의 요소를 그룹으로 묶는다.
- `그룹화`는 `컬렉터`를 사용한다.

```java
// 그룹화 예제
public class Grouping {
    public static void main(String[] args) {
        Map<Food.Type, List<Food>> foodsByType = menu.stream()
            .collect(groupingBy(Food::getType));
    }
}
```

#### 🔵 mapping을 이용한 그룹화
- `mapping` : 스트림의 요소를 다른 값으로 변환한 다음에 그룹화할 수 있다.
- `mapping(T -> R, Collector<R, A, D>)` : 변환 함수, 컬렉터

```java
// mapping을 이용한 그룹화 예제
public class Grouping {
    public static void main(String[] args) {
        Map<Food.Type, List<String>> foodNamesByType = menu.stream()
            .collect(groupingBy(Food::getType, mapping(Food::getName, toList())));
    }
}
```

<br />

#### 🔵 다수준 그룹화
- `다수준 그룹화` : 두 개 이상의 기준으로 그룹화할 수 있다.
- `groupingBy`를 두 번 호출하면 된다.

```java
// 다수준 그룹화 예제
public class Grouping {
    public static void main(String[] args) {
        Map<Food.Type, Map<Food.CaloricLevel, List<Food>>> foodsByTypeCaloricLevel = menu.stream()
            .collect(groupingBy(Food::getType, groupingBy(food -> {
                if (food.getCalories() <= 400) return Food.CaloricLevel.DIET;
                else if (food.getCalories() <= 700) return Food.CaloricLevel.NORMAL;
                else return Food.CaloricLevel.FAT;
            })));
    }
}
```
- 그룹 안에 그룹이 만들어진다.
- 결과는 아래와 같다.
    ```markdown
    {
        FISH={DIET=[salmon], NORMAL=[prawns], FAT=[shark]},
        OTHER={DIET=[rice], NORMAL=[season fruit], FAT=[pizza]},
        MEAT={DIET=[pork], NORMAL=[beef], FAT=[pork, beef]}
    }
    ```
  
<br />

#### 🔵 서브그룹에서 데이터 수집
- `서브그룹에서 데이터 수집` : 그룹화된 스트림에 `컬렉터`를 적용할 수 있다.
- `groupingBy`의 두 번째 인수로 `컬렉터`를 전달하면 된다.

```java
// 서브그룹에서 데이터 수집 예제
public class Grouping {
    public static void main(String[] args) {
        Map<Food.Type, Long> typesCount = menu.stream()
            .collect(groupingBy(Food::getType, counting()));
    }
}
```
- 결과는 아래와 같다.
    ```markdown
    {FISH=3, OTHER=3, MEAT=4}
    ```
  
<br />

#### 🔵 `collectingAndThen` 사용
- `collectingAndThen` : `컬렉터`의 결과를 `다른 값`으로 변환한다.
- `collectingAndThen(Collector<T, A, R>, Function<R, RR>)` : 컬렉터, 변환 함수


```java
// collectingAndThen 예제
// 각 서브그룹에서 가장 높은 칼로리를 가진 요리를 찾기
public class Grouping {
    public static void main(String[] args) {
        Map<Food.Type, Food> mostCaloricByType = menu.stream()
            .collect(groupingBy(Food::getType, collectingAndThen(
                maxBy(comparingInt(Food::getCalories)), Optional::get)));
    }
}
```
- 결과는 아래와 같다.
    ```markdown
    {FISH=shark, OTHER=pizza, MEAT=pork}
    ```

<br />

#### 🔵 `partitioningBy` 분할 함수
1. `partitioningBy` : `boolean`을 반환하는 `분할 함수`를 인수로 받는다.
2. **기존 `filter` 방식과의 차이점**
   - 기존 `filter`는 `True`인 것만 남겼다.
   - 하지만 `partitioningBy`는 `True`와 `False` 모두 남긴다. 

```java
// partitioningBy 예제
public class Partitioning {
    public static void main(String[] args) {
        Map<Boolean, List<Food>> partitionedMenu = menu.stream()
            .collect(partitioningBy(Food::isVegetarian));
    }
}
```

<br />

## 4-4. Collector 인터페이스
- `Collector` 인터페이스는 `컬렉터`를 만들 때 사용한다.
- `Collector` 인터페이스는 `Supplier`, `Accumulator`, `Combiner`, `Finisher`, `Characteristics`를 인수로 받는다.
- `Supplier` : 컬렉터가 새로운 결과 컨테이너를 만들 때 사용한다.
- `Accumulator` : 컬렉터가 스트림의 다음 요소를 처리할 때 사용한다.
- `Combiner` : 컬렉터가 서로 다른 스레드에서 수집된 부분 결과를 하나로 합칠 때 사용한다.
- `Finisher` : 컬렉터가 최종 결과를 변환할 때 사용한다.
- `Characteristics` : 컬렉터의 동작 방식을 설명한다.
- `Characteristics`는 `IDENTITY_FINISH`, `CONCURRENT`, `UNORDERED`가 있다.

```java
// Collector 인터페이스 예제
public interface Collector<T, A, R> {
    Supplier<A> supplier();
    BiConsumer<A, T> accumulator();
    BinaryOperator<A> combiner();
    Function<A, R> finisher();
    Set<Characteristics> characteristics();
}
```

#### 1. `supplier`
- 새로운 결과를 갖는 컨테이너를 만든다.
- `Supplier`는 빈 결과로 이루어진 `Supplier`를 반환

```java
// supplier 예제
public class Supplier {
   public Supplier<List<T>> supplier() {
	   return () -> new ArrayList<T> ();
        // return ArrayList::new; // functional interface
   }
}
```

<br />

#### 2. `accumulator`
- 결과 컨테이너에 요소를 추가한다.
- `accumulator`는 `reducing` 연산 수행하는 함수 반환 

```java
// accumulator 예제
public class Accumulator {
   public BiConsumer<List<T>, T> accumulator() {
       return (list, item) -> list.add(item);
        // return List::add; // functional interface
   }
}
```

<br />

#### 3. `combiner`
- 두 결과 컨테이너 병합
- `combiner`는 두 결과를 하나로 합치는 함수 반환

```java
// combiner 예제
public class Combiner {
   public BinaryOperator<List<T>> combiner() {
       return (list1, list2) -> {
           list1.addAll(list2);
           return list1;
       };
   }
}
```

<br />

#### 4. `finisher`
- 최종 변환값을 결과 컨테이너로 적용
- `finisher`는 결과를 최종 형태로 변환하는 함수 반환

```java
// finisher 예제
public class Finisher {
   public Function<List<T>, List<T>> finisher() {
       return Function.identity();
   }
}
```

<br />

#### 5. `characteristics`
- 컬렉터의 연산을 정의하는 Characteristics 형식 불변 집합 반환
- `characteristics`는 `IDENTITY_FINISH`, `CONCURRENT`, `UNORDERED`가 있다.

```java
// characteristics 예제
public class Characteristics {
   public Set<Characteristics> characteristics() {
       return Collections.unmodifiableSet(EnumSet.of(IDENTITY_FINISH, CONCURRENT));
   }
}
```

<br />

#### 🔵 커스텀 Collector 사용하는 경우
1. 스트림의 요소를 수집하여 새로운 데이터 구조를 생성해야 하는 경우
2. 병렬 스트림에서 수집한 요소를 동기화해야 하는 경우
3. 스트림의 요소를 그룹화하거나 분할해야 하는 경우
4. 기본 제공 Collector로는 수행할 수 없는 특정한 작업을 수행해야 하는 경우

<br />

