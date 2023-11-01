# https://github.com/WeeklyStudy/spring/issues/7 서블릿 컨테이너와 서블릿이란 무엇이고, 서블릿 생명주기는 어떻게 될까?

## 💡서블릿(Servlet)

> 클라이언트의 요청을 처리하고, 그 결과를 반환하는 자바 웹 프로그래밍 기술이다.
> 

### 1. 특징

- 동적인 페이지를 생성하는 기술이다.
- **Java 코드 안에 HTML 코드를 사용한다.**
- HTML 변경 시 서블릿을 재컴파일해야 한다.(단점)
- `Java Thread`를 이용하여 동작한다.
- MVC 패턴에서 `Controller`로 이용된다.

> **📍서블릿 생성 방법**
> 
> 1. 서블릿 클래스에 `@WebServlet(urlPatterns = "")` 으로 해당 서블릿과 매핑될 url을 지정한다.
> 2. `HttpServlet` 클래스를 상속한다.

### 2. 서블릿의 생명주기

<img width="300" src="https://github.com/WeeklyStudy/spring/assets/48237976/f210189b-39a3-4212-bd31-5d750d74bcf6">


- `init()` : 서블릿 초기화 시(메모리에 올릴 때), 한 번만 실행된다.
- `service()` : 요청/응답(request/response)을 처리하며 요청이 GET/POST인지 구분하여 `doGet()` 또는 `doPost()` 메소드로 분기된다.
- `destroy()` : 서블릿 종료 요청 시, 한 번만 실행된다.

## 💡서블릿 컨테이너(Servlet Container)

> 서블릿을 관리해주는 컨테이너다.
> 
- WAS = Web Server + Web Container(Sevlet Container)

### 1. 특징

- `서블릿과 웹 서버의 통신`을 지원한다.
- `서블릿의 생명주기(생성/호출/종료)`를 관리한다.
- `멀티 스레드`를 지원한다.
- 서블릿을 `싱글톤`으로 관리한다.

## Reference

- [[JSP] 서블릿(Servlet)이란?*](https://mangkyu.tistory.com/14)
- [[Spring] 서블릿과 서블릿 컨테이너란?](https://steady-coding.tistory.com/462)
- [Servlet*](https://velog.io/@han_been/Servlet)
- [서블릿 컨테이너(Servlet Container) 란?*](https://velog.io/@han_been/%EC%84%9C%EB%B8%94%EB%A6%BF-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88Servlet-Container-%EB%9E%80)

# https://github.com/WeeklyStudy/spring/issues/8 WAS(Web Application Server)의 스레드 풀(Thread Pool)은 무엇이고, 스레드 풀을 사용하는 이유는 무엇인가?

## 💡Thread Pool(스레드 풀)

> 스레드들을 미리 생성해놓고 재사용하는 방식이다.
> 

### 1. SpringBoot가 다중 요청을 처리하는 방법

스프링 부트에 내장된 서블릿 컨테이너(Tomcat)가 다중 요청을 처리해준다.

1. Tomcat은 다중 요청을 처리하기 위해 부팅할 때 `Thread Pool` 을 생성합니다.
2. 클라이언트 요청(`HttpServletRequest`)이 들어오면 `Thread Pool`에서 꺼낸 `Thread`를 각각 할당합니다.
3. 해당 `Thread`에서 `Dispatcher Servlet`을 거쳐 요청을 처리합니다.
4. 모든 작업이 끝나면 해당 `Thread` 를 `Thread Pool` 로 반환합니다.

### 2. Thread Pool을 사용하는 이유

- 스레드 생성/소멸 비용을 절약하여, 응답 속도가 빨라진다.
- 스레드 최대 사이즈를 설정하여, 다수의 요청이 들어와도 기존 요청을 안전하게 처리할 수 있다.
    - 모든 쓰레드가 사용중일 때, 요청을 거절하거나 대기하도록 설정해서 서버가 다운되지 않도록 할 수 있다.
    - Tomcat의 기본 최대치 = 200

## Reference

- [Spring Thread Pool](https://velog.io/@tritny6516/Spring-Thread-Pool)
- [스프링부트는 어떻게 다중 유저 요청을 처리할까? (Tomcat9.0 Thread Pool)*](https://velog.io/@sihyung92/how-does-springboot-handle-multiple-requests)
- [[Spring] 서블릿 컨테이너 개념과 쓰레드 풀](https://velog.io/@semi-cloud/Spring-%EC%84%9C%EB%B8%94%EB%A6%BF)

# https://github.com/WeeklyStudy/spring/issues/9 서블릿은 어떻게 동작하는가?

## 💡서블릿 동작 방식

<img width="500" src="https://github.com/WeeklyStudy/spring/assets/48237976/4fb880ab-a1cf-4b29-9efc-5cfa0ef2902b">


1. 사용자(Client)가 URL을 입력하면 `HTTP Request`가 `Servlet Container`로 전송됩니다.
2. 요청을 받은 `Servlet Container`는 `HttpServletRequest`, `HttpServletResponse` 객체를 생성합니다.
3. `web.xml`을 기반으로 사용자가 요청한 URL이 어느 서블릿에 대한 요청인지 찾습니다.
4. 해당 서블릿에서 `service()` 메서드를 호출한 후 GET, POST 여부에 따라 `doGet()` 또는 `doPost()` 메서드를 호출합니다.
5. `doGet()` , `doPost()` 메서드는 동적 페이지를 생성한 후 `HttpServletResponse`객체에 응답을 보냅니다.
6. 응답이 끝나면 `HttpServletRequest`, `HttpServletResponse` 두 객체를 소멸시킵니다.

## Reference

- [[JSP] 서블릿(Servlet)이란?*](https://mangkyu.tistory.com/14)
