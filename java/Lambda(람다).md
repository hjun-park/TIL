
- [자바의 신](http://www.yes24.com/Product/Goods/77125987) 를 참고하였습니다.
 
# Lambda(람다)
- 람다란, 메서드로 전달할 수 있는 익명 함수를 단순화한 것이다.

#### 람다의 특징
1. `익명`: 이름이 없다.
2. `함수` : 특정 클래스에 종속하지 않지만 파라미터, return 값, body가 있으므로 함수라고 부른다.
3. `전달` : 람다식을 인수로 전달하거나 변수로 저장할 수 있다.
4. `간결성` : 익명 클래스처럼 이러저러한 코드 구현할 필요 없다.

<br />

## 1. 람다의 표현
```markdown
(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```
1. `(Apple a1, Apple a2)` : 파라미터 리스트
2. `->` : (화살표)람다의 파라미터 리스트와 바디 구분
3. `a1 ... a2.getWeight());`  (람다 바디)람다의 반환값에 해당하는 표현식

<br />

## 2. 어디에서 어떻게 람다를 사용할까

### 2-1. 함수형 인터페이스 (Functional Interface)
1. `함수형 인터페이스` : 정확히 하나의 추상 메서드를 지정하는 인터페이스
   - `Comparator<T>`, `Runnable` 이 2개가 함수형 인터페이스의 예

<br />

1. @FunctionalInterface 어노테이션
   - 함수형 인터페이스를 가리키는 어노테이션
   - 정확히 하나의 추상 메서가 있어야 하며 그 이상일 경우 에러 발생 가능

### 2-2. 함수형 인터페이스로 무엇을 하는지 ?
1. 람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있다.
2. 즉, 전체 표현식을 함수형 인터페이스의 인스턴스로 취급할 수 있다.

<br />

#### Runnable을 이용한 람다식 예제
- 다음과 같은 함수형 인터페이스가 있다.
```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

- 이를 람다식으로 표현하면 아래와 같이 표현 가능하다.
```java
Runnable r1 = () -> System.out.println("Hello");
```
<br />





