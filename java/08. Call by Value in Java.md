# Call by Value in Java

## 1. Call by Value & Call by Reference

- Call by Value : 메서드 호출 시 매개변수로 값을 복사하여 전달
- Call by Reference : 메서드 호출 시 매개변수로 참조값을 전달

## 2. JAVA는 Call by Value 이다.

1. JAVA는 모든 매개변수를 기본형으로 간주한다.
2. 따라서 JAVA는 모든 매개변수를 Call by Value로 처리한다.
    - `primitive type`의 경우 `stack`에 실제 값이 있으며 이를 복사해서 넘겨준다.
    - `reference type`의 경우 `heap`에 실제 값이 있고 `stack`이 이를 가리킨다.
        - 이를 메서드에 던져주게 되면 해당 `stack` 값을 복사해서 전달해 주기 때문에 `call by value`

## 3. 확인 예제

### 3-1. Set 타입의 매개변수를 받았을 때

```java

@Data
public class CarCheck {
	private String name;

	public CarCheck(String name) {
		this.name = name;
	}

	public void addName(Set<String> set) {
		set.add(this.name);
	}
}
```

- 매개변수로 받은 set에 추가될 것이다.

### 3-2. Set 타입의 매개변수를 받고 초기화 했을 때

```java

@Data
public class CarCheck {
	private String name;

	public CarCheck(String name) {
		this.name = name;
	}

	public void addName(Set<String> set) {
		set = new HashSet<>();
		set.add(this.name);
	}
}
```

- 매개변수로 받은 set이 초기화 되는 것처럼 보이겠지만 초기화 되지 않는다.
- 새로 생성한 HashSet의 주소를 set이 다시 받게 될 것이고 add 동작이 이루어진 후 끝나게 된다.
- 매개변수로 받은 set은 전혀 영향 없다.

## 4. C의 Call by Reference와 어떤 차이가 있을까 ?

1. C의 Call by Reference는 메모리 주소를 전달하는 것이다.
    - C의 Call by Reference는 메모리 주소를 전달하기 때문에 메모리 주소를 변경할 수 있다.
2. JAVA의 Call by Value는 메모리 주소를 복사해서 전달하는 것이다.
    - JAVA의 Call by Value는 메모리 주소를 복사해서 전달하기 때문에 메모리 주소를 변경할 수 없다.

```markdown
- C의 경우 레퍼런스 포인트 자체가 전달된다.
- 포인터 자체가 넘어가는 것을 call by reference 라고 하며, C의 경우 java와 다르게 heap, stack 간접으로 바라보는 것이 아니고 직접 그 주소의 실제 값을 가리킨다.
- 이 때문에 실제로 함수포인터를 넘겨주고 이를 바꾸게 되면 자바와 다르게 실제로 변경된다.
```






