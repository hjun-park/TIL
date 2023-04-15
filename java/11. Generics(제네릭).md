# Generics
- 제네릭은 클래스 내부에서 사용할 데이터 타입을 외부에서 지정하는 기법이다.
- JDK 1.5 부터 추가됐다.
  - 타입 안정성을 보장하며,
  - 실행시간에 (형변환 등으로) 오버헤드가 발생하지 않도록 하기위해 도입
- 컴파일 이후 제네릭은 모두 제거된다.

### [참고 : 제네릭 컴파일 과정](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A0%9C%EB%84%A4%EB%A6%AD-%ED%83%80%EC%9E%85-%EC%86%8C%EA%B1%B0-%EC%BB%B4%ED%8C%8C%EC%9D%BC-%EA%B3%BC%EC%A0%95-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0)


## 1. 제네릭 정의
### 1-1. 제네릭 예시
```java
class Box<T> {
    T item;
    
    public void setItem(T item) {
        this.item = item;
    }
    
    public T getItem() {
        return item;
    }
}

class Main {
    public static void main(String[] args) {
        // 제네릭 타입 매개변수에 Integer 타입 지정
        Box<Integer> box = new Box<>();       
        
        // 제네릭 타입 매개변수에 String 타입 지정
        Box<String> box2 = new Box<>();
        
        // 제네릭 타입 매개변수에 AppleBox 타입 지정
        Box<AppleBox> box3 = new Box<>();
        
    }
}
```

### 1-2. 제네릭 다형성 
- 제네릭 타입은 다형성을 가진다.
- 제네릭 타입의 다형성은 타입 매개변수에 의해 결정된다.
```java
class FruitBox<T> {
    T item;
    
    public vod add(T item) {
        this.item = item;
    }
}

class Main {
    public static void main(String[] args) {
        FruitBox<Fruit> fruitBox = new FruitBox<>();
        
        // 제네릭은 다형성이 적용됨
        fruitBox.add(new Fruit());
        fruitBox.add(new Apple());
        fruitBox.add(new Grape());
    }
}
```

### 1-3. 제네릭 복수 타입 파라미터
- 제네릭은 복수의 타입 매개변수를 가질 수 있다.

```java
class Box<T, U> {
    List<T> appleBox = new ArrayList<>();
    List<U> orangeBox = new ArrayList<>();

    public void add(T apple, U orange) {
        this.appleBox.add(apple);
        this.orangeBox.add(orange);
    }
}

public class Main {
    public static void main(String[] args) {
        Box<Apple, Orange> box = new Box<>();
        box.add(new Apple(), new Orange());
        box.add(new Apple(), new Orange());
    }
}
```

### 1-4. 제네릭 타입 파라미터 네이밍
- `T`는 `Type`의 약자이다.
- `E`는 `Element`의 약자이다.
- `K`는 `Key`의 약자이다.
- `V`는 `Value`의 약자이다.
- `N`은 `Number`의 약자이다.
- `S`, `U`, `V`는 두 번째, 세 번째, 네 번째 타입을 나타낸다.
- `R`은 반환 타입을 나타낸다.

### 1-5. 제네릭을 사용하는 이유

#### 1) 컴파일 타임에 Type 체크하여 예외 방지
1. 제네릭은 java 1.5에 추가됐다. 이전에는 여러 인수를 다루기 위해 `Object` 사용했었다.
2. 하지만 `Object`는 Type 변환 시에 `ClassCastException`이 발생할 수 있다. (`Runtime` 에서 발생)
3. 때문에 제네릭을 사용하면 `Compile Time`에 타입을 체크할 수 있다.

<br />

#### 2) 불필요한 캐스팅 제거 -> 성능 향상
1. `Object`를 사용하면 꺼낼 때 `다운캐스팅` 사용 => 성능 낭비
```java
   public class Main {
    public static void main(String[] args){
        List list = new ArrayList();            // Object 타입을 사용
        list.add("hello");                      // String 타입을 넣음
        String str = (String) list.get(0);      // 다운캐스팅
   }
}
```
2. 제네릭 사용 시에 미리 타입을 지정하기 때문에 `가독성` + `번거로움 제거`
```java
public class Main {
    public static void main(String[] args){
        List<String> list = new ArrayList<>(); // String 타입을 사용
        list.add("hello");                      // String 타입을 넣음
        String str = list.get(0);               // 다운캐스팅 필요 없음
    }
}
```

