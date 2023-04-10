> ### 출처 : [모던 자바 인 액션](http://www.yes24.com/Product/Goods/77125987)

# Lambda(람다)

- 람다란, 메서드로 전달할 수 있는 익명 함수를 단순화한 것이다.
- 함수를 파라미터로 넘겨서 코드를 줄일 수 있다.

#### 🔵 람다의 특징

1. `익명`: 이름이 없다.
2. `함수` : 특정 클래스에 종속하지 않지만 파라미터, return 값, body가 있으므로 함수라고 부른다.
3. `전달` : 람다식을 인수로 전달하거나 변수로 저장할 수 있다.
4. `간결성` : 익명 클래스처럼 이러저러한 코드 구현할 필요 없다.

<br />

#### 🔵 람다의 장점

1. 코드 라인 수가 줄어듦
2. 병렬 프로그래밍 가능
    - `iteration` 방식은 대상을 일일이 루프에서 지정함
    - `함수형 프로그래밍`에서는 반복 대상을 사용자 코드에서 직접 지정하지 않는다.
3. 람다식으로 함수 실행문을 바로 전달할 수 있다.
    - 기존에는 java 메서드로 값이나 객체 생성하여 전달했지만
    - 람다식은 실행문 자체를 람다식으로 전달, 구현
4. 가독성이 높다.

<br />

#### 🔵 람다의 단점

1. 일회용 함수다. (재사용성 X)
2. 람다식이 길어지면 가독성이 떨어진다.

<br />

## 1. 람다의 표현

`(매개변수) -> {함수 구현부}`

```markdown
(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```

1. `(Apple a1, Apple a2)` : 파라미터 리스트
2. `->` : (화살표)람다의 파라미터 리스트와 바디 구분
3. `a1 ... a2.getWeight());`  (람다 바디)람다의 반환값에 해당하는 표현식

<br />

## 2. 함수형 인터페이스 (Functional Interface)

> 람다를 사용하기 위한 조건이 함수형 인터페이스다.

1. 함수형 인터페이스의 제약조건은 `정확히 하나의 추상 메서드만 존재해야 한다`
    - 다수의 default method, static method를 정의해도 된다
2. `Comparator<T>`, `Runnable` 이 2개가 함수형인터페이스의 예시

<br />

#### 🔵 @FunctionalInterface 예시

```java
// 가능 (추상 메서드 1개)
@FunctionalInterface
public interface StudentPredicate {
	boolean test(Student student); // 추상 메서드
}

// 가능 (추상 메서드 1개), default, static 은 개수 상관 없다.
@FunctionalInterface
public interface StudentPredicate {
	boolean test(Student student); // 추상 메서드

	default void defaultMethod() {   // default 메서드
		System.out.println("default method");
	}

	static void staticMethod() {     // static 메서드
		System.out.println("static method");
	}
}

// 불가능 (추상 메서드 2개) 
@FunctionalInterface
public interface StudentPredicate {
	boolean test(Student student); // 추상 메서드

	boolean biTest(Student student1, Student student2); // 추상 메서드
}
```

<br />

#### 🔵 @FunctionalInterface 어노테이션 ?

1. 함수형 인터페이스를 가리키는 어노테이션
2. 정확히 하나의 추상 메서가 있어야 하며 그 이상일 경우 에러 발생 가능

<br />

#### 🔵 함수형 인터페이스로 무엇을 할 수 있을까 ?

1. 람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있다.
2. 즉, 전체 표현식을 함수형 인터페이스의 인스턴스로 취급할 수 있다.

<br />

#### 🔵 그럼 람다 사용하기 위해 매번 함수형 인터페이스를 정의해야만 하는가 ?

