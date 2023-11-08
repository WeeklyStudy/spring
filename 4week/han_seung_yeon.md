# https://github.com/WeeklyStudy/spring/issues/10 MVC 패턴이란 무엇이고, MVC1과 MVC2의 차이는?

## 💡MVC 패턴

> Model, View, Controller로 역할을 구분한 디자인 패턴이다.
> 
- `Model` : 데이터 관리 및 비즈니스 로직을 처리하는 부분(DAO, DTO, Service)
- `View` : 비즈니스 로직의 처리 결과를 통해 사용자 인터페이스가 표현되는 구간
    - HTML, JSP, Thymeleaf로 화면을 구성하거나 REST API 서버에서는 JSON 응답으로 구성됨
- `Controller` : 사용자의 요청을 처리하고 Model과 View를 중개하는 역할

## 💡MVC1

> `JSP`가 `Controller`와 `View` 를 담당한다.
> 
- `JSP`가 모든 처리를 담당한다.
![Untitled (20)](https://github.com/WeeklyStudy/spring/assets/48237976/ff5a93c3-7dd3-4a89-b337-0ec00e8a24e8)

- 장점
    - 구조가 단순하고 구현하기 쉽다.
- 단점
    - JSP에서 HTML 코드와 자바 코드를 같이 사용하기 때문에, 유지보수가 어렵다.

## 💡MVC2

> `JSP`가  `View`를 담당하고, `Servlet`이 `Controller` 를 담당한다.
> 
- `Controller`와 `View`가 분리되었다.
![Untitled (21)](https://github.com/WeeklyStudy/spring/assets/48237976/bd9459cb-dff8-4fe7-9c2a-8fb58353db6b)


- 장점
    - 유지보수와 확장이 용이하다.
- 단점
    - 구조가 복잡해질 수 있다.
    - 구조 설계를 위한 시간이 많이 소요되고 개발 속도가 느리다.

## Reference

- [[Spring] Spring의 MVC 패턴과 MVC1과 MVC2 비교](https://chanhuiseok.github.io/posts/spring-3/)
- [[Java/Jsp] MVC 1, MVC 2 차이 및 장단점](https://onejuny.tistory.com/entry/JavaJsp-MVC-1-MVC-2-%EC%B0%A8%EC%9D%B4-%EB%B0%8F-%EC%9E%A5%EB%8B%A8%EC%A0%90)

# https://github.com/WeeklyStudy/spring/issues/11 어노테이션 @Controller와 @RestController의 작동 방식 차이는 무엇인가

## 💡@Controller와 @RestController의 차이

> `@Controller`는 **View(화면)**을 반환하기 위해 사용하고, `@RestController`는 `@Controller + @ResponseBody`의 형태로 **JSON 데이터**를 반환하기 위해 사용한다.
> 

## 💡Spring MVC 구조

### 1. 구성요소

| 구성요소 | 설명 |
| --- | --- |
| DispatcherServlet | 클라이언트에게 요청을 받아 응답을 보낼 때까지의 MVC 처리과정을 통제한다.(프론트 컨트롤러) |
| HandlerMapping | 클라이언트의 요청 URL을 어떤 Controller가 처리할지 결정한다. |
| HandlerAdapter | HandlerMapping에서 결정된 핸들러 정보로 해당 메서드를 직접 호출한다. |
| Controller | 클라이언트의 요청을 처리한 뒤 결과를 리턴한다. |
| ModelAndView | Controller가 처리한 결과 정보(model)와 뷰 선택에 필요한 정보(view name)을 담는다. |
| ViewResolver | Controller의 처리 결과를 보여줄 View를 결정한다. |
| View | Controller의 처리 결과 화면을 생성한다. |

### 2. @Controller의 동작 흐름
![Untitled (22)](https://github.com/WeeklyStudy/spring/assets/48237976/5327e8ca-df53-43e3-aeb4-7b24cc929d42)
![Untitled (23)](https://github.com/WeeklyStudy/spring/assets/48237976/56f6c103-93f8-4f29-9acf-cf97957148e9)

1. `DispatcherServlet`으로 클라이언트의 HTTP 요청이 들어온다.
2. `DispatcherServlet`은 `HandlerMapping`을 통해 해당 요청을 처리할 `Controller`를 찾는다.
3. `DispatcherServlet`은 `Controller`를 실행할 수 있는 `HandlerAdapter`를 찾는다.
4. `HandlerAdapter`는 `Controller`의 메서드를 호출한다.
5. `Controller`는 비즈니스 로직을 처리하고 결과를 `Model`에 저장하고 `view name`을 리턴한다.
6. `DispatcherServlet`은 `ViewResolver`을 통해 `view name`에 해당하는 `View`를 찾는다.
7. `DispatcherServlet`은 `View`에게 `Model`을 전달하고 화면 표시를 요청한다.
    - `Model`이 null이면 `View`를 그대로 사용하고, null이 아니면 `View`에 `Model` 데이터를 렌더링한다.
8. `DispatcherServlet`은 `View 결과(HttpServletResponse)`를 클라이언트에게 반환한다.

### 3. @RestController의 동작 흐름

`@RestController`의 경우 6, 7과정이 생략된다. 즉, `ViewResolver`를 거치지 않고 `Controller`의 반환값에 알맞는 `MessageConverter`를 찾아 응답 본문을 작성한다.

## Reference

- [@Controller와 @RestController의 차이점](https://dncjf64.tistory.com/288)
- [[Spring] @Controller와 @RestController 차이*](https://mangkyu.tistory.com/49)
- [Spring MVC 기본 동작 흐름](https://codingnotes.tistory.com/28)
- [DispatcherServlet - Part 1*](https://tecoble.techcourse.co.kr/post/2021-06-25-dispatcherservlet-part-1/)
- [DispatcherServlet - Part 2*](https://tecoble.techcourse.co.kr/post/2021-07-15-dispatcherservlet-part-2/)
- [신입 개발자 기술면접 질문 정리 - 백엔드](https://dev-coco.tistory.com/163)
- [[실무 면접 준비 - 8] Spring Framework](https://imbf.github.io/interview/2021/03/06/NAVER-Practical-Interview-Preparation-8.html)

# https://github.com/WeeklyStudy/spring/issues/12 Spring MVC에서 Dispatcher Servlet 역할은 무엇인가?

## 💡DispatcherServlet이란?

> Spring MVC구조에서 HTTP 프로토콜을 통해 서버로 들어오는 모든 요청을 제일 앞에서 처리해주는 `FrontController`다.
> 
- 공통 작업은 `DispatcherServlet`에서 처리하고, 이외 작업은 적절한 세부 컨트롤러로 위임한다.
- `DispatcherServlet`은 `HttpServlet`의 하위 클래스다.

## Reference

- [DispatcherServlet - Part 1*](https://tecoble.techcourse.co.kr/post/2021-06-25-dispatcherservlet-part-1/)
- [[Spring] Dispatcher-Servlet(디스패처 서블릿)이란? 디스패처 서블릿의 개념과 동작 과정](https://mangkyu.tistory.com/18)