### 1-6. 제네릭을 사용 시 유의할 점
#### 1) 제네릭 타입 객체는 생성 불가
- new 연산자 뒤에 T가 올 수 없다.
  - `T t = new T();` 불가

<br />

#### 2) static 멤버에 제네릭 타입 불가
- static 멤버는 객체 생성 없이 사용할 수 있기 때문에 타입을 알 수 없다.
  - `static T t;` 불가
  - `static T method(T n) { ... }` 불가


<br />

# 2. 제네릭 실습
## 2-1. 제네릭 클래스
- 제네릭 클래스는 클래스 선언문에 제네릭 타입 매개 변수가 선언된 클래스이다.

```java
class Box<T> {      // 제네릭 클래스
    T item;         // T는 타입 매개변수
    
    public void setItem(T item) {
        this.item = item;
    }
    
    public T getItem() {
        return item;
    }
}

public class Main {
    public static void main(String[] args) {
        
        // 정수형을 다루는 제네릭 클래스
        Box<Integer> box1 = new Box<>();    // 제네릭 타입 매개변수에 Integer 타입 지정
        box1.setItem(10);
        System.out.println(box1.getItem());
        
        // String을 다루는 제네릭 클래스
        Box<String> box = new Box<>();      // 제네릭 타입 매개변수에 String 타입 지정
        box.setItem("Hello");
        System.out.println(box.getItem());
    }
}
```

## 2-2. 제네릭 인터페이스
- 제네릭 인터페이스는 인터페이스 선언문에 제네릭 타입 매개 변수가 선언된 인터페이스이다.

```java
interface Box<T> {
    void setItem(T item);
    T getItem();
}

// 오버라이딩한 메서드에는 제네릭 타입에 맞춰 사용
class BoxImpl<T> implements Box<T> {
    T item;
    
    @Override
    public void setItem(T item) {
        this.item = item;
    }
    
    @Override
    public T getItem() {
        return item;
    }
}

public class Main {
    public static void main(String[] args) {
        Box<String> box = new BoxImpl<>();
        box.setItem("Hello");
        System.out.println(box.getItem());
    }
}
``` 

## 2-3. 제네릭 함수형 인터페이스
- 람다 표현식의 함수형 인터페이스에 제네릭 인터페이스가 많이 사용 된다.

```java
// 제네릭으로 타입을 받아, 해당 타입의 두 값을 더하는 인터페이스
@FunctionalInterface
interface IAdd<T> {
    public T add(T x, T y);
}

public class Main {
    public static void main(String[] args) {
        // 제네릭을 통해 람다 함수의 타입을 결정
        IAdd<Integer> o = (x, y) -> x + y; // 매개변수 x와 y 그리고 반환형 타입이 int형으로 설정된다.
        
        int result = o.add(10, 20);
        System.out.println(result); // 30
    }
}
```

## 2-4. 제네릭 메서드
- 제네릭 클래스 안에 제네릭 타입 파라미터를 사용하는 메서드는 제네릭 메서드가 아니다.
  ```java
  class FruitBox<T> {
      public T addBox(T x, T y) {
          // ...
      }
  }
  ```