1. 아니다. Java 8은 `Function` 패키지로 여러 가지 새로운 함수형 인터페이스를 제공한다.
    - `Predicate` `Consumer` `Function` `Supplier` `UnaryOperator` `BiConsumer` 등등
      ![image](https://user-images.githubusercontent.com/70880695/226154965-77863cc7-d075-4ffd-b450-44c9a7fb2196.png)

<br />

#### 🔵 익명 클래스와는 어떤 차이가 있을까 ?

- `익명 클래스`는 컴파일 하면 별도의 클래스로 떼어져 나온다.
- `람다`는 컴파일 해도 별도의 클래스로 뗴어져 나오지 않는다.

<br />

### 2-1 함수형 인터페이스 예시

#### 🔵 1) Runnable을 이용한 예제?

```java

@FunctionalInterface
public interface Runnable {
	// 함수 디스크립터
	public abstract void run();
}

	// 람다식으로 표현하면 아래와 같다.
	Runnable r1 = () -> System.out.println("Hello");
```

<br />

#### 🔵 2) function 패키지 :: Consumer 예제

1. `Consumer`는 기본적으로 T 타입 객체를 받고 return 없다. (`void`)

```java

public class ConsumerSample {
	public static void main(String[] args) {
		// String 이라는 객체를 받고 그저 출력한다. (return 없음)
		Consumer<String> print = (message) -> System.out.println("message = " + message);

		// accept 이용하여 출력한다.
		// message = hello nathan
		print.accept("hello nathan");
	}
}

```

<br />

#### 🔵 3) function 패키지 :: Function/BiFunction 예제

1. `Function`은 기본적으로 T 타입 객체를 받고 R 타입으로 return 한다.
2. `BiFunction`은 기본적으로 2개의 T와 U 타입 객체를 받고 R 타입으로 return 한다.

```java
public class FunctionSample {
	public static void main(String[] args) {
		// T 타입으로 message 라는 매개변수를 받는다.
		// R 타입으로 "Message :" 와 message 가 조합된 String 값이 return 된다.
		Function<String, String> function = (message) -> "Message : " + message;

		// Message : nathan
		System.out.println(function.apply("nathan"));

		// T 타입으로 String, U 타입으로  Integer로 받았다.
		// body 내용을 보면 String으로 return 한다.
		BiFunction<String, Integer, String> biFunction = (message, count) -> {
			int resultCount = count + 10;
			return "Message : " + message + ", Count : " + resultCount;
		};

		// Message : nathan, Count : 5
		System.out.println(biFunction.apply("nathan", 5));
	}
}

```

<br />

#### 🔵 4) function 패키지 :: Predicate 예제

1. T 타입을 매개변수로 받고 boolean을 return한다.

```java
public class PredicateSample {
	public static void main(String[] args) {
		// T 타입으로 Integer, boolean으로 return 
		Predicate<Integer> equalsCount = (count) -> count.equals(50);

		// true
		System.out.println(equalsCount.test(50));

		// false
		System.out.println(equalsCount.test(20));
	}
}
```

<br />

#### 🔵 5) function 패키지 :: Supplier 예제

1. 매개변수는 없고 T 타입을 return 한다.

```java
public class SupplierSample {
	public static void main(String[] args) {
		Supplier<String> getMessage = () -> "nathan";
		// nathan
		System.out.println(getMessage.get());
	}
}
```

<br />

### 2-2. 함수형 인터페이스 문제

- 컴파일이 안 되는 이유는 ?

```java
Object o=()->{System.out.println("functional interface");};
```

<br />

1. Object는 Functional Interface가 아니다.
2. `Runnable`은 `() -> void` 형식의 함수 디스크립터를 가지는데, 이로 변경한다.
   ```java
   // 1. Runnable 사용
   Runnable r = () -> { System.out.println("functional interface"); };
   
   // 2. 캐스팅 하여 사용
   Object r = (Runnable) () -> { System.out.println("functional interface"); };
   ```

<br />

## 3. 람다의 참조

### 3-1. 메서드 참조

1. 기존 메서드 구현으로 람다식 만들 수 있다.
2. 가독성이 좋아진다.

```java
// 1. 람다표현식과 메서드참조
(Apple apple)->apple.getWeight()
	Apple::getWeight

	// 2
	(str,i)->str.substring(i)
	String::substring

	// 3
	(String s)->this.isValidName(s)
	this.isValidName(s)
```

<br />

### 3-2. 메서드 참조 만들기

**1. 정적 메서드 참조**

- `Integer` 클래스의 `parseInt`는 static 클래스다.
- 이를 `Integer::parseInt` 표현이 가능하다.

**2. 다양한 형식의 인스턴스 메서드 참조**

- `String` 클래스의 `length` 메서드는 인스턴스 메서드다.
- 이를 `String::length` 표현이 가능하다.

**3. 기존 객체의 인스턴스 메서드 참조**

- `Transaction` 객체가 있고 이를 이용해 인스턴스를 생성한 `instanceTransaction`이 있다.
- `Transaction`에 `getValue` 메서드가 있다면 `instanceTransaction::getValue` 표현이 가능하다.

<br />

### 3-3. 메서드 참조 예제

#### 🔵 (1)

```java
// 람다식
ToIntFunction<String> stringToInt=
	(String s)->Integer.parseInt(s);
```

```java
// 메서드 참조
Function<String, Integer> stringToInt=Integer::parseInt;
```

<br />

#### 🔵 (2)

```java
// 람다식
BiPredicate<List<String>,String>contains=
	(list,element)->list.contains(element);
```

```java
// 메서드 참조
Function<String, Integer> stringToInt=Integer::parseInt;
```

<br />

#### 🔵 (3)

```java
// 람다식
ToIntFunction<String> stringToInt=
	(String s)->Integer.parseInt(s);
```

```java
// 메서드 참조
Function<String, Integer> stringToInt=Integer::parseInt;
```

<br />

### 출처

1. [모던 자바 인 액션](http://www.yes24.com/Product/Goods/77125987)
2. [테코톡: 스컬의 람다](https://www.youtube.com/watch?v=sS-_Xr5Q4V4)
3. [어라운드허브스튜디오 : 람다 함수 (람다식)](https://www.youtube.com/watch?v=3zd7HN-ZEUE&list=PLlTylS8uB2fA5PevGI6ARN6v-VQWmqaix&index=14)
