# GC (Garbage Collection)

## 1. GC란

> JVM의 Heap 영역에서 사용하지 않는 객체를 주기적으로 삭제하는 프로세스이다.

- 자바의 메모리 관리기법 중 하나
- JVM의 `Heap` 영역에 동적으로 할당한 메모리 중 필요 없는 객체를 `주기적`으로 제거하는 프로세스

#### 🔵 GC를 사용하는 이유 ?

1. 메모리 누수에 신경 안 쓰고 개발에만 집중하기 위해서
    - `C/C++`의 경우 GC가 없어서 프로그래머가 수동으로 메모리 관리해야 했다.
    - 메모리를 할당하고 이를 해제하지 않을 경우 `Memory Leak` 문제가 발생한다.
    - 따라서 GC를 사용하면 누수에 대해 관리하지 않고 개발에 집중할 수 있도록 한다.

<br />

#### 🔵 GC의 단점

1. 언제 메모리가 해제되는지 알 수 없다
2. GC 동작 중에는 다른 동작이 멈춘다. (오버헤드)
    - 이를 STW(`Stop The World`) 라고 한다.

<br />

#### 🔵 STW(`Stop The World`)란 ?

1. GC 수행하기 위해 JVM이 실행을 멈추는 현상
    - GC 동작 중에는 GC 관련 Thread 외 모든 동작이 멈춤
    - 때문에 추후 GC 알고리즘이 출현하였다.

<br />

#### 🔵 Java에서는 어떻게 GC 도입이 가능했을까 ?

> 객체는 대부분 일회성이며 메모리에 오래 남아있는 경우는 드물기 때문이다.

1. 대부분의 객체는 금방 `Unrechable` 상태가 된다.
   ```java
   for (int i = 0; i < 10000; i++) {
     NewObject obj = new NewObject();  
     obj.doSomething();
   }
   ```
    - 1000번 돌면서 생성된 객체는 loop를 나오면 `Unrechable`이 된다.

<br />

2. 오래된 객체에서 새로운 객체로의 참조는 아주 적다.
   ```java
   Model model = new Model("value");
   doSomething(model);
   
   // 더이상 model 을 사용하지 않음
   ```
    - 주로 JAVA에서는 객체로 POJO (Plain Old Java Object) 사용
    - 보통 위와 같이 객체를 생성하고 메서드나 클래스로 전달한 이후 더 이상 사용하지 않는다.
        - 물론 다시 재사용할 수도 있겠지만 대부분은 그렇지 않다.

<br />

## 2. GC 대상

> `Heap`에는 데이터가 있으나 어디서든 이 데이터를 참조하지 않는 객체

- JVM 메모리는 `Heap`에서 실제 데이터가 생성
    - 주로 `Stack` `Method` 영역에서 `Heap`을 참조하는 형식 (`Reachable`)

<br />

#### 🔵 `Reachable` & `Unrechable`

1. `Rechable` : 객체가 참조되고 있는 상태
2. `Unrechable` : 객체가 참조되지 않고 있는 상태
    - `Heap`에는 데이터가 있으나 어디서든 이 데이터를 참조하지 않는 객체가 GC의 대상

<br />

## 3. GC 청소방식 (`Mark and Sweep`)

- GC가 동작하는 아주 기본적인 과정

