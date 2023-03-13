- [JVM (Java Virtual Machine)](#jvm--java-virtual-machine-)
  - [왜 필요한가?](#-------)
   * [1. JVM 동작방식](#1-jvm-----)
   * [2. JVM 구조](#2-jvm---)
      + [2-1. 클래스 로더 (Class Loader)](#2-1---------class-loader-)
      + [2-2. 실행 엔진 (Execution Engine)](#2-2--------execution-engine-)
      + [2-3. 가비지 컬렉터 (Garbage Collector)](#2-3----------garbage-collector-)
      + [2-4. 런타임 데이터 영역 (Runtime Data Area)](#2-4-------------runtime-data-area-)
         - [1) 메서드 영역 (Method Area) == `Class Area, Static Area`](#1----------method-area------class-area--static-area-)
         - [2) 힙 영역 (Heap Area)](#2--------heap-area-)
         - [3) 스택 영역 (Stack Area)](#3---------stack-area-)
         - [4) PC (Program Counter) 레지스터](#4--pc--program-counter------)
         - [5) Native Method Stack](#5--native-method-stack)
      + [2-5. JNI(Java Native Interface)](#2-5-jni-java-native-interface-)
      + [2-6. Native Method Library](#2-6-native-method-library)
   * [3. JVM 구조 정리](#3-jvm------)
   * [4. [그림으로 보는 Java 메모리 영역]](#4----------java------)

- [자바의 신](http://www.yes24.com/Product/Goods/42643850), [Inpa Dev](https://inpa.tistory.com/) 를 참고하였습니다.

# JVM (Java Virtual Machine)
- java를 실행하는 가상머신


<br /> 

#### 왜 필요한가? 
1. C에서는 OS나 CPU구조에 맞추어진 컴파일러로 컴파일 된다. (OS에 종속적)
2. 반면 JAVA에서는 JVM 덕분에 OS에 독립적이며 이식성이 좋다. (JVM만 있으면 어디든 JAVA 실행가능)
3. 결론적으로, 모든 플랫폼에서 제약 없이 동작하기 위해 필요하다.

<br />

## 1. JVM 동작방식
![image](https://user-images.githubusercontent.com/70880695/224580455-989661d8-0cdd-40ea-86ca-1eecda68731a.png)
> 출처 : https://steady-snail.tistory.com/67

1. JAVA 실행 시 JVM은 OS로부터 메모리 할당 받는다.
2. 자바 컴파일러(`javac`)가 소스코드(`.java`) ->  바이트코드(`.class`)로 컴파일 한다.
3. `Class Loader`에 의해 필요한 클래스는 Loading 하고 Linking 하여 `Runtime Data Area`에 올린다.
4. `Runtime Data Area`에 올라간(로딩) 바이트 코드는 `Execution Engine`에 의해 해석된다.
5. `Execution Engine`에 의해 `Garbage Collector`가 작동되고 `Thread` 동기화 이루어진다.

<br />

## 2. JVM 구조
![image](https://user-images.githubusercontent.com/70880695/224581063-9595a34b-d719-49f0-bcf3-2777a25c9155.png)
> 출처 : https://gyoogle.dev/blog/computer-language/Java/Java%20Virtual%20Machine.html

<br />

### 2-1. 클래스 로더 (Class Loader)
1. Load된 `.class` 바이트코드를 `Runtime Data Area` JVM 메모리 영역으로 배치 
   - 모든 클래스를 한 번에 올리는 것이 아닌, 애플리케이션에서 필요로 하는 경우만 동적으로 올린다.
2. 로딩 순서 `Loading -> Linking -> Initialization`
   - `Loading` : JVM 메모리에 올리는 작업
   - `Linking` : 클래스가 필요로하는 메모리를 할당하는 등 클래스파일 사용하기 위한 검증과정
   - `Initialization` : 클래스 변수들을 초기화 (static 키워드 붙은 것들을 지정한 값으로 초기화)

<br />

### 2-2. 실행 엔진 (Execution Engine)
1. 바이트코드를 명령어 단위로 읽어서 실행
2. `인터프리터와`, `JIT 컴파일러` 혼합하여 바이트코드 실행
   - `인터프리터` : 바이트코드 하나씩 읽어서 바로 실행 (똑같은 메서드도 여러 번 호출되면 매번 해석하고 읽어서 느림)
   - `JIT 컴파일러` : 인터프리터 단점보완, 반복되는 코드는 `바이트코드` -> `Native Code` 변경하여 캐싱해둔 후 `Native Code`로 실행
     - 모든 바이트코드를 Native Code로 변환하는 것이 아닌 인터프리터 사용 중 일정 기준 넘어가면 JIT 컴파일러 사용

<br />

### 2-3. 가비지 컬렉터 (Garbage Collector)
1. Heap 메모리에서 안 쓰는 메모리 자동으로 회수

<br />

### 2-4. 런타임 데이터 영역 (Runtime Data Area)
- 런타임 데이터 영역 = JVM 메모리 영역

![image](https://user-images.githubusercontent.com/70880695/224586949-13b54008-ffaf-4f93-b935-bcda0549a5bf.png)
> 출처 : [Inpa Dev](https://inpa.tistory.com/entry/JAVA-%E2%98%95-JVM-%EB%82%B4%EB%B6%80-%EA%B5%AC%EC%A1%B0-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%98%81%EC%97%AD-%EC%8B%AC%ED%99%94%ED%8E%B8#%EC%9E%90%EB%B0%94_%EA%B0%80%EC%83%81_%EB%A8%B8%EC%8B%A0jvm%EC%9D%98_%EB%8F%99%EC%9E%91_%EB%B0%A9%EC%8B%9D)

- `Thread 공유 O`: `Method Area` `Heap Area` 
- `Thread 공유 X`: `(JVM) Stack Area` `PC Register` `Native Method Stack`

<br />

#### 1) 메서드 영역 (Method Area) == `Class Area, Static Area`
1. JVM 시작 시 생성, 종료 시까지 저장 (Thread 공유)
2. `정적 필드`, `클래스 구조`를 가지고 있는 영역
3. `Runtime Constant Pool` : 
   - 각 클래스마다 `constant pool`을 가짐 : 클래스 생성 시 참조해야할 정보를 상수로 갖고 있음
   - JVM은 `constant pool` 통해 메서드/필드의 실제 메모리 주소를 찾아 참조한다.

<br />

#### 2) 힙 영역 (Heap Area)
1. 런타임 상 동적으로 할당하여 사용하는 영역 (Thread 공유)
2. 아래의 내용을 가짐
   - new 연산자로 생성되는 클래스
   - 인스턴스 변수 (static 키워드 없는 class 필드)
   - Reference Type 변수
3. `Heap`에는 실제 데이터가 있으며 Heap의 참조 주소는 `Stack`이 갖고 있다.
   - `Stack`에 있는 주소를 힙 참조 주소를 통해 실제 Heap 영역 데이터 접근이 가능하다.

![image](https://user-images.githubusercontent.com/70880695/224588117-1774b7e9-8825-470f-9717-12a767a19303.png)
1. Heap은 `Eden`, `Survivor`, `Old`, `Permanent` 영역으로 나뉜다.
  - `Young` : `Eden`과 `Survivor`이 있으며 `Eden -> Survivor 1 -> Survivor 2` 순으로 이동하며 객체가 채워진다.
  - `Old` : `Young`에서 마지막까지 살아남은 객체가 이동

<br />

#### 3) 스택 영역 (Stack Area)
1. Primitive 기본 자료형 사용 시 저장하는 공간 (Thread 공유 X)
   - Reference Type도 Stack에 담기는데 Primitive와는 다르게 실제 값이 아닌 Heap 참조 주소가 담기게 된다.
2. Method 호출 시마다 `Stack Frame`이 생성되며 여기에 `매개변수/지역변수/return 값`을 담는다.

<br />

#### 4) PC (Program Counter) 레지스터
1. JVM에서 현재 수행 중인 명령어 주소 저장하는 공간 

<br />

#### 5) Native Method Stack
1. 바이트코드가 아닌 실제 실행할 수 있는 기계어로 작성된 프로그램 실행시키는 영역
2. 즉, JIT 컴파일러에 의해 변환된 Native Code(C, C++, Assembly)도 여기에 실행된다.

<br />

### 2-5. JNI(Java Native Interface)
- JVM이 Native Method를 적재하고 수행할 수 있도록 제공하는 프로그램

<br />

### 2-6. Native Method Library
- C, C++로 작성된 라이브러리, 헤더가 필요할 경우 JNI는 이 라이브러리를 로딩해 사용

<br />

## 3. JVM 구조 정리
![image](https://user-images.githubusercontent.com/70880695/224588784-cb932a46-70a3-4fee-8fb6-5160dd52dda9.png)

<br />

## 4. [그림으로 보는 Java 메모리 영역 (inpa dev)](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B7%B8%EB%A6%BC%EC%9C%BC%EB%A1%9C-%EB%B3%B4%EB%8A%94-%EC%9E%90%EB%B0%94-%EC%BD%94%EB%93%9C%EC%9D%98-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%98%81%EC%97%AD%EC%8A%A4%ED%83%9D-%ED%9E%99)

