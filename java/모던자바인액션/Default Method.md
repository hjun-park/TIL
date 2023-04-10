# Default Method

#### 🔵 Default Method란?

- `default` 키워드를 사용하면 `인터페이스`에 `메서드`를 추가할 수 있다.
-

#### 🔵 인터페이스에 기본 구현된 메서드를 제공하지 않는다면 어떤 문제가 발생할까 ?

- `Collections`의 `List` 인터페이스에 `sort` 같은 메서드가 추가되면 `List`를 구현한 모든 클래스에 `sort` 메서드를 추가해야 한다.
    - `List` 인터페이스를 상속한 모든 클래스를 고쳐야 한다면 굉장한 비용이 발생한다.
- 이를 `Default Method`를 통해 해결할 수 있다.

<br />

#### 🔵 (인터페이스 기본 구현 메서드) Default Method는 어떻게 만들까?

1) 인터페이스 내부에 `static method` 사용
2) 인터페이스 기본 구현을 제공할 수 있도록 `default method` 사용

<br />

#### 🔵 Default Method를 사용하면 결국 추상 클래스 아닌가 ?

1) `추상 클래스`는 1개만 상속 가능하지만, `인터페이스`는 여러 개 구현 가능
2) `추상 클래스`는 `인스턴스 필드`로 공통의 상태를 가질 수 있지만, `인터페이스`는 `인스턴스 변수`를 가질 수 없다.

<br />

#### 🔵 Default Method 예시

```java
public interface Formula {
    double calculate(int a);

    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}
```

- `Formula` 인터페이스에 `calculate` 추상 메서드와 `sqrt` 기본 메서드가 있다.

<br />

## 1. Default Method 활용 패턴

### 1-1. 선택형 메서드

- 이전에는 인터페이스 구현 클래스에서 메서드의 내용이 비어 있는 경우가 있었다.
    - 구현하고 싶지 않아도 일단 구현해야 했고, 불필요한 코드가 생겼다.
- 하지만 `default method`를 사용하면 인터페이스에 메서드를 추가할 수 있으므로, 구현 클래스에서 메서드를 구현하지 않아도 된다.

```java
public interface Formula {
    double calculate(int a);

    // Optional Method 
    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}

public class FormulaImpl implements Formula {
    @Override
    public double calculate(int a) {
        return sqrt(a * 100);
    }
}
```

- `Formula` 인터페이스에 `sqrt` 메서드가 추가되었지만, `FormulaImpl` 클래스에서는 `sqrt` 메서드를 구현하지 않아도 된다.

<br />

### 1-2. 동작 다중 상속

- `인터페이스`는 `다중 상속`이 가능하다.
- `인터페이스`를 구현한 클래스는 `인터페이스`를 구현한 모든 인터페이스의 `default method`를 상속받는다.

```java
public interface Formula {
    double calculate(int a);

    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}

public interface Formula2 {
    double calculate2(int a);

    default double sqrt2(int a) {
        return Math.sqrt(a);
    }
}

public class FormulaImpl implements Formula, Formula2 {
    @Override
    public double calculate(int a) {
        return sqrt(a * 100);
    }

    @Override
    public double calculate2(int a) {
        return sqrt2(a * 100);
    }
}
```

- `FormulaImpl` 클래스는 `Formula`와 `Formula2` 인터페이스를 구현했다.
- `Formula`와 `Formula2` 인터페이스에 `default method`가 추가되었지만, `FormulaImpl` 클래스에서는 `default method`를 구현하지 않아도 된다.

<br />

#### 🔵 Default Method의 우선순위 (3가지 우선순위)

- 다중 구현 시에 `같은 시그니처`를 갖는 `디폴트 메서드`를 상속받는 상황이 생긴다.

<br />

1. `클래스가 항상 이긴다` : 클래스나 슈퍼클래스에서 `정의한 메서드`는 `디폴트 메서드`보다 우선권 가진다.
2. `서브인터페이스가 이긴다` : 1번 규칙 외의 상황에는 `default method를 정의하는` `서브 인터페이스`가 이긴다.
    - 즉, B가 A를 상속받을 때는 `B(서브)에서 구현한 메서드`가 `A(슈퍼)의 디폴트 메서드`를 덮어쓴다.
3. `명시적으로 default method를 오버라이드` : 여러 인터페이스를 상속받는 클래스가 명시적으로 `default Method` 오버라이딩 + 호출

<br />

**예제**

```java
public interface A {
    default void hello() {
        System.out.println("Hello from A");
    }
}

public interface B extends A {
    default void hello() {
        System.out.println("Hello from B");
    }
}

// 1) `D`는 클래스지만 `hello`를 오버라이드 한 것이 아닌 단순히 구현해서 `A`의 `default method`를 상속받았다.
public class D implements A { }

// 2) `C`는 `D`를 상속받고, `B`와 `A`를 구현했다.
public class C extends D implements B, A {
    public static void main(String[] args) {
        new C().hello();
    }
}
```
1) 원래는 첫 번째 원칙에 맞게 `D` 클래스의 `hello` 메서드가 호출되어야 했다.
   - 하지만 `D` 클래스는 `A` 인터페이스를 구현했지만, `A` 인터페이스의 `default method`를 오버라이드 하지 않았다.
   - 그렇기 때문에 첫 번째 원칙이 적용되지 않는다.
2) 다음으로 볼 수 있는 것들은 `A`와 `B` 클래스이다.
   - 두 번째 원칙에 따라 `서브인터페이스`인 `B`의 `hello` 메서드가 호출된다.
3) 따라서 `Hello from B` 가 출력된다.

<br />

#### 🔵 그럼에도 발생하는 충돌은 어떻게 해결하는가 ?
- 개발자가 명시적으로 `method`를 `Override`하고 호출해야 한다.

```java
public class C implements B, A {
    @Override
    public void hello() {
        // super를 사용하여 명시적으로 메서드 선택
        B.super.hello();
    }
}
```
  

