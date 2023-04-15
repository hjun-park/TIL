# 추상 클래스 vs 인터페이스
- 코드는 [자바의 신](http://www.yes24.com/Product/Goods/42643850), [Inpa Dev](https://inpa.tistory.com/) 를 참고하였습니다.

## 1. 인터페이스

### 1-1. 인터페이스 형식
```java
public interface TV {
	public static final int MAX_VOLUME = 10;    // public static final 생략 가능
	int MIN_VOLUME = 0; 
	
	public abstract void turnOn(); // public abstract 생략 가능
    void turnOff();
	void changeVolume(int volume);
	void changeChannel(int channel);
} 
```

<br /> 

### 1-2. Default method
- JAVA 1.8부터 default method 를 제공함으로써 추상 클래스처럼 method 구현부 정의 가능
- 인터페이스 구현 이후 모든 인터페이스를 구현한 구현체에게 수정없이 메서드를 주고싶을 때 사용

<br />

#### 왜 JAVA 1.8부터 interface에 default method를 제공할까
- `호환성` : JAVA 1.8에 제공되는 `stream`, `lambda` 같은 함수형 프로그래밍을 `Collection` 클래스에서 사용하기 위함

<br />

#### 주의점
1. Object를 상속받지 않으므로 `equals` `hsaCode` 재정의 필요
2. 인터페이스에서 다중 구현과 default method 사용으로 `죽음의 다이아몬드` 문제가 발생한다. 
   1. 죽음의 다이아몬드는 다중 상속의 문제점이다. 
   2. java interface 역시 다중 상속은 아니나 다중 구현에 같은 default method가 있다면 이 문제가 발생한다.
   
<br />

#### default method 예제
```java
public interface Calculator {
	int plus(int i, int j);
	
	// default로 선언함으로써 메서드 구현 가능
	default int sub(int i, int j) {
		return i - j;
    }
}

public class MyCalculator implements Calculator{
    // 추상 메서드만 구현
    @Override
    public int plus(int i, int j) { return i + j; }
}

public class Main {
	public static void main(String[] args){
		MyCalculator myCal = new MyCalculator();

		// upcasting
		Calculator cal = (Calculator) myCal;

		// call default method
		int value = cal.sub(5, 10);
		System.out.println(value); 
	}
}
```

<br />

## 2. 추상 클래스

### 2-1. 추상 클래스 형식
```java
// 추상 클래스
abstract class Pet {
    abstract public void walk(); // 추상 메소드
    abstract public void eat(); // 추상 메소드
    
    public int health; // 인스턴스 변수
    public void run() {  // 인스턴스 메서드
    	System.out.println("run");
    }
}

class Dog extends Pet {
	
	// 상속받은 메서드 구현
    public void walk() {
        System.out.println("Dog walk");
    }
    public void eat() {
    	System.out.println("Dog eat");
    }
}

public class main {
    public static void dogMain(String[] args) {
        Dog dog = new Dog();
        dog.eat();    // 구현된 추상 메서드 사용
        dog.walk();   // 구현된 추상 메서드 사용
        dog.run();    // 부모 추상 클래스 내 인스턴스 메서드 사용
    }
}
```

<br />

## 3. 추상클래스 vs 인터페이스 일반적 차이

### 3-1. 키워드의 차이
1. 인터페이스 : `implements` 사용, 목적에 맞게 기능 구현
2. 추상클래스 : `extends` 키워드 사용, 하위 클래스로 확장

<br />

### 3-2. 다중 상속(구현)
1. 인터페이스 : 클래스에 다중 구현, 인터페이스끼리 다중 상속
2. 추상클래스 : 불가능

<br />

### 3-3. 접근 제어자
1. 인터페이스 : public
2. 추상클래스 : 제한 없음

<br />

### 3-4. 사용가능 변수
1. 인터페이스 : public static final
2. 추상클래스 : 제한 없음

<br />

## 4. 추상클래스 vs 인터페이스 목적의 차이

### 4-1. 추상클래스(abstract)를 쓰는 경우

<br />

#### 상속 받는 클래스들의 중복된 멤버(필드, 메서드) 통합
- JPA의 경우 Entity에서 `INPUT DATE`, `UPDATE DATE`가 공통으로 있을텐데 이 경우 abstract class 사용

#### 계층 구조를 명확히 하고 싶을 때
- 모양에는 원, 마름모, 삼각형 // 애완동물에는 강아지, 고양이, 거북이가 있듯이 논리적으로 묶여 있는 경우 사용
- SMS 보내는 3개의 벤더사 (SKT, KT, LG U+)가 있을 때 제공하는 기능은 비슷하지만 각각 다른 구현이 필요한 경우 사용한다.

<br />

### 4-2. 인터페이스(interface)를 쓰는 경우

<br />

#### 상하 수직관계가 아닌 관련성 없는 클래스를 묶어줄 때(수평, 형제관계)
```java
abstract class Creature { }

// 명확한 상하관계 추상화는 abstract 사용
abstract class Animal extends Creature { }
abstract class Fish extends Creature { }

// 수영 가능 추상화 interface 생성
interface Swimmable {
    void swimming();
}

class Tiger extends Animal { }  // 호랑이
class Parrot extends Animal { } // 앵무새

// 상속에 구애받지 않고 필요한 동작은 인터페이스를 통해 묶음 
class People extends Animal implements Swimmable{ 
    @Override
    public void swimming() {}
}

// 마찬가지로 상속에 구애받지 않고 필요한 동작은 인터페이스를 통해 묶음
class Whale extends Fish implements Swimmable{ 
    @Override
    public void swimming() {}
}
```
- 마찬가지로 앵무새의 경우 동물 중에서도 날 수 있기 때문에 `Flyable` interface 만들어서 구현이 가능하다.

<br /> 

#### 다중 상속(구현)을 통해 설계해야 할 때
- 추상클래스는 다중 상속이 불가능하므로 이 때 사용

<br />

#### 인터페이스 다형성을 이용하여 설계할 때 
- 인터페이스를 만들어 놓은 후에 특정 클래스에 인터페이스 타입의 객체를 필드로 선언하여 사용한다.
- 런타임 상에서 상속 관계에 얽매이지 않고 구현할 수 있다는 장점이 있다.
