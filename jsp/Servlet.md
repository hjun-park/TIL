# Servlet

## 1. 서블릿이란?

![image](https://user-images.githubusercontent.com/70880695/230265394-dad16e12-857a-40fd-a9db-6e52a172698f.png)

- 서블릿은 자바를 이용하여 웹을 개발할 때 사용하는 기술이다.
- 서블릿 컨테이너란 서블릿을 담고 관리하는 친구; 바구니 라고 생각하면 좋다.

### 1-1. 서블릿을 쓰면 어떤 이점이 있는데 ?

![image](https://user-images.githubusercontent.com/70880695/230265651-ca84826b-39e8-4425-809a-198fa3abd2a6.png)

- 만약 `request`를 직접 해석하고 처리한 후에 규약에 맞춰 `response` 같은 텍스트 형식의 응답을 만드는 것은 매우 어렵다.
- `http servlet request`를 사용하면 직접 파싱할 필요 없이 클래스 가져다 쓸 수 있어서 편하다.
- 결국 비즈니스 로직에 좀 더 신경 쓸 수 있게 하는 기술이다.

## 2. 서블릿 컨테이너와 서블릿 호출 과정

![image](https://user-images.githubusercontent.com/70880695/230266476-931f743f-4f3c-4ddb-a71a-3eae12093918.png)

1. `Servlet Request` / `Servlet Response` 객체 생성
2. 설정 파일 참고해서 Mapping할 `Servlet` 확인
3. 해당 Servlet Instance 유무 확인하여 없으면 생성 (`init()`)
4. `ServletContainer`에 thread 생성하고 `res` `req` 인자로 `service()` 호출
5. 호출 이후 `Request` `Response` 객체 소멸

- 사용자 요청이 들어오면 서블릿 컨테이너는 해당 요청과 매핑된 서블릿을 찾게됨

#### 서블릿 컨테이너에서 어떤 요청과 매핑되어 있는지 어떻게 알 수 있을까 ?

![image](https://user-images.githubusercontent.com/70880695/230268283-1ccdc159-2333-48c7-bb82-7187a4d5d397.png)

- `web.xml`에 서블릿과 url 매핑 정보가 있음
    - `/hello` 라는 요청이 들어오면 `HelloServlet`이라는 서블릿으로 처리하겠다는 것을 의미한다.

- 이렇게 설정파일을 서블릿 컨테이너가 읽어서 요청에 어떤 서블릿이 필요한 지 확인
- 서블릿 인스턴스가 컨테이너에 있는지 확인
    - 있으면 그대로 사용
    - 없으면 인스턴스 생성 (서블릿의 init() 메서드 호출)
- 서블릿 컨테이너에 스레드 생성
- 미리 만들어져 있는 `HttpServletResponse` `HttpServletRequest` 객체를 인자로 해서 `service()` 메서드 호출
- 로직 처리 후 `Request` `Response` 객체 소멸

#### 왜 `Servlet`은 생성만 하고 소멸시키지 않는가 ?

- `Servlet`이 싱글톤으로 관리되기 때문이다.
- 소멸되지 않고 있다가 다시 같은 요청이 들어오면 `서블릿 컨테이너`에 의해서 또 호출되어 사용된다.
- 즉, `서블릿 컨테이너`는 서블릿의 생명주기를 관리하는 객체

#### 만약 한 요청을 처리하던 중 다른 요청을 처리할 일이 생긴다면 ?

- `서블릿 컨테이너`는 `스레드 풀`을 이용해서 멀티스레드로 요청 처리
- 스레드 당 여러 개의 서블릿 혹은 하나의 서블릿으로 여러 개의 스레드 요청을 처리할 수 잇다.
- 하지만 스레드의 남발은 성능 저하 혹은 서버 이슈를 일으킬 수 있다.
    1. 스레드 생성 비용이 크다.
    2. 다른 스레드로 전환할 때 발생하는 `Context Switching`이 오버헤드를 일으킨다.
    3. 스레드 생성 제한 개수를 제한하지 않는다면 HW 한계 넘는 순간 서버가 터질 수 있다.

### 예시

![image](https://user-images.githubusercontent.com/70880695/230313413-c791c078-9a43-4513-8e2a-ca48850da1d9.png)

- 각각의 음료를 주문하면 각 음료를 담당하는 직원이 나와서 주문 받고 만들고 포장한다.
- 주문 받고 만들고 포장한다는 것이 중복 로직이 발생한다.

<br />

![image](https://user-images.githubusercontent.com/70880695/230313680-8c1a6ff9-47d9-4d2d-aa08-9700ec732038.png)

- 손님의 요청을 앞단에서 처리하는 매니저를 두었다. `프론트 컨트롤러 패턴`
- 매니저는 주문 받고 포장하는 공통 로직만 담당한다.

<br />

![image](https://user-images.githubusercontent.com/70880695/230314170-d3cdb832-c89f-477b-9b44-4ae9f7c164ea.png)

- 스프링 MVC도 `프론트 컨트롤러 패턴`을 따른다.
- 모든 요청을 받는 전면 컨트롤러인 서블릿을 `Dispatcher Servlet`이라고 부른다.
- 서블릿은 `하나`만 두고 `모든 요청`을 받을 수 있도록 한다.

<br />

![image](https://user-images.githubusercontent.com/70880695/230314938-65cf4136-e540-472c-9a83-e8af9208d9d8.png)

- 이전(왼쪽)의 경우 요청마다 서블릿을 정의하고 요청 수행 시마다 매번 thread 생성했다면,
- 지금(오른쪽)의 경우 `하나의 서블릿`만 정의하고 그 서블릿이 모든 요청 수행한다.

## 3. Dispatcher Servlet 이 Web 요청 처리하는 과정

![image](https://user-images.githubusercontent.com/70880695/230315949-703bb612-adb3-44a5-8520-c68133ec7c36.png)

- 이전 예제에서 매니저 한 명을 두는 방식, 만약 손님이 많이 오는 경우 매니저에 부하가 걸릴 수 있다.
- 따라서 `1) 계산만 / 2) 음료만 / 3) 포장만`을 맡는 직원을 두는 것이 좋다.

<br />

![image](https://user-images.githubusercontent.com/70880695/230316170-958c00c3-d775-4742-828e-33b6d1487872.png)

1. `Dispatcher Servlet`은 모든 요청을 다 받고 `Handler Mapping`을 통해 요청을 처리할 `Controller`를 찾는다.
2. `Handler Adapter`는 해당 `Controller`의 method 호출하여 처리 로직 수행
3. 처리 결과를 `Model And View` 객체 변환 후 `Dispatcher Servlet`에 전달
4. 전달 받은 `DispatcherServlet`은 `View Resolver`를 통해 `View`를 찾거나 생성한다.
5. 그렇게 하면 `View`를 얻게 되고 `View`는 `Model`로 들어왔던 데이터를 넣어서 응답 결과 생성 요청
6. 우리가 볼 수 있는 JSP나 Tymeleaf 등의 템플릿 엔진(데이터를 담은 출력 파일)로 응답한다.

<br />

![image](https://user-images.githubusercontent.com/70880695/230317142-b938b15c-2a65-4348-ab46-1549ced74792.png)

- 일이 더 많아진 것 같지만 사실 개발자는 이 노란 부분만 신경쓰면 된다. (`Controller` 혹은 `Handler` 뿐)

![image](https://user-images.githubusercontent.com/70880695/230317482-38f57503-932c-4f79-87dc-f315b8ec0427.png)

- `Handler Mapping` `View` `Handler Adapter` 같은 것들은 스프링 프레임워크가 대신 알아서 주입해준다.

![image](https://user-images.githubusercontent.com/70880695/230317856-10f61a3e-42a0-4bc1-85ec-1f3895131d5e.png)

- 이렇게 필요한 부분은 주입받아 사용하게 된다.

<br />

## 3. HttpServlet 주요 메서드

### 3-1. HttpServlet 주요 메서드

1. `init()` : 서블릿 객체 생성 시 호출되는 메서드
2. `destroy()` : 서블릿 객체 소멸 시 호출되는 메서드
3. `service()` : 모든 요청을 처리하는 메서드
4. `doGet()` : `GET` 요청을 처리하는 메서드
5. `doPost()` : `POST` 요청을 처리하는 메서드

### 3-2. HttpServletRequest 주요 메서드

1. `getParameter()` : 요청 파라미터 조회
2. `getParameterValues()` : 같은 이름의 파라미터 여러 개 조회
3. `getParameterNames()` : 요청 파라미터 이름 조회
4. `getParameterMap()` : 요청 파라미터 Map 조회
5. `getHeader()` : 특정 HTTP 요청 헤더 조회

### 3-3. HttpServletResponse 주요 메서드

1. `setStatus()` : HTTP 응답 코드 설정
2. `setHeader()` : HTTP 응답 헤더 설정
3. `setContentType()` : HTTP 응답 본문의 MIME 타입 설정
4. `sendRedirect()` : HTTP 응답 리다이렉트

### 3-4. HttpSession 주요 메서드

1. `getId()`: 세션 ID 조회
2. `getLastAccessedTime()`: 마지막 접근 시간 조회
3. `setMaxInactiveInterval()`: 세션 유지 시간 설정 (초 단위)
4. `isNew()`: 신규 세션인지 조회
5. `invalidate()`: 세션 만료

<br />

## 4. 서블릿 라이프 사이클

![image](https://user-images.githubusercontent.com/70880695/231022287-26e23585-d262-402a-866b-6e3f028f8fa4.png)
> 출처 :: [코데방 :: 서블릿(Servlet) 생성과 생명주기](https://codevang.tistory.com/193)

1. 클라이언트의 서블릿 요청 후 서블릿 객체 생성
    - `req` `res`는 요청 응답을 주고 받고 소멸되지만 `Servlet`은 생성되면 계속 존재함
2. `@PostConstruct`가 있다면 실행
3. `init()` 호출 (최초 한 번)
    - 처음 서블릿 사용하는 시점이 아닌 컨테이너가 처음 구동되는 시점에 초기화 진행 (`init()`이 오래 걸리기 때문)
4. `service()` `doGet()` `doPost()` 반복 호출
5. `destroy()` 호출 (최초 한 번)
    - 서블릿 컨테이너가 종료될 때 호출
6. `@PreDestroy`가 있다면 실행



  