- 제네릭 메서드는 `메서드의 선언부에 <T>` 가 선언된 메서드를 말한다.
- 클래스에 정의된 제네릭 타입 매개변수와는 별개로 `제네릭 메서드만의 타입 파라미터`를 사용한다.
![image](https://user-images.githubusercontent.com/70880695/230241773-1b7f0618-4493-4080-ba10-7e948e91608d.png)
> 출처 : [inpa Tistory](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A0%9C%EB%84%A4%EB%A6%ADGenerics-%EA%B0%9C%EB%85%90-%EB%AC%B8%EB%B2%95-%EC%A0%95%EB%B3%B5%ED%95%98%EA%B8%B0#recentComments)



```java
class Box<T> {
    T item;
    
    public void setItem(T item) {
        this.item = item;
    }
    
    // 제네릭 메서드가 아니다.
    public T getItem() {
        return item;
    }
    
    // 제네릭 메서드
    public static <T> Box<T> boxing(T t) {
        Box<T> box = new Box<>();
        box.setItem(t);
        return box;
    }
}

public class Main {
    public static void main(String[] args) {
        
        // 제네릭 메서드 호출 시 `메서드 왼쪽`에 제네릭 타입이 위치하지만,
        Box<Integer> box1 = Box.<Integer>boxing(10);
        System.out.println(box1.getItem());
        
        // 컴파일러가 추정할 수 있기 때문에 생략 가능하다.
        Box<Integer> box1 = Box.boxing(10);
        System.out.println(box1.getItem());
    }
}
```

# 3. 제네릭 타입 제한
- 제네릭은 타입을 지정함으로써 `타입 예외`에서 어느 정도의 안정성을 확보한다.
- 하지만 너무 자유롭다는 것이 문제점이다.

```java
// 숫자만 받아 계산하는 계산기 클래스 모듈
class Calculator<T> {
    void add(T a, T b) {}
    void min(T a, T b) {}
    void mul(T a, T b) {}
    void div(T a, T b) {}
}

public class Main {
    public static void main(String[] args) {
        // 제네릭에 아무 타입이나 모두 할당이 가능
        Calculator<Number> cal1 = new Calculator<>();
        Calculator<Object> cal2 = new Calculator<>();
        Calculator<String> cal3 = new Calculator<>();
        Calculator<Main> cal4 = new Calculator<>();
    }
}
```

## 3-1. 타입 한정 키워드 `<T extends [제한타입]>`
- `<T extends Number>` 제네릭을 이용하면 `Integer` `Double` 같이 `Number`의 하위 타입만 받도록 할 수 있다.

```java
class Calculator<T extends Number> {
    void add(T a, T b) {}
    void min(T a, T b) {}
    void mul(T a, T b) {}
    void div(T a, T b) {}
}

public class Main {
    public static void main(String[] args) {
        // 제네릭에 Number 타입만 할당이 가능
        Calculator<Integer> cal = new Calculator<>();
        Calculator<Number> cal1 = new Calculator<>();
        
        // 아래는 모두 에러
        Calculator<Object> cal2 = new Calculator<>(); // Error
        Calculator<String> cal3 = new Calculator<>(); // Error
    }
}
```

## 3-2. 인터페이스 타입 한정
- 인터페이스를 구현한 클래스들만 제네릭 타입으로 사용할 수 있도록 제한

```java
interface Readable {
}

// 인터페이스를 구현하는 클래스
public class Student implements Readable {
}

// 인터페이스를 Readable를 구현한 클래스만 제네릭 가능
public class School<T extends Readable> {
}


public class Main {
  public static void main(String[] args) {
    // 타입 파라미터에 인터페이스를 구현한 클래스만이 올수 있게 됨	
    School<Student> a = new School<Student>();
  }
}
```

## 3-3. 다중 타입 한정
- 제네릭 타입을 한정할 때 `&` 연산자를 사용하여 다중 타입 한정이 가능하다.
- java는 interface만 다중 상속이 가능하기 때문에 interface 에서만 적용 가능

```java
interface Readable {
}

interface Closeable {
}

class BoxType implements Readable, Closeable {
}

// 다중 타입 한정
class Box<T extends Readable & Closeable> {
  List<T> list = new ArrayList<>();

  public void add(T item) {
    list.add(item);
  }
}

public class Main {
  public static void main(String[] args) {
    // Readable 와 Closeable 를 동시에 구현한 클래스만이 타입 할당이 가능하다
    Box<BoxType> box = new Box<>();

    // 심지어 최상위 Object 클래스여도 할당 불가능하다
    Box<Object> box2 = new Box<>(); // ! Error
  }
}
```

# 4. 제네릭 와일드카드
- 제네릭 타입을 사용할 때 `?`를 사용하여 타입을 지정하지 않고 사용할 수 있다.
- 제네릭 와일드카드 종류
  - `?` : 모든 타입을 의미
  - `? extends [상위타입]` : 상위 타입이나 상위 타입의 하위 타입만 올 수 있다.
  - `? super [하위타입]` : 하위 타입이나 하위 타입의 상위 타입만 올 수 있다.

```java
// 제네릭 와일드카드
public static void main(String[] args) {
    List<Integer> lists = new ArrayList<>(Arrays.asList(1, 2, 3));
    print(lists); // OK
}

// Number와 그 하위 타입(Integer, Double 등) 만을 받는다
public static void print(List<? extends Number> list) {
    for (Object e : list) {
        System.out.println(e);
    }
}
```

## 출처
- [1] [Inpa : 자바 제네릭(Generics) 개념 & 문법 정복하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A0%9C%EB%84%A4%EB%A6%ADGenerics-%EA%B0%9C%EB%85%90-%EB%AC%B8%EB%B2%95-%EC%A0%95%EB%B3%B5%ED%95%98%EA%B8%B0#%EC%A0%9C%EB%84%A4%EB%A6%AD_%EB%A9%94%EC%84%9C%EB%93%9C)
