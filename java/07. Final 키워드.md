# Final

## 1. Final

- final 키워드는 `immutable` 하게 만들어 준다.

### 1-1. final 필드

- 필드에 `final`을 붙이면 값을 변경할 수 없는 `상수`가 된다.

```java
class SharedClass {
	public static final double PI = 3.14; // static 상수 필드 선언 (반드시 초기값 지정)
}

public class FinalFieldClass {
	final int ROWS = 10; // 인스턴스 상수 필드 선언

	void f() {
		int[] intArray = new int[ROWS]; // 상수 활용

		ROWS = 30; // 컴파일 오류 발생, final 필드 값을 변경할 수 없다. 
	}
}
```

<br /> 

### 1-2. final 메서드

- 메서드에 `final` 키워드를 붙이면 해당 메서드는 `Overriding` 할 수 없도록 만들어진다.

```java
public class SuperClass {
	protected final int finalMethod() { // final 메서드선언
		// ... 
	}
}

class SubClass extends SuperClass { // SubClass가 SuperClass 상속
	protected int finalMethod() { // 컴파일 오류, 오버라이딩 불가
		// ... 
	}
}
```

<br />

### 1-3. final 클래스

- 클래스에 `final` 키워드를 붙이면 해당 클래스는 다른 클래스가 `상속` 받을 수 없다.

```java
final class FinalClass { // final 클래스 선언
	.....
}

class SubClass extends FinalClass { // 컴파일 오류. FinalClass 상속 불가
	.....
}
```

### 출처

[1] [inpa dev : [JAVA] ☕ 객체 지향(OOP) 클래스 문법 💯 총정리](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5OOP-%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%AC%B8%EB%B2%95-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC#final_%EC%A0%9C%EC%96%B4%EC%9E%90)