![image](https://user-images.githubusercontent.com/70880695/226112060-fe19ccc2-3e5d-4972-b87f-aec00d892476.png)
> 출처 : [J-log](https://mirinae312.github.io/develop/2018/06/04/jvm_gc.html)

1. 식별(`Mark`): `Root Space`로부터 연결된 객체를 찾아내서 어떤 객체를 참조하는지 마킹
2. 제거(`Sweep`): 참조하고 있지 않은 `Unrechable` 객체를 `Heap`에서 제거
3. 압축(`Compact`): `Sweep` 이후 분산된 객체들을 `Heap`의 시작 주소로 모음
    - 이 과정은 GC에 따라 하지 않을 수 있다.

<br />

#### 🔵 Root Space란

> Heap 메모리 영역을 참조하는 `Method` `Stack` `Native Method stack` `Static 변수`

<br />

#### 🔵 Native Method Stack ?

- Java 7 까지는 Heap에 `Permanent` 영역이 존재
- Java 8 부터는 `Native Method Stack`으로 이동되고 `Metaspace` 영역으로 변경

<br />

## 4. GC 동작과정

> 이미지 출처 : [10분 테코톡 - 엘리의 GC](https://www.youtube.com/watch?v=Fe3TVCEJhzo&list=WL&index=4)

### 4-1. Minor GC 동작과정

- Minor GC는 `Young` 영역에서 발생
- new 객체가 위치하는 `Eden`이 꽉차면 발생
- `Young` 영역은 `Old` 영역에 비해 상대적으로 작기 때문에 GC가 빠름


1. 처음 생성된 객체는 `Young` 영역의 `Eden`에 위치한다.
   ![image](https://user-images.githubusercontent.com/70880695/226113059-02a156fa-b4a9-4ee6-8495-1e1d18317fea.png)


2. `Eden`이 꽉 차게 되면 `Minor GC` 실행
   ![image](https://user-images.githubusercontent.com/70880695/226113200-62c81f85-1d7a-469d-a2d4-ea4d0bc8fe8c.png)


3. `Mark` 동작: 통해 `Rechable` 객체 탐색
   ![image](https://user-images.githubusercontent.com/70880695/226113091-975d0361-6f7f-4c63-9ffb-76e7ca3babaf.png)


4. `Eden` 영역에서 살아남은 객체는 `Survivor 0`로 이동
    - `Survivor 0`이 될 수도, 1이 될 수도 있다.
      ![image](https://user-images.githubusercontent.com/70880695/226113112-662da9d2-09a5-441c-8c93-674e36f4081b.png)


5. `Sweep` 동작: `Eden` 영역에서 사용하지 않는 객체(`Unrechable`)는 메모리 해제
   ![image](https://user-images.githubusercontent.com/70880695/226113157-9cf5a5bb-e9b4-4689-984d-b95000f03d34.png)


6. 살아남은 모든 객체는 `age 1` 증가
   ![image](https://user-images.githubusercontent.com/70880695/226113171-439b8fdd-e764-4fef-8026-93fe5211efa5.png)


7. 또 다시 `Eden`이 꽉 차면 `Minor GC` 실행하여 `Mark` 동작 진행
   ![image](https://user-images.githubusercontent.com/70880695/226113226-6712916f-d8a9-4e99-9778-68900912dc4d.png)


8. `Marking`한 객체들을 `Survivor 1`로 이동
    - (4) 과정에서 사용한 `Survivor` 영역이 아닌 다른 `Survivor` 영역으로 이동
      ![image](https://user-images.githubusercontent.com/70880695/226113248-011e642b-4da9-4837-96e2-389ad7757cd5.png)

9. 다시 `Sweep` 동작 진행
   ![image](https://user-images.githubusercontent.com/70880695/226113318-84fe1b22-f310-4ad5-b0c1-ce66be2d1578.png)

10. 다시 살아남은 모든 객체는 `age 1` 증가
    ![image](https://user-images.githubusercontent.com/70880695/226113305-8ef1d6f9-379c-4454-8b24-6af2821fdb0e.png)


11. 계속 반복

<br />

### 4-2. Major GC 동작과정

- Major GC는 `Old` 영역에서 발생
- `Old` 영역이 꽉 차면 발생
- `Old` 영역은 `Young` 영역에 비해 상대적으로 크기 때문에 GC가 느림


1. `Promotion`: `Survivor` 영역에 위치한 객체의 age가 임계치에 도달하는 경우 `Old` 영역으로 이동
   ![image](https://user-images.githubusercontent.com/70880695/226113373-2fb56225-fbed-4797-9ecc-c92879ba1591.png)
   ![image](https://user-images.githubusercontent.com/70880695/226113397-cb06f566-bf67-4f66-81cc-fad7b70f7ca1.png)


2. (1)의 과정이 반복되어 `Old`가 꽉차면 `Major GC` 발생
   ![image](https://user-images.githubusercontent.com/70880695/226113420-970bbe2f-2e20-423c-9cd1-f76c36d55950.png)

<br />

#### 🔵 객체의 age 값이란?

- `Survivor` 영역에서 객체가 살아남은 횟수 의미, `Object Header`에 기록
- 임계값에 다다르면 `Old` 영역으로 이동

<br />

#### 🔵 Survivor 영역의 제약 조건

1. `Survivor` 두 영역 중 하나는 반드시 사용되어야 하며, 다른 쪽은 비어 있어야 한다.
2. `Survivor` 두 영역이 모두 비어 있거나 모두 데이터가 존재한다면 정상적인 상황이 아니다.

<br />

## 5. GC 알고리즘

<div style="text-align: center;">
   <img src="https://user-images.githubusercontent.com/70880695/226104669-b4dc21a3-0920-49ff-b684-a6a6e35ef248.png" width="70%" height="70%">
</div>

> 출처 : [ionutbalosin](https://ionutbalosin.com/2019/12/jvm-garbage-collectors-benchmarks-report-19-12/)

<br />

### 5-1. Serial GC

1. GC 알고리즘
    - `Minor GC`: `Mark-Sweep`
    - `Major GC`: `Mark-Sweep-Compact`
2. Core가 1개의 경우 사용할 수 있도록 제공되는 단순한 GC

- 실무에서 사용하지는 않는다.

<br />

### 5-2. Parallel GC

![image](https://user-images.githubusercontent.com/70880695/226112129-30b74bcd-d772-457e-ad0f-523e495f63fa.png)
> 출처 : [J-log](https://mirinae312.github.io/develop/2018/06/04/jvm_gc.html)

1. GC 알고리즘
    - `Minor GC`: `Serial GC`와 동일
    - `Major GC`: `Serial GC`와 동일하지만 멀티 스레드로 수행 (`Old` 영역은 단일 스레드)

<br />

### 5-3. Parallel GC

1. GC 알고리즘
    - `Minor GC`: `Serial GC`와 동일
    - `Major GC`: `Serial GC`와 동일하지만 멀티 스레드로 수행 (`Old` 영역은 단일 스레드)

<br />

### 5-4. Parallel Old GC

1. GC 알고리즘
    - `Minor GC`: `Mark-Summary-Compact` 방식 (멀티 스레드)
    - `Major GC`: `Mark-Summary-Compact` 방식 (멀티 스레드)

<br />

### 5-5. CMS GC (Concurrent Mark Sweep)

1. `Stop the World` 시간을 줄이기 위한 GC
2. 메모리 파편화 문제와 CPU 사용량이 높음
    - `Java 9`부터 deprecated `Java 14`부터는 사용 중지

<br />

### 5-6. G1 GC (Garbage First)

![image](https://user-images.githubusercontent.com/70880695/226111195-c3e231a8-fc18-4e4a-8cc9-14a57ae880e5.png)
> 출처 : [J-log](https://mirinae312.github.io/develop/2018/06/04/jvm_gc.html)

1. `Java 7`부터 `CMS GC`를 대체하기 위해 나온 GC
    - `Java 9+` Default GC로 지정
2. 4GB 이상의 힙 메모리 환경에 권장
3. `Region` 개념 새로 도입
    - 기존 GC는 Heap 영역을 `Young` `Old` 으로 나누었음
    - 전체 Heap을 `Region` 영역으로 체스판처럼 분할 후 `Eden` `Survivor` `Old` 등 역할을 고정이 아닌 동적으로 부여

<br />

#### 🔵 G1 GC의 Region 동작

1. 하나하나 메모리를 탐색해 객체를 제거하는 방식이 아니다.
2. 메모리가 많이 차 있는 `region`을 인식하는 기능으로 메모리가 많이 차 있는 영역을 우선적으로 GC한다.
    - 즉, `Heap` 전체 탐색이 아닌 `Region` 단위로 탐색하고 `Region` 단위로 GC 진행한다.
3. G1 GC는 효율적이라고 생각되는 위치로 객체를 재할당 시킨다.
    - 이전 GC는 `Young` Minor GC가 일어날 때 `Eden`에 살아남은 객체는 `Survivor`로 이동
    - G1 GC는 `Survivor 0` 영역 객체가 `Eden`으로 가는게 더 효율적이라 판단되면 `Eden`으로 이동시킨다.

<br />

#### 🔵 G1 GC의 Region 동작 과정

![image](https://user-images.githubusercontent.com/70880695/226112852-33500bd3-9e20-4f52-9a47-24fb667cb25c.png)
> 출처 : [J-log](https://mirinae312.github.io/develop/2018/06/04/jvm_gc.html)

- `Old GC` 즉, Full GC 수행 시 동작과정은 아래와 같다.

1. `Initial Mark`: `Old Region`에 객체들이 참조하는 `Survivor Region` 찾는다. (STW 발생)
2. `Root Region Scan`: (1) 과정에서 찾은 `Survivor Region` 대상으로 GC 대상을 스캔한다.
3. `Concurrent Mark`: 모든 Heap의 `Region`을 스캔, GC 대상 객체가 발견되지 않은 `Region`은 마킹하지 않음
4. `Remark`: STW 발생, 최종적으로 GC에서 제외될 객체 식별
5. `Cleanup`
    - STW 발생 후 `Rechable`한 객체가 적은 `Region`에 대해 객체 제거 수행
    - 이후 비워진 `Region`은 FreeList에 추가, 재사용할 수 있도록 한다.
6. `Copy`
    - GC 대상 Region 이지만 `Cleanup`에서 완전히 비워지지 않은 `Region` 대상
    - 새롭게 비워진 `Region`에 복사하여 압축(Compaction) 작업 수행

### 5-7. Shenandoah GC

![image](https://user-images.githubusercontent.com/70880695/226111650-4580cfe8-eb20-4271-b135-e45f03fb108c.png)

1. `Java 12`에 RedHat에서 개발한 GC
2. `CMS GC`의 메모리 단편화, `G1 GC`의 pause 이슈 해결
3. 강력한 동시성 동작과 가벼운 GC로 `Heap` 사이즈에 상관 없이 일정한 `pause` 시간 소요

<br />

### 5-8. ZGC (Z Garbage Collector)

![image](https://user-images.githubusercontent.com/70880695/226111829-8d97f490-b542-4b83-920f-cac27d9ae79f.png)

1. `Java 15`에 나와 다량의 메모리를 낮은 지연으로 처리할 수 있게 설계된 GC
2. `G1 GC`의 `Region`처럼 ZGC는 `Zpage` 영역 사용
    - `Region`: 크기 고정
    - `Zpage` : 객체 크기에 따라 2MB 배수로 동적으로 할당
3. Heap 크기가 증가해도 `Stop the World` 시간이 10ms를 넘지 않음

### 출처

1. [Inpa Dev](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC#g1_gc_garbage_first)
2. [10분 테코톡 - 엘리의 GC](https://www.youtube.com/watch?v=Fe3TVCEJhzo&list=WL&index=4)
3. [J-log : Java 의 GC는 어떻게 동작하나?](https://mirinae312.github.io/develop/2018/06/04/jvm_gc.html)
4. [perfectacle : (JVM) Garbage Collection Advanced](https://perfectacle.github.io/2019/05/11/jvm-gc-advanced/#G1-GC-Garbage-First-GC)
