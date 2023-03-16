- [JVM Heap](#jvm-heap)
    * [1. Heap 메모리 영역](#1-heap-------)
        + [1-1. Young Generation](#1-1-young-generation)
            - [Eden](#eden)
            - [Survivor 1 / Survivor 2](#survivor-1---survivor-2)
        + [1-2. Old Generation](#1-2-old-generation)
    * [2. JVM 내의 Heap에서의 객체 LifeCycle](#2-jvm----heap-------lifecycle)
        + [2-1. JVM에서의 `Permanent`](#2-1-jvm-----permanent-)
            - [Java 8에 와서 제거되고 `Metaspace` 영역 추가](#java-8-----------metaspace-------)
            - [그렇다면 왜 `Metaspace` 영역으로 대체했을까 ?](#--------metaspace--------------)
            - [얼핏보면 Heap에 있는 `Permanent Generation`과 `Method Area`가 비슷한 것 같다.](#-----heap------permanent-generation----method-area-----------)

# JVM Heap

## 1. Heap 메모리 영역
![image](https://user-images.githubusercontent.com/70880695/225625840-dfd795ed-a418-4dbb-9c9f-c8ec46b07d3d.png)
> 출처 : https://mirinae312.github.io/develop/2018/06/04/jvm_memory.html

- JVM의 Heap은 5가지의 영역으로 나뉜다.

### 1-1. Young Generation
- 새로 생성된 객체가 위치하는 영역, 생명 주기가 짧은 객체가 GC 대상이 된다.

#### Eden
- new로 생성된 객체가 위치함
- 정기적으로 GC 발생한 이후 살아남은 객체는 `Survivor` 영역으로 이동 (1, 2중 하나로)

<br />

#### Survivor 1 / Survivor 2
- 둘 중 하나가 차면 살아남은 객체는 비워진 Survivor로 이동
  - 만약 Survivor 2가 가득 찼다면, 참조가 없는 객체들을 메모리에서 제거 후 살아 남은 것들만 Survivor1으로 이동
  - 즉, Survivor 1 혹은 2 중 하나는 항상 비워진 상태가 된다.

<br />

### 1-2. Old Generation
- Survivor1, 2를 왔다갔다 하면서 끝까지 살아남은 객체가 Old로 이동
  - 즉, 생명주기가 긴 객체가 남게되는 영역
  - 마찬가지로 GC도 `Young` 영역보다 적게 발생

<br />

## 2. JVM 내의 Heap에서의 객체 LifeCycle
![image](https://user-images.githubusercontent.com/70880695/225628701-17f58978-6e98-4374-bbbd-cb0fb0c46bf7.png)
> 출처: https://mirinae312.github.io/develop/2018/06/04/jvm_memory.html
- `Minor GC` : `Eden` `Survivor` 같은 `Young` 영역에서 발생하는 GC
- `Major GC` : `Old` `Permanent`에서 발생하는 GC

<br />

### 2-1. JVM에서의 `Permanent`

#### Java 8에 와서 제거되고 `Metaspace` 영역 추가
1. `Permanent` 영역은 Heap에 위치해 JVM에 의해 관리되는 영역이고 아래와 같은 정보를 저장한다. 
    - 클래스의 메타 정보와 이름
    - 클래스의 메서드
    - 상수 풀 정보
    - JVM 내부 객체, 최적화 컴파일러(JIT) 최적화 정보
2. `Metaspace`는 Heap이 아닌 Native 메모리 (OS레벨에서 관리하는) 영역으로 취급된다.
    - `Permanent` 영역이 java 8부터 `Metaspace`에 저장된다.

<br />

#### 그렇다면 왜 `Metaspace` 영역으로 대체했을까 ? 
1. `Permanent` 영역은 JVM에 의해 관리되고 크기도 제한됐다. 
2. 반면에 `Metaspace` 영역은 OS level에서 관리되기 때문에 크기도 자동으로 늘어나고 시스템 메모리 상한선까지 사용할 수 있다.
   - 자동으로 늘어나기 때문에 `OutOfMemoryError`가 덜 발생한다.
   - 그래도 메모리는 한정적이니 메모리 누수는 발생하지 않도록 해야 한다.

<br />

#### 얼핏보면 Heap에 있는 `Permanent Generation`과 `Method Area`가 비슷한 것 같다.
1. `Method` 영역은 `Permanent Generation`의 일부이다.
2. `Method` 영역은  Thread가 공유하는 공간, `Permanent Generation`은 JVM이 사용하는 공간
- 추후 GC의 동작을 살펴보면서 다시 정리 예정



```markdown
### 질문
1. JVM의 Heap과 Stack 차이
    - 생명주기 / 실제 값과 참조 값 / StackOverFlow, OutOfMemoryError / 어디서든 사용 가능, 스레드끼리 공유 불가
```
