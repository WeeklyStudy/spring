## Q1. 서블릿 컨테이너와 서블릿이란 무엇이고, 서블릿 생명주기는 어떻게 될까?

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
    - 클라이언트가 URL을 입력하여 요청하면, 정보를 처리하기 위해 스레드를 만들고 Servlet 객체를 생성하고 Servlet을 매핑하는 등의 작업을 개발자가 처리해야 한다.
    - 이렇게 Servlet과 스레드를 관리하고 지원하는 것이 Servlet Container의 역할이다.
- 세부 기능
    - 웹서버와의 통신 지원
        - Servlet과 웹서버가 손쉽게 통신할 수 있게 해준다.
        - 웹 서버와 통신하기 위해 소켓 생성 및 listen, accept 등의 기능을 API로 제공하여 복잡한 과정을 생략할 수 있게 해준다.
    - Servlet 생명주기(Life Cycle) 관리
        - Servlet 클래스를 로딩하여 인스턴스화하고, 초기화 메서드를 호출하고, 요청이 들어오면 적절한 Servlet 메소드를 호출한다.
        - Servlet의 소멸 시점을 관리하고, Servlet이 더 이상 필요하지 않을 때 소멸 메서드를 호출하여 리소스를 해제한다.
        - 즉, Servlet Container는 서비스에서 Servlet을 제거해야 하는지 결정할 수 있다.
           - 예를 들어 Container가 메모리 리소스를 회수하려고 하거나 종료되는 경우(서버 종료)가 이에 해당된다.
    - 멀티 스레드 지원 및 관리
        - 클라이언트의 요청은 프로세스 실행 단위인 스레드에서 처리한다.
        - 따라서 스레드가 Servlet을 호출한다.
        - 문제는 **동시 처리**가 필요하면 **스레드를 추가로 생성**해야 한다는 것이다.
        - 이렇게 각 요청을 별도의 스레드로 동시 처리하는 멀티 스레드 관련 기능을 지원해주기 때문에, 개발자가 이와 관련된 코드를 직접 작성할 필요 없다.
   
<br>

> ##### WAS와 Servlet Container
> - WAS는 Web Server + Web Container(Servlet Container)로 구성되어 있다.
> - Web Server와 WAS를 같이 사용하는 경우, Web Server에서 정적 파일을 처리 하고, 동적파일이 필요한 경우 WAS 즉, 서블릿 컨테이너로 요청한다.

### 3. Servlet 생명주기
    
