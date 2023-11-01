## Q1. 서**블릿 컨테이너와 서블릿이란 무엇이고, 서블릿 생명주기는 어떻게 될까?**

### 1. Servlet(Java Servlet)

- 기능
    - 클라이언트와 서버가 서로 소통을 할 때 HTTP를 준수하면서 요청과 응답을 하게 된다.
    - 이 때 **클라이언트 요청을 처리하고, 그 결과를 반환하는 웹 프로그래밍 기술**을 ‘Servlet’이라고 한다.
    - Servlet은 **Java**를 활용하여 **웹페이지를 동적으로** 생성한다.
        - HTML 코드 안에 Java 코드를 작성하는 JSP와 달리, Servlet은 Java 소스코드 안에 HTML코드를 작성한다.
- 사용 이유
    - HTTP 요청 메세지 파싱 등의 비즈니스 로직이 아닌 작업을 위임하여 **의미있는 비즈니스 로직**에 **집중**할 수 있다.

### 2. Servlet Container

- 기능
    - 클라이언트가 URL을 입력하여 요청하면, 정보를 처리하기 위해 스레드를 만들고 **Servlet 객체를 생성**하고 ****Servlet을 매핑하는 등의 작업을 개발자가 처리해야 한다.
    - 이렇게 Servlet과 스레드를 관리하고 지원하는 것이 Servlet Container의 역할이다.
- 세부 기능
    - 웹서버와의 통신 지원
        - Servlet과 웹서버가 손쉽게 통신할 수 있게 해준다.
        - 웹 서버와 통신하기 위해 소켓 생성 및 listen, accept 등의 기능을 API로 제공하여 복잡한 과정을 생략할 수 있게 해준다.
    - 서블릿 생명주기(Life Cycle) 관리
        - 서블릿 클래스를 로딩하여 인스턴스화하고, 초기화 메서드를 호출하고, 요청이 들어오면 적절한 서블릿 메소드를 호출한다. 서블릿 소멸 시에는 GC(Garbage Collection)을 진행한다.
    - 멀티 스레드 지원 및 관리
        - 클라이언트의 요청은 프로세스 실행 단위인 스레드에서 처리한다.
        - 따라서 스레드가 Servlet을 호출한다.
        - 문제는 **동시 처리**가 필요하면 **스레드를 추가로 생성**해야 한다는 것이다.
        - 이렇게 각 요청을 별도의 스레드로 동시 처리하는 멀티 스레드 관련 기능을 지원해주기 때문에, 개발자가 이와 관련된 코드를 직접 작성할 필요 없다.
    
    > 
    > 
    > 
    > ### WAS의 일부인 Servlet Container
    > 
    > WAS는 Web Server + Web Container(Servlet Container)로 구성되어 있다.
    > 
    
    ### 3. Servlet 생명주기
    
    ![image](https://github.com/WeeklyStudy/spring/assets/63441091/bade5284-5fe0-4fe8-9a7b-807b0c855467)

    
    - `init()`
        - 디폴트 생성자를 이용해서 서블릿 객체를 생성하고 초기화한다.
        - 따라서, 초기에 필요한 Servlet 설정값이 있을 경우, `init()` 메서드를 오버라이딩하여 구현하는데, 이 때 공유 변수, 멤버 변수는 공용으로 사용하는 만큼 주의해서 사용해야 한다.
        - 만일, 실행 중 서블릿이 변경되면 기존 서블릿을 제거하고 `init()`을 통해 새로운 객체를 생성한다.
    - `service()`
        - 요청이 있을때 마다 호출되는 메서드이다.
        - HTTP 메소드를 참조하여 `doGet()`을 호출할지, `doPost()`를 호출할지 결정한다.
    - `destroy()`
        - 소멸될 때 호출되는 메서드이다.
        - `distroy()`도 마찬가지로 한번만 실행되기 때문에, 종료시에 처리해야하는 작업들은 `destroy()`메서드를 오버라이딩하여 구현하면 된다.

### References

- [[WEB] 서블릿(Servlet) 과 서블릿 컨테이너(Servlet Container)](https://maenco.tistory.com/entry/%EC%84%9C%EB%B8%94%EB%A6%BFServlet-%EA%B3%BC-%EC%84%9C%EB%B8%94%EB%A6%BF-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88Servlet-Container)
- [[Web] Servlet과 JSP의 차이와 관계](https://gmlwjd9405.github.io/2018/11/04/servlet-vs-jsp.html)
- [서블릿 생명주기 (Servlet LifeCycle)](https://velog.io/@corone_hi/%EC%84%9C%EB%B8%94%EB%A6%BF-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-Servlet-LifeCycle)

## Q2. 서블릿은 어떻게 동작하는가?

### 서블릿 컨테이너가 관리하는 서블릿 동작 과정

- 서블릿 컨테이너가 흐름을 제어한다. 즉 IoC(Inversion of Control)가 일어난다.
- 동작 과정
    1. 클라이언트의 요청이 들어오면, HTTP 요청을 처리하기 위한 HttpServletRequest 객체 및 HttpServletResponse 객체 생성한다.
    2. 설정 정보를 확인하여 해당 서블릿 인스턴스 존재의 유무를 확인한다. 확인해서 없으면 `init()` 메서드를 호출하여 생성한다.
    3. `init()`이 호출된 후 각 스레드의 `service()`를 통해 요청에 대한 응답이 `doGet()` 이나 `doPost()`로 분기된다.
        - 이 때 앞에서 과정에서 생성된 HttpServletRequest 객체와 HttpServletResponse 객체가 인자로 전달된다.
    4. `doGet()` or `doPost()` 로직에서 생성된 동적 웹 페이지 결과물은 HttpServletResponse 객체에 담긴다.
    5. HttpServletResponse 객체를 서블릿 컨테이너에서 HTTP 형태로 바뀌어 웹서버로 전송한다.
    6. HttpServletRequest 객체와 HttpServletResponse 객체의 메모리 소멸 및 스레드 종료한다.

### References

- [Servlet의 개념과 동작 과정 - 끄적끄적 - 티스토리](https://programmingnote.tistory.com/61)
- [[WEB] 서블릿(Servlet) 과 서블릿 컨테이너(Servlet Container)](https://maenco.tistory.com/entry/%EC%84%9C%EB%B8%94%EB%A6%BFServlet-%EA%B3%BC-%EC%84%9C%EB%B8%94%EB%A6%BF-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88Servlet-Container)

## Q3. **WAS(Web Application Server)의 스레드 풀(Thread Pool)은 무엇이고, 스레드 풀을 사용하는 이유는 무엇인가?**

### 1. 스레드 풀이란?

- 프로그램 실행에 필요한 Thread들을 미리 생성해놓는 것이다.

### 2. 스레드 풀 사용 이유

- 모든 요청에 대해 스레드를 생성하고 소멸하는데에 OS와 JVM에 많은 부담을 주고, 동시에 일정 이상 다수의 요청이 올 경우 CPU와 메모리 자원의 소모가 심하기 때문에 이를 해결하기 위해 Spring 내장 서버 Tomcat은 스레드 풀을 사용한다.

### References

- https://velog.io/@tritny6516/Spring-Thread-Pool
