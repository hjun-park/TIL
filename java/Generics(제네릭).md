# Generics
- 제네릭은 클래스 내부에서 사용할 데이터 타입을 외부에서 지정하는 기법이다.

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