![image](https://github.com/WeeklyStudy/spring/assets/63441091/bade5284-5fe0-4fe8-9a7b-807b0c855467)
1. 클라이언트의 요청이 들어오면 컨테이너는 해당 서블릿이 메모리에 있는지 확인하고 없으면 `init()`을 호출한다.
   - `init()`은 처음 한번만 실행된다.
   - 따라서, 초기에 필요한 Servlet 설정값이 있을 경우, `init()` 디폴트 메서드를 오버라이딩하여 구현해야 한다.
   -  이 때 공유 변수, 멤버 변수는 공용으로 사용하는 만큼 주의해서 사용해야 한다.
   - 만일, 실행 중 서블릿이 변경되면 기존 서블릿을 제거하고 `init()`을 통해 새로운 객체를 생성한다.
2. 클라이언트 요청이 있을 때마다  `service()`를 호출한다.
   - 요청 HTTP 메서드를 참조하여 `doGet()`을 호출할지, `doPost()`를 호출할지 결정한다.
3. 컨테이너가 Servlet에 종료 요청하면 `destroy()`를 호출하여 소멸시킨다.
   - `destroy()`도 초기화 매서드와 마찬가지로 한번만 실행되기 때문에, 종료 시에 처리해야하는 작업들은 `destroy()`메서드를 오버라이딩하여 구현하면 된다.
   - Servlet이 제거될 때(소멸될 때)는 해당 Sevlet의 `service()` 메서드들이 모두 실행 완료되어야 한다.

### References

- [[WEB] 서블릿(Servlet) 과 서블릿 컨테이너(Servlet Container)](https://maenco.tistory.com/entry/%EC%84%9C%EB%B8%94%EB%A6%BFServlet-%EA%B3%BC-%EC%84%9C%EB%B8%94%EB%A6%BF-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88Servlet-Container)
- [[Web] Servlet과 JSP의 차이와 관계](https://gmlwjd9405.github.io/2018/11/04/servlet-vs-jsp.html)
- [서블릿 생명주기 (Servlet LifeCycle)](https://velog.io/@corone_hi/%EC%84%9C%EB%B8%94%EB%A6%BF-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-Servlet-LifeCycle)
- [[Java Doc] Finalizing a Servlet](https://docs.oracle.com/javaee/6/tutorial/doc/bnags.html)
- [[StackOverFlow] when exactly a servlet is destroyed](https://stackoverflow.com/questions/24832470/when-exactly-a-servlet-is-destroyed)

## Q2. 서블릿은 어떻게 동작하는가?

### 1. 서블릿과 서블릿 컨테이너
- 서블릿 컨테이너가 서블릿 흐름을 제어한다. 즉 IoC(Inversion of Control)가 일어난다.

### 2. 서블릿 컨테이너가 관리하는 서블릿 동작 과정
1. 클라이언트의 요청이 들어오면, HTTP 요청을 처리하기 위한 HttpServletRequest 객체 및 HttpServletResponse 객체 생성한다.
2. 설정 정보를 확인하여 해당 서블릿 인스턴스 존재의 유무를 확인한다. 확인해서 없으면 `init()` 메서드를 호출하여 생성한다.
3. `init()`이 호출된 후 각 스레드의 `service()`를 통해 요청에 대한 응답이 `doGet()` 이나 `doPost()`로 분기된다.
   - 이 때 이전 과정에서 생성된 HttpServletRequest 객체와 HttpServletResponse 객체가 인자로 전달된다.
4. `doGet()` or `doPost()` 로직에서 생성된 동적 웹 페이지 결과물은 HttpServletResponse 객체에 담긴다.
5. HttpServletResponse 객체를 서블릿 컨테이너에서 HTTP 형태로 바뀌어 웹서버로 전송한다.
6. HttpServletRequest 객체와 HttpServletResponse 객체의 메모리 소멸 및 스레드 종료한다.

### References

- [Servlet의 개념과 동작 과정 - 끄적끄적 - 티스토리](https://programmingnote.tistory.com/61)
- [[WEB] 서블릿(Servlet) 과 서블릿 컨테이너(Servlet Container)](https://maenco.tistory.com/entry/%EC%84%9C%EB%B8%94%EB%A6%BFServlet-%EA%B3%BC-%EC%84%9C%EB%B8%94%EB%A6%BF-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88Servlet-Container)

## Q3. **WAS(Web Application Server)의 스레드 풀(Thread Pool)은 무엇이고, 스레드 풀을 사용하는 이유는 무엇인가?**
### 1. Thread Pool(스레드 풀)이란?

- 여러 스레드를 효율적으로 관리하고 재사용하기 위해 스레드를 미리 생성하고 빌려쓰는 동시성 제어 메커니즘

### 2. Thread Pool 사용 이유

- Thread Pool을 사용하면 효율적인 자원 관리가 가능하다.
  - Thread 재사용 가능
    - Thread는 생성 비용이 비싸다.
    - OS와 JVM에 많은 부담을 주고, 응답속도가 늦어지기 때문이다.
  - Thread 갯수 제어
    - Thread Pool에 의해 MAX_THREAD를 관리하지 않으면, 생성에 제한이 없기 때문에 동시에 일정 이상 다수의 요청이 올 경우 CPU, 메모리 임계점을 넘어 서버가 죽을 수 있다.
  - 작업 Queue로 순차적 관리
  - 동시성 및 병렬 처리로 성능 향상

### 3. 스프링 부트에서 다중 요청을 처리하는 방법

- 스프링 부트는 내장 서블릿 컨테이너인 Tomcat을 사용한다.
- 애플리케이션이 구동될 때, Thread Pool을 생성한다.
- 유저의 요청(HttpServletRequest)이 들어오면 Thread Pool에서 하나씩 Thread를 할당한다.
- 해당 Thread에서 스프링 부트의 Dispatcher Servlet을 거쳐 유저 요청을 처리한다.
- 작업을 모두 수행하고 나면 Thread는 Thread Pool로 반환된다.

### References

- [Spring Thread Pool](https://velog.io/@tritny6516/Spring-Thread-Pool)
