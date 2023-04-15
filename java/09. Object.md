# Object

## 1. Object 클래스

- `java.lang` 패키지에 있는 클래스, 모든 클래스의 최상위 클래스
- 11개 메서드가 있으며 이 중 `clone()` `equals` `hashCode()` `toString()`은 재정의하여 빈번히 사용한다.

<br />

## 2. Objects 클래스

- `java.util` 패키지에 있는 Object와 또다른 클래스
- 객체 비교, 해시코드 생성, null여부 체크, 객체 문자열 리턴 등의 기능을 제공한다.

## 3. Object 클래스 재정의

### 3-1. toString()

- 기본적으로 `Object`의 `toString()`은 인스턴스에 대한 이름과 주소(hashCode)를 리턴한다.
- `toString()`을 재정의하여 주소값이 아닌 객체의 고유 정보를 리턴하도록 한다.
- lombok의 `@ToString`도 있다.

```java
class Person {
	String name;
	int age;

	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}

	@Override
	public String toString() {
		return "이름 : " + name + ", 나이 : " + age;
	}
}
```

### 3-2. equals()

1. `Object`의 `equals()`는 인스턴스의 주소값을 비교한다.
    - `equals()`를 재정의하지 않으면 `==`와 같은 결과를 리턴한다.

- `equals()`를 재정의하여 주소가 아닌 인스턴스의 내용을 비교하도록 한다.

```java
class Person {
	String name;
	int age;

	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;   // 현 객체 this와 매개변수 obj가 같을 경우 true
		if (!(obj instanceof Person))
			return false;   // obj가 Person 타입이 아닐 경우 false
		Person person = (Person)obj;   // obj를 Person 타입으로 형변환
		return name.equals(person.name) && age == person.age;   // name과 age가 같을 경우 true

		return false;
	}
}
```

### 3-3. hashCode()

1. `Object`의 `hashCode()`는 객체의 주소 값을 이용해서 해싱(hashing) 기법으로 해시코드 만든 후 반환한다.
    - 즉, 서로 다른 두 객체는 같은 해시코드를 가질 수 없다.
    - 주소라기 보다는 주소값으로 만든 고유한 숫자값이다.
2. `hashCode()`를 재정의하여 객체의 필드들을 기반으로 해시코드를 리턴하도록 한다.
    - `Objects.hash()` 이용 시, 반환하는 해시코드 값을 주소값이 아닌 객체의 내용을 기반으로 만든다.
3. lombok의 `@EqualsAndHashCode`도 있다.

```java
class Person {
	String name;
	int age;

	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}

	@Override
	public int hashCode() {
		return Objects.hash(name, age);  // name과 age를 이용해서 해시코드 생성
	}
}
```

### 3-4. clone()

1. `Object`의 `clone()`은 객체의 깊은 복사 시에 사용되는 메서드
2. `Object`의 `clone()`은 기본적으로 `protected` 접근 권한이다.
    - 따라서 재정의하여 public 접근제어자로 오버라이딩 해야 한다.
3. 얕은 복사와 깊은 복사
    - 얕은 복사 : 객체의 필드 값만 복사
    - 깊은 복사 : 객체의 필드 값과 필드 값이 참조하는 객체까지 복사
4. 객체 내에 또 다른 클래스가 있다면 해당 클래스도 `Cloneable` 인터페이스를 구현해야 한다.

```java
class Person implements Cloneable {
	String name;
	int age;

	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}

	@Override
	public Person clone() throws CloneNotSupportedException {
		return (Person)super.clone();
	}
}
```

## 4. 질문

### 4-1. equals()는 왜 hashCode()와 함께 재정의해야 하는가?

1. 두 메소드를 재정의하지 않는다면 hash 값을 사용하는 `Collection Framework` 사용 시 문제 발생
    - `HashSet` `HashMap` `HashTable` 사용 시 문제 발생
2. JAVA는 기본적으로 `equals()`가 true인 두 객체의 해시코드는 반드시 같아야 한다.
    - `equals()`가 true인 두 객체가 다른 해시코드를 가질 경우, 해시테이블에서 두 객체를 구분할 수 없다.
    - 따라서 `equals()`가 true인 두 객체는 반드시 같은 해시코드를 가져야 한다.

<br />

#### 🔵 hashCode, equals 동작 순서 (hashCode 비교 -> equals 비교 -> 저장)

1. `HashSet`에 객체를 저장하려고 할 때, `hashCode()`를 호출하여 해시코드를 얻는다.
2. 해시코드를 이용하여 객체를 저장할 위치를 계산한다.
3. 해당 위치에 객체가 없으면 객체를 저장한다.
4. 해당 위치에 객체가 있으면 `equals()`를 호출하여 두 객체를 비교한다.
    - `equals()`가 true이면 동일한 객체로 판단하고 저장하지 않는다.
    - `equals()`가 false이면 다른 객체로 판단하고 저장한다.

<br />

#### 🔵 hashCode를 재정의 할 경우 고유 객체 주소 값은 어떻게 얻을까?

1. `identityHashCode()` 사용한다.
    - `System.identityHashCode()`는 객체의 고유 주소값을 리턴한다.

<br />

#### equals & hashCode를 직접 구현할 일이 없는 이유는 ?
- `@EqualsAndHashCode` 사용, `@Data` 사용
  - `@EqualsAndHashCode`는 `@Data`에 포함되어 있다.
- java 17의 경우 `record` 사용



