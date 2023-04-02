# Thread 및 비동기

- `동기` `비동기` : 프로세스 `수행 순서 보장`에 대한 매커니즘
- `블로킹` `논블로킹` : 프로세스 `유휴 상태`에 대한 개념

## 1. 동기 / 비동기 / 블로킹 / 논블로킹 개념

### 1-1. 블로킹 (Blocking)

![image](https://user-images.githubusercontent.com/70880695/229350947-0a8f0748-8ddb-4a1f-b23b-7eba91374f08.png)

- 자신의 작업을 진행하다가 다른 주체의 작업이 시작되면, 다른 작업이 끝날 때까지 **기다렸다가** 자신의 작업을 시작하는 것

#### 🔵 Blocking 실생활 예

1. 직원 A는 서류를 작성해서 상사에게 전달했다.
2. Blocking 상사는 해당 서류 다 읽을 때까지 A 직원에게 기다리라고 한다.
    - 상사가 다 읽을 때까지 A 직원은 아무것도 못한다.
3. Blocking 상사가 서류를 다 읽고나서 A에게 돌아가라고 한다.
    - 이 때부터 직원은 자기 일 처리 가능

### 1-2. 논블로킹 (Non-Blocking)

![image](https://user-images.githubusercontent.com/70880695/229351042-dbae4072-cf36-4ec0-a6a1-f611440ce3d9.png)

- 다른 주체의 작업에 **관련없이** 자신의 작업을 하는 것

#### 🔵 Non-Blocking 실생활 예

1. 직원 A는 서류를 작성해서 상사에게 전달했다.
2. Blocking 상사는 서류 받고나서 직원에게 '읽어볼테니 가세요' 라고 한다.
3. 직원은 곧바로 자리에 들어가서 자신의 일처리가 가능하다.

#### 🔵 Blocking vs Non-Blocking

- 다른 주체가 작업할 때 자신의 `제어권`이 있는지 없는지로 볼 수 있다.

<br />

### 1-3. 동기 (Synchronous)

![image](https://user-images.githubusercontent.com/70880695/229351371-4ed255f2-6453-4c98-b6d5-76f91d30bbc8.png)

- 작업을 동시에 수행하거나, 동시에 끝나거나, **끝나는 동시에 시작함을 의미**

#### 🔵 Synchronous 실생활 예

1. 블로킹 여부에 상관없이 Synchronous 상사는 직원이 기다리든, 다른 일을 하든 신경쓰지 않는다.
2. A 직원은 상사가 서류 보고 전달해 준 결과를 갖고 빠르게 업무를 하고 싶다.
3. 그래서 상사가 전달 받은 서류를 다 읽고나서 결과를 돌려주면 직원은 '바로 처리하겠습니다' 하고 처리 시작

### 1-4. 비동기 (Asynchronous)

![image](https://user-images.githubusercontent.com/70880695/229351394-1fd5eb8d-00e1-4040-bf3a-6005a2ab15b4.png)

- 시작, 종료가 일치하지 않으며, **끝나는 동시에 시작을 하지 않음**을 의미
- 동기와는 다르게 결과에 큰 비중 두지 않고 곧바로 자신의 다른 할 일을 하러 돌아온다.
- 나중에 결과가 돌아오면 처리를 안 할 수도 있고 할 수도 있다.

#### 🔵 Asynchronous 실생활 예

1. `Asynchronous` 상사는 `Synchronous` 상사와는 다르게 일을 바로 처리하지 않아도 괜찮다.
    - 또한 `Blocking` 상사나 `Non-Blocking` 상사가 아니기 때문에 기다리거나 다른 일을 해도 신경쓰지 않는다.
2. A 직원은 상사에게 서류를 주고 기다리거나 다른 일을 할 것이다.
3. 상사는 서류를 다 읽고 A 직원에게 나중에 확인하라고 적어서 보내준다.
4. A 직원은 나중에 내용을 확인하고 언젠가 처리하면 될 것이다.

#### 🔵 Synchronous vs Asynchronous

- 결과 돌려주었을 때 `순서와 결과에 관심`이 있는지 아닌지로 동기/비동기 판단할 수 있다.

<br />

## 2. 4가지 조합의 경우

### 2-1. Blocking / Synchronous

![image](https://user-images.githubusercontent.com/70880695/229351961-c9790c42-5bf2-4f47-823c-535b11fcc3b4.png)

- Blocking은 `제어권`이 관심사, 따라서 다른 작업이 시작되는 동안 동작하지 않음
- Sync는 `결과의 처리`가 관심사, 따라서 결과를 반환하면 곧바로 해당 업무를 처리한다.

<br />

#### 🔵 Blocking + Sync 실생활 예

1. A 직원은 Blocking/Sync 상사에게 서류 전달
2. `[Blocking]` 상사는 직원에게 자신이 서류 다 읽을 때까지 기다리라고 한다.
3. `[Sync]` 다 읽고 결과를 직원에게 전달하면 직원은 해당 업무를 바로 처리하게 된다.

<br />

#### 🔵 Blocking + Sync 는 어디에 사용될까 ?

```java
// `메시지를 입력받고 출력하는 작업`
public class Application {
	public static void main(String[] args) {
		System.out.println("출력하고 싶은 메시지 입력 : ");
		final Scanner scanner = new Scanner(System.in);
		String message = scanner.nextLine();
		System.out.println("입력한 메시지 : " + message);
	}
}
```

1. Blocking + Sync는 `순서가 중요한 작업`에 사용된다.
    - 메시지를 입력받고 출력하는 순서가 중요하기 때문에 Blocking + Sync를 사용한다.
2. 입력에서 제어권이 넘어갔기 때문에 `(blocking)` 입력을 기다린다.
    - 입력하면 제어권과`(blocking)` 결과를 받아서 바로 처리`(sync)`하고 있다.

<br />

### 2-2. Non-Blocking / Synchronous

![image](https://user-images.githubusercontent.com/70880695/229351976-cc6b0602-f994-492d-9aa1-8a6d9049652a.png)

- `Non-Blocking` : 다른 작업이 있어도 자신이 제어권을 가지고 일을 처리
- `Synchronous` : 결과에 관심이 있는 것
    - 그래서 자신의 일처리 중간중간 `결과 나왔나요?` 물어보게 된다.
    - 결과가 나오면 바로 처리하고 다음 일을 하러 간다.

<br />

#### 🔵 Non-Blocking + Sync 실생활 예

1. 서류를 받은 Non-Blocking / Sync 상사는 직원에게 다른 업무를 보라고 한다.
2. 그런데 직원은 상사에게 너무 시달린 나머지 상사가 전달해준 일은 바로 처리해야 한다는 강박이 있다.
    - 그래서 상사에게 `끝나셨나요?` 물어본다.
3. 상사가 `아직 끝나지 않았다`고 하면 직원은 다시 자신의 업무를 보러 하러 간다.
4. 직원이 또 다시 상사에게 `끝나셨나요?` 물어본다.
5. 상사가 `끝났다`고 하면 직원은 바로 처리하고 다시 자신의 업무를 보러 간다.

<br />

#### 🔵 Non-Blocking + Sync 특징

1. Blocking / Sync와 별 차이가 없다.
2. 게임에서 맵 로딩 시 유저에게 몇 %까지 완료됐는지 보여주는 것과 같다고 보면 된다.

<br />

### 2-3. Blocking / Async

![image](https://user-images.githubusercontent.com/70880695/229352251-8ddf8028-2b55-4d96-8d27-adbcd0dc89d1.png)

- Blocking이기 때문에 자신의 작업에 대한 제어권이 없다.
- 하지만 Async이기 때문에 곧바로 결과 처리하지 않아도 된다.
- 굳이 비동기인데 Blocking을 써야하는가? 라는 생각이 든다.
    - 개발자의 실수로 이 경우가 발생할 수 있다고 한다.

#### 🔵 Blocking + Async 실생활 예

1. A 직원이 상사에게 서류 전달
2. 상사는 자신이 일하는 동안 옆에 사람 두는 것을 좋아해서 A 직원에게 옆에서 기다리라고 한다.
3. A 직원은 결과에 크게 관심이 없는데도 기다려야 한다.
4. 일이 끝나면 상사는 결과를 회사톡으로 보내줄 테니 돌아가라고 한다.
5. 사원은 돌아가서 자신 일 하고 있다가 이전에 상사가 보내준 결과를 갖고 일처리할 것이다.

<br />

### 2-4. Non-Blocking / Async

![image](https://user-images.githubusercontent.com/70880695/229352504-ec257f21-33e0-45c5-ad1a-459aaf3b12ac.png)

1. Non-Blocking은 다른 작업이 시작되어도 자신이 하던 작업은 멈추지 않는다.
    - 그렇기 때문에 Application과 Kernel 작업은 각자 동시에 일어나게 될 것이다.
2. Async는 결과에 관심 없기 때문에 다른 작업에서 끝난 결과를 바로 처리하지 않고 자신의 일이 끝나야 처리한다.

#### 🔵 Blocking + Async 실생활 예

1. A 직원이 마지막 서류를 상사에게 전달
2. Non-Blocking이기 때문에 상사는 사원에게 돌아가서 자신의 일을 하라고 한다.
3. 상사는 서류를 다 읽고 나면 회사톡으로 결과를 적어서 A 직원에게 전달한다.
4. A 직원은 나중에 이 톡을 확인하고 처리하게 될 것이다.

#### 🔵 Non-Blocking + ASync 는 어디에 사용될까 ?

1. `javascript`에서 api 요청 후 다른 작업 하다가 `callback` 통해서 추가 작업 처리할 때 사용

```javascript
fetch('url', option)
    .then((res) => {
        // callback
        return response.json();
    })
    .then((data) => {
        // callback
        something(data);
    });
```

### 3. 결과 정리

![image](https://user-images.githubusercontent.com/70880695/229352747-0d966d15-1bcb-4eaf-915f-d6d763d135f4.png)

- `Blocking`은 자신의 작업에 대한 제어권이 없다.
- `Non-Blocking`은 자신의 작업에 대한 제어권이 있다.
- `Sync`는 결과를 받을 때까지 기다려야 한다.
- `Async`는 결과를 받을 때까지 기다리지 않아도 된다.

### 출처

[1] [inpa dev : 동기 & 비동기 vs 블로킹 & 논블록킹 개념](https://inpa.tistory.com/entry/%F0%9F%91%A9%E2%80%8D%F0%9F%92%BB-%EB%8F%99%EA%B8%B0%EB%B9%84%EB%8F%99%EA%B8%B0-%EB%B8%94%EB%A1%9C%ED%82%B9%EB%85%BC%EB%B8%94%EB%A1%9C%ED%82%B9-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC)
[2] [Baeldung : The Difference Between Asynchronous And Multi-Threading](https://www.baeldung.com/cs/async-vs-multi-threading)
[3] [우아한테크 : [10분 테코톡] 🐰 멍토의 Blocking vs Non-Blocking, Sync vs Async](https://www.youtube.com/watch?v=oEIoqGd-Sns)
