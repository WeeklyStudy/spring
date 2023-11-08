## https://github.com/WeeklyStudy/spring/issues/10 **MVC 패턴이란 무엇이고, MVC1과 MVC2의 차이는?**

### 1. MVC 패턴의 본질

![image](https://github.com/WeeklyStudy/spring/assets/63441091/829e2962-9293-414c-8daf-96cef53dee99)


위 사진은 MVC 패턴의 초기 아이디어이다.

MVC 패턴의 핵심은 **비즈니스 로직**과 **뷰** 간의 분리와 상호작용을 조직화하는 것이다.

- **Model(비즈니스 로직)**은 데이터 처리와 애플리케이션의 핵심 로직을 담당하고, **View(뷰)**는 사용자 인터페이스로 정보를 시각적으로 표시하고 사용자와 상호작용하는 역할을 담당한다.
- 그리고 그 사이의 **Controller(제어기)**는 Model과 View 간의 중개자 역할을 하여, Model을 업데이트하거나 뷰를 업데이트하는 역할을 한다.
- 이처럼 MVC 패턴은 **관심사를** **분리**하여 결합도를 낮추고 **변경을 용이**하게 하기 위한 패턴이다.

### 2. MVC1과 MVC2의 차이

- MVC1
    
    ![image](https://github.com/WeeklyStudy/spring/assets/63441091/c298b953-d1a0-4fa8-bafb-e410ffcb0075)

    
    - View와 Controller를 모두 JSP가 담당한다.
- MVC2
    
    ![image](https://github.com/WeeklyStudy/spring/assets/63441091/17a82625-45cb-4821-b7be-fc41f19ef866)

    
    - Controller는 Servlet이, View는 JSP(혹은 다른 템플릿 엔진)이 담당하도록 분리되었다.
    - 예시: Spring Framework의 MVC2 패턴
        
        ![image](https://github.com/WeeklyStudy/spring/assets/63441091/b801cab6-6624-4cdb-9595-be446895a5fd)

        
        - 기존 MVC 패턴에서 공통 처리를 수행하는 **FrontController**를 도입하고, 이에 적합한 Controller(Handler)를 처리하는 **HandlerMapping** 기능과 모델 데이터를 어떤 뷰로 렌더링할지 결정하는 **ViewResolver** 기능이 추가되었다.

### 3. 결론

- MVC2는 요청처리와 응답 처리가 모두 한 곳에서 이루어지는 MVC1의 코드 복잡성, 유지보수 어려움을 보완한다.
- Spring Framework의 MVC 역시 MVC2의 기본적인 아키텍처 구조를 확장하고 추가로 개선한 패턴이다.

### References

- [MVC 창시자가 말하는, MVC의 본질](https://velog.io/@eddy_song/mvc)
- [[Spring] Spring의 MVC 패턴과 MVC1과 MVC2 비교](https://chanhuiseok.github.io/posts/spring-3/)
- [[Inflearn] MVC1, MVC2, FrontController 이론적 구분 Q&A](https://www.inflearn.com/questions/856975/mvc1-mvc2-front-controller%EC%9D%98-%EC%9D%B4%EB%A1%A0%EC%A0%81-%EA%B5%AC%EB%B6%84%EC%9D%B4-%EA%B6%81%EA%B8%88%ED%95%A9%EB%8B%88%EB%8B%A4)

## https://github.com/WeeklyStudy/spring/issues/11 **어노테이션 @Controller와 @RestController의 작동 방식 차이는 무엇인가?**

### 1. `@Controller`

- 응답 형식:
    - 주로 HTML 또는 뷰 템플릿을 렌더링하는 데 사용된다.
- 동작 과정:

    <img src="https://github.com/WeeklyStudy/spring/assets/63441091/a2588176-8e53-493f-a610-29119da52934" width="600">
    
    - 클라이언트로부터 URI 웹 서비스 요청이 오면, DispatcherServlet이 인터셉트하여 요청을 처리할 대상을 찾고, 요청을 처리해줄 수 있는 HandlerAdapter로 Controller(Handler)를 실행한다. **Controller**가 요청을 처리한 후에 반환한 **ViewName**으로 ViewResolver를 통해 해당하는 **View**를 찾아 사용자에게 **응답**한다.

### 2. `@RestController`

- 특징:
    
    ```java
    @Target(ElementType.TYPE)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Controller
    @ResponseBody
    public @interface RestController {
    	@AliasFor(annotation = Controller.class)
    	String value() default "";
    }
    ```
    
    - `@RestController` 는 `@Controller`와 `@ResponseBody`가 합쳐진 어노테이션이다.
        - `@ResponseBody` 의 역할:
            - 메서드가 반환하는 데이터를 **1)직렬화**하여, **2)HTTP 응답의 본문(body)에 포함**시킨다.
- 응답 형식:
    - JSON, XML과 같은 데이터 형식으로 반환하는 데 사용된다.
- 동작 과정:
    
    <img src="https://github.com/WeeklyStudy/spring/assets/63441091/aca2e66c-7642-41d6-b486-f022d78b1eaa" width="600">


    
    - 클라이언트로부터 URI 웹 서비스 요청이 오면, DispatcherServlet이 인터셉트하여 요청을 처리할 대상을 찾고, 요청을 처리해줄 수 있는 HandlerAdapter로 Controller(Handler)를 실행하는 것까지는 **View 반환 동작 과정과 동일하다**.
    - 그러나, Controller가 요청을 처리한 후에 **객체**를 반환하면, 이 객체를 **적절한 형식(JSON 혹은 XML)으로** **직렬화**하여 사용자에게 **응답**한다.
        - 이 과정에서 **ViewResolver** 대신에 **HttpMessageConverter**가 동작한다.
        - 다양한 유형의 데이터를 HTTP 응답으로 변환하기 위한 기능을 제공하는 HttpMessageConverter에는 여러 Converter가 등록되어 있다.
        - Spring은 클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보를 조합하여 **반환해야 하는 데이터**에 따라 적합한 HttpMessageConverter를 선택한다.
            - e.g. StringHttpMessageConverter(문자열), MappingJackson2HttpMessageConverter(객체)

### 3. 결론

- `@RestController` 의 동작 방식은 `@Controller`에 `@ResponseBody` 를 붙인 동작 방식과 완벽하게 동일하다.
    - 따라서, 컨트롤러 클래스를 컴포넌트 스캔 대상으로 만들고, RequestHandlerMapping의 대상으로 인식하도록 하는 `@Controller` 의 기능 역시 포함되어 있다.
- `@RestController` 는 **HttpMessageConverter**가 ****반환된 객체를 직렬화하여 HTTP 응답 바디에 담기 때문에 **RESTful 웹 서비스**를 더욱 쉽게 개발할 수 있도록 도와준다.

### References

- [[Spring] @Controller와 @RestController 차이](https://mangkyu.tistory.com/49)
- [[Spring Boot] @Controller와 @RestController의 차이, REST 방식 사용법(Ajax)](https://hongs-coding.tistory.com/127)

## https://github.com/WeeklyStudy/spring/issues/12 **Spring MVC에서 Dispatcher Servlet 역할은 무엇인가?**

### 1. FrontController 패턴

![image](https://github.com/WeeklyStudy/spring/assets/63441091/76dd84d8-73b3-42c6-8e35-a6a152ea4dba)


- **입구**를 하나만 제공하여, **중앙 집중 처리**하는 방식이다.
- **공통 코드**에 대해서는 **FrontController**에서 처리하고, 서로 다른 코드들만 각 Controller에서 처리할 수 있도록 한다.
- 따라서, FrontController 외에 **다른 Controller**에서는 **Servlet이 필요 없다**.
    - 요청에 맞게 Servlet과 URL 매핑해주는 역할은 프론트컨트롤러에서 한번만 하면 되기 때문이다.
- 스프링 웹 MVC의 FrontController의 역할을 **DispatcherServlet**이 수행한다.

### 2. **Dispatcher Servlet**

- 특징:
    - Java를 활용하여 웹페이지를 동적으로 생성하는 Servlet의 일종으로, HTTPServlet을 상속받아서 사용된다.
    - 스프링 부트를 사용하면 DispatcherServlet을 서블릿으로 자동 등록하면서 내장 톰캣을 띄운다.
    - 스프링 부트를 사용하지 않는 상황에서는 일반적으로 ****web.xml이나 JavaConfig에서 수동 설정하여 사용한다.
- 역할:
    - 모든 요청 중앙집중적 관리
        - DispatcherServlet은 Servlet WebApplicationContext와 Root WebApplicationContext를 생성하여 컴포넌트(Spring이 관리하는 Special Bean)에 실제 작업을 위임한다.
            - handlerMapping:
                - URL 패턴관리
                    - 별도의 설정이 없으면 기본적으로 모든 요청을 인터셉트하여, 각 요청을 적절한 핸들러에 매핑한다.
                        - 단, 정적 리소스에 대한 요청과 애플리케이션에 관한 요청으로 클라이언트 요청을 분리하여 처리한다.
                            - /resources와 같은 URL로 들어오는 정적 리소스 요청은 보통 웹 서버(Apache, Nginx) 또는 서블릿 컨테이너(Tomcat)에서 처리한다.
            - handlerAdapter:
                - 다양한 컨트롤러(핸들러) 지원
            - handlerExceptionResolver:
                - 요청 처리 중에 발생하는 예외 처리
            - viewResolver:
                - 컨트롤러 반환 값으로부터 뷰 컴포넌트 결정
                    - 예를 들어 String 타입의 뷰의 논리적인 이름(파일명)을 파라미터로 받았을 때, 이를 modelAndView와 같은 객체로 반환한다.
            - LocaleResolver:
                - 유저의 언어,지역,출력형식 등을 정의하는 문자열인 Locale을 결정
            - MultipartResolver:
                - HTTP 응답의 본문을 여러 파트로 나누어 멀티파트 파일 업로드 처리

### 3. 결론

- Spring MVC에서 Dispatcher Servlet은 ****가장 앞단에서 HTTP 프로토콜로 들어오는 모든 요청을 먼저 받아 적합한 컨트롤러에 위임해주는 프론트 컨트롤러로,

### References

- [[MVC] 프론트 컨트롤러 패턴](https://yeonyeon.tistory.com/103)
- [[Spring] Dispatcher Servlet 이해하기](https://velog.io/@betterfuture4/Spring-Dispatcher-Servlet-%EC%A0%95%EB%A6%AC)
- [[Spring] 스프링 디스패처 서블릿 (Dispatcher Servlet)](https://ggop-n.tistory.com/72)
