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
