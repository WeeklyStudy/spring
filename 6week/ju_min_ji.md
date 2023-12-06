## https://github.com/WeeklyStudy/spring/issues/16 **필터와 인터셉터란?**

웹 관련된 관련된 공통 관심사는 HTTP의 헤더나 URL의 정보들이 제공받을 수 있는 서블릿 필터나 스프링 인터셉터를 사용하면 유용하다.

### 1. 필터

<img src="https://github.com/WeeklyStudy/spring/assets/63441091/534285b7-010f-4514-9cd9-55c6db46bf18" width="650">

- 개념:
    - J2EE 표준 스펙 기능으로 Dispatcher Servlet에 요청이 전달되기 전/후에 url 패턴에 맞는 모든 요청에 대해 부가작업을 처리할 수 있는 기능을 제공한다.
- 호출 흐름:
    - HTTP 요청 ->WAS-> **필터** -> 서블릿 -> 컨트롤러
- 특징:
    - 모든 고객의 요청 로그를 남기는 요구사항이 있다면,   `/*` URL 패턴을 설정하여 모든 요청에 필터를 적용할 수 있다.
    - 특정 URL 패턴을 사용하여 인증/인가 처리 등을 할 수 있다.
    - Spring Context 바운더리 내에 존재하지 않는 필터는 기본적으로는 **Spring Bean을 사용하지 못한다.**
        - 하지만, DelegationProxyFilter 등장 이후로는 빈 등록도 가능해졌다.
- 구현:
    - javax.sevlet의 Filter 인터페이스를 구현해야 하며 3가지 메서드를 가지고 있다.
        
        ```java
        public interface Filter {
        
            public default void init(FilterConfig filterConfig) throws ServletException {}
            
            public void doFilter(ServletRequest request, ServletResponse response,
                    FilterChain chain) throws IOException, ServletException;
                    
            public default void destroy() {}
        
        }
        ```
        
        - `init()`:
            - 필터 객체를 초기화하고 서비스에 추가하기 위해 1회만 호출되는 메서드이다.
        - `doFilter()`:
            - `init()` 메서드가 호출된 이후의 url 패턴에 맞는 모든 요청들은 해당 메서드를 통해 처리된다.
            - doFilter의 파라미터로는 FilterChain이 있는데, FilterChain의 doFilter 통해 다음 대상으로 요청을 전달하게 된다.
        - `destroy()`:
            - destroy 메소드는 필터 객체를 서비스에서 제거하고 사용하는 자원을 반환하기 위해 1회만 호출되는 메서드이다.
- 용도:
    - **Spring Context에서 MVC로 요청을 처리하기 전**에, 혹은 **Spring과 무관하게 전역적으로** 처리해야 하는 **공통로깅** 작업, **인증 및 인가**(CORS 정책 준수여부, JWT 등), **XSS방어**, **인코딩**(UTF-8) 등에 사용된다.

### 2. 인터셉터

<img src="https://github.com/WeeklyStudy/spring/assets/63441091/1b472115-d98b-4a6d-ba7d-618a89a03e4c" width="650">

- 개념:
    - Spring이 제공하는 기술로써, 디스패처 서블릿(Dispatcher Servlet)이 컨트롤러를 호출하기 전과 후에 요청과 응답을 참조하거나 가공할 수 있는 기능을 제공한다.
- 호출 흐름:
    - HTTP 요청 ->WAS-> 필터 -> 서블릿 -> **스프링 인터셉터** -> 컨트롤러
        - 스프링 MVC의 시작점이 DispatcherServlet이기 때문에, 스프링 인터셉터는 디스패처 서블릿 이후에 작동된다.
        - DispatcherServlet은 핸들러 매핑을 통해 적절한 컨트롤러를 찾도록 요청하는데, 그 결과로 실행 체인(HandlerExecutionChain)을 돌려준다.
        - 실행체인에 1개 이상의 인터셉터가 등록되어 있다면 순차적으로 인터셉터들을 거쳐 컨트롤러가 실행되도록 하고, 인터셉터가 없다면 바로 컨트롤러를 실행한다.
- 특징:
    - Spring Context 내부에 있기 때문에 빈 조회 및 사용이 자유롭다.
    - 필터보다 정밀한 URL 패턴 설정이 가능하다.
- 등록 및 구현:
    - 인터셉터 등록:
        
        ```java
        @Configuration // 생성한 인터셉터 빈 등록
          public class WebConfig implements WebMvcConfigurer {
              @Override
              public void addInterceptors(InterceptorRegistry registry) {
                  registry.addInterceptor(new LogInterceptor())
                          .order(1) // 인터셉터 호출 순서
        									.addPathPatterns("/**") // 적용 패턴
        									.excludePathPatterns("/css/**", "/*.ico", "/error"); // 제외 패턴
             }
        //...
        }
        ```
        
        - `WebMvcConfigurer` 가 제공하는 `addInterceptors()` 를 사용해서 인터셉터를 등록할 수 있다.
    - 디폴트 메서드 override하여 구현:
        
        ```java
        public interface HandlerInterceptor
        
            default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        	throws Exception {
                return true;
            }
        
            default void postHandle(HttpServletRequest request, HttpServletResponse response,
        	Object handler, @Nullable ModelAndView modelAndView) throws Exception {
            }
        
            default void afterCompletion(HttpServletRequest request, HttpServletResponse response,
        	Object handler, @Nullable Exception ex) throws Exception {
            }
        }
        ```
        
        - `preHandle()`: 컨트롤러 호출 전 실행되는 메서드이다.
        - `postHandler()` : 컨트롤러 호출 후 실행되는 메서드이다.
        - `afterCompletion()` : 요청이 완료된 후 실행되며 뷰가 렌더링 된 후에 실행된다.
- 용도:
    - **Spring Context에서 처리하는 다양한 로직들**을 컨트롤러 실행 전/후, 요청 완료 시점에 맞춰서 Controller로 넘어오는 **파라미터 데이터** 전처리 및 가공, **인증 및 인가**, API 호출에 대한 **로깅** 등에 상용된다.

### 3. 필터와 인터셉터 차이

- 서블릿 필터와 비교해서 스프링 인터셉터가 개발자 입장에서 훨씬 편리하고 세밀하게 처리할 수 있다.
- 하지만 기본적으로 **필터**는 **Web Contex**t 내부에, **인터셉터**는 Web Context 내부이자 구체적으로 **Spring Context** 내부에 있기 때문에 이들을 **함께 사용하는 경우**에는 이에 대한 **관심사**를 **분리**하기 위함이라고 생각한다.
- 즉, 인터셉터에 비해 **필터**는 **HTTP 요청과 응답의 수준**에서 처리해야 하는 작업에 더 적합하다고 보는 것이다.
    - 예시로, **Spring Security**는 인터셉터를 활용해서 구성할 수도 있기는 하지만, 기본적으로 `FilterChainProxy`라는 클래스를 통한 **필터** 기반 아키텍처이다.
    - `DelegatingFilterProxy`을 통해 `FilterChainProxy`를 빈으로 등록 후, 여러 보안 관련 필터들을 관리하고 이 필터들을 서블릿 컨테이너의 필터 체인에 연결하여 보안을 적용한다.
    - `FilterChainProxy`는 스프링 시큐리티의 중요한 부분 중 하나로, 여러 보안 관련 필터들을 관리하고 이 필터들을 서블릿 컨테이너의 필터 체인에 연결하여 보안을 적용한다.

### References
- [[Spring Doc] Spring Security](https://docs.spring.io/spring-security/site/docs/4.2.1.RELEASE/apidocs//index.html?org/springframework/security/web/FilterChainProxy.html)
- [[Spring] 필터(Filter)와 인터셉터(Interceptor) 차이](https://mozzi-devlog.tistory.com/9)
- [Filter와 Interceptor 용도 차이 Q&A](https://okky.kr/questions/1346808)
- [[Spring] 필터(Filter) vs 인터셉터(Interceptor) 차이 및 용도 - (1)](https://mangkyu.tistory.com/173)
- [Filter와 Interceptor 차이](https://algopoolja.tistory.com/110)
- [Spring Security에 대해서 알아보자(동작 과정편)](https://velog.io/@younghoondoodoom/Spring-Security%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90%EB%8F%99%EC%9E%91%EA%B3%BC%EC%A0%95%ED%8E%B8)

## https://github.com/WeeklyStudy/spring/issues/17 **쿠키와 세션이란?**

### 1. HTTP

- **Stateless**
    - HTTP는 요청은 즉 상태를 관리하지 않는다는 특징이 있다.
- **Connectless**
    - 클라이언트가 요청을 한 후 응답을 받으면 그 연결을 끊어 버리는 특징이 있다.
    - 즉, 각각의 요청은 독립적이고 서버 입장에서는 클라이언트를 식별한다거나, 이전 요청에 대해 연관지어 응답하지 않는다.
- 위 특징들을 보완하는 것이 쿠키와 세션이다.

### 2. 쿠키

- 개념:
    - **클라이언트(브라우저)** **로컬**에 저장되는 키와 값이 들어있는 작은 데이터 파일이다.
    - 사용자 인증이 유효한 시간을 명시할 수 있으며, 유효 시간이 정해지면 브라우저가 종료되어도 인증이 유지된다는 특징이 있다.
    - 클라이언트에 300개까지 쿠키저장 가능하고, 하나의 도메인당 20개의 값만 가질 수 있으며, 하나의 쿠키값은 4KB까지 저장 가능하다.
    - 쿠키는 사용자가 따로 요청하지 않아도 브라우저가 Request시에 Request Header를 넣어서 자동으로 서버에 전송한다.
- 동작방식:
    1. 클라이언트가 페이지를 요청한다.
    2. HTTP 응답 Set-Cookie 헤더를 사용하여 클라이언트에 쿠키를 생성하고 전송한다.
    3. 브라우저가 종료되어도 쿠키 만료 기간이 있다면 클라이언트에서 보관하고 있으나, 만료 날짜를 설정하지 않으면 세션 쿠키로 간주되어 브라우저가 닫힐 때 삭제된다.
    4. 같은 HTTP 요청을 보낼 때, 클라이언트는 서버에 해당 도메인과 연관된 모든 쿠키를 요청과 함께 전송한다.
    5. 서버에서 쿠키를 읽어 이전 상태 정보를 변경 할 필요가 있을 때 쿠키를 업데이트 하여 변경된 쿠키를 HTTP 헤더에 포함시켜 응답
- 용도:
    - 웹사이트의 선호 설정, 로그인 정보, 사용자 활동 추적 등 다양한 용도로 사용된다.

### 3. 세션

- 개념:
    - 세션은 쿠키를 기반하고 있지만, 세션은 **서버** 측에서 사용자 정보 파일을 관리한다.
    - 서버에서는 클라이언트를 구분하기 위해 고유한 세션 ID를 부여하며 웹 브라우저가 서버에 접속해서 브라우저를 종료할 때까지 인증상태를 유지한다.
    - 물론 접속 시간에 제한을 두어 일정 시간 응답이 없다면 정보가 유지되지 않게 설정이 가능하다.
    - 세션은 일반적으로 서버 메모리, 데이터베이스 또는 외부 저장소에 저장된다.
    - 사용자에 대한 정보를 서버에 두기 때문에 쿠키보다 보안에 좋지만, 사용자가 많아질수록 서버 **메모리** 문제와 **성능** **저하**를 일으킬 수 있다.
        - 그 중에서도 서버 메모리에 저장 시 다음과 같은 문제가 있다.
            - 세션은 스케일 아웃시 공유되지 않는다.
            - 서버에 저장되어있는 in-Memory에 접근해야하기때문에 트래픽이 몰린다면 서버가 복구될 때까지 서비스를 중단해야 하는 상황이 발생한다.
        - 또한 데이터 베이스의 경우, 읽기/쓰기 작업으로 인해 성능 저하가 발생할 수 있다.
        - 따라서, 이와 같은 문제들(**확장성, 성능**)을 고려하여 Redis와 같은 **외부 메모리**를 많이 사용한다.
- 동작방식:
    1. 클라이언트가 서버에 최초 요청을 보내면, 서버는 클라이언트에게 고유한 세션 ID를 할당한다.
    2. 클라이언트는 세션 ID에 대해 쿠키를 사용해서 저장하고 가지고 있음
    3. 클라리언트는 서버에 요청할 때, 이 쿠키의 세션 ID를 같이 서버에 전달해서 요청
    4. 서버는 세션 ID를 전달 받아서 별다른 작업없이 세션 ID로 세션에 있는 클라언트 정보를 가져와서 사용
    5. 클라이언트 정보를 가지고 서버 요청을 처리하여 클라이언트에게 응답
- 용도:
    - 로그인 같이 보안상 중요한 작업을 수행할 때 사용한다.

### 4. 쿠키와 세션 보안 이슈

- 쿠키 탈취 및 조작:
    - 쿠키는 일반적으로 클라이언트에 저장되기 때문에, 악의적인 공격자가 쿠키를 탈취하여 클라이언트로 위장하거나 조작할 수 있다. 중요한 정보를 담은 쿠키가 노출될 경우 클라이언트의 계정이나 세션을 손상시킬 수 있다.
        - 해결방안 1) 악의적인 스크립트를 사용하여 쿠키를 탈취할 수 있는 XSS(Cross Site Scripting) 공격을 방지하기 위해 **HTTP Only 쿠키**로 브라우저를 통해서는 쿠키에 접근할 수 없도록 제한할 수 있다.
        - 해결방안 2) 그러나 자바스크립트 코드 대신 네트워크를 직접 감청하는 등의 방식으로도 쿠키를 가로챌 수 있기 때문에 이를 방지하기 위해 HTTPS 프로토콜을 사용할 때에만 쿠키를 전달하는 **Secure 쿠키**를 사용하는 방법이 있다.
- 세션 하이재킹:
    - 클라이언트와 서버 간의 **세션 ID**를 탈취하여 공격자가 사용자로 위장하여 해당 세션을 이용하고, 사용자의 권한을 도용하여 데이터를 조작하는 공격이다.
        - 해결방안) 일정 시간 또는 중요한 작업 후 세션 ID를 변경하여 이를 방지할 수 있다.

### 5. 쿠키와 세션을 함께 사용하는 이유

- 세션은 쿠키보다 보안이 높다.
- 그럼에도 쿠키와 세션을 함께 사용하는 이유는 세션은 서버의 자원을 사용하기 때문에 무분별하게 만들다보면 서버의 메모리가 감당할 수 없어질 수가 있고 속도가 느려질 수 있기 때문에 보안이 중요하지 않은 경우 쿠키를 사용하는 것이 좋다.

### References

- [쿠키와 세션 개념](https://interconnection.tistory.com/74)
- [[네트워크] 쿠키와 세션, 토큰을 이용한 인증](https://ttl-blog.tistory.com/1305#%F0%9F%A4%94%20%EC%BF%A0%ED%82%A4%EC%99%80%20%EB%B3%B4%EC%95%88%20%EB%AC%B8%EC%A0%9C-1)

## https://github.com/WeeklyStudy/spring/issues/18 **스프링 인터셉터는 어떻게 동작하는가?**
### 1. 인터셉터의 기본 동작 3단계
<p align="center"> 
  <img src="https://github.com/WeeklyStudy/spring/assets/63441091/c4dfcf1c-dbab-46c3-85a8-115d3928ab6e" width="600">
</p>

- 스프링의 인터셉터의 동작은 크게 '컨트롤러 실행 전', '컨트롤러 실행 후, 뷰 실행 전', '뷰 실행 후' 이 세 단계로 구분된다.
- 스프링 인터셉터를 만들기 위해서는 `HandlerInterceptor` 인터페이스를 구현해야 한다.
- 해당 인터페이스는 `preHandle()`, `postHandle()`, `afterCompletion()` 이라는 세 메소드를 제공한다.
    1. `preHandle()`**:**           

        ![image](https://github.com/WeeklyStudy/spring/assets/63441091/e6c3b306-bbe2-48e5-89cf-9cf94e080528)
        - 실행시점:
            - 컨트롤러 호출 전
        - 용도:
            - 컨트롤러 이전에 처리해야 하는 **전처리 작업**이나 요청 정보를 가공하거나 추가할 때 유용하다.
        - 특징:
            - 세 번째 파라미터인 `handler` 객체 타입은 어떤 핸들러 매핑을 사용하는가에 따라 달라진다.
                - `HandlerInterceptor` 인터페이스에서는 `Object` 형태로 업 캐스팅해서 매개변수로 넘겨주는 것이고, 실제 사용할 때는 `instanceof` 연산자로 객체의 타입을 확인해서 다운 캐스팅 한 뒤 객체에 접근하면 된다.
                 -  `@Controller` ,`@RequestMapping`을 활용한 핸들러 매핑을 사용하는 경우, `HandlerMethod` 타입의 객체가, 정적 리소스가 호출 되는 경우, `ResourceHttpRequestHandler` 타입의 객체가 핸들러 객체로 넘어온다.
            - 반환 타입은 `boolean`인데 반환값이 `true`면 다음 단계로 진행이 되지만, **`false`라면 작업을 중단하여 이후의 작업(다음 인터셉터 또는 컨트롤러)은 진행되지 않는다.**
            - 따라서, `JwtInterceptor` 를 구현해서 인가/인증하는 경우,`false`를 반환하여 해당 Handler로 접근을 제한할 수 있다.
    2. `postHandle()` :      
        
        ![image](https://github.com/WeeklyStudy/spring/assets/63441091/9b6708cb-e2ee-4275-b9d5-ad9588210ad6)
        - 실행시점:
            - 컨트롤러 호출 후
        - 용도:
            - 컨트롤러의 비즈니스 로직이 **성공적**으로 처리된 이후에 필요한 후처리 작업이 있을 때 유용하다.
        - 특징:
            - 컨트롤러가 반환하는 데이터 정보와 뷰 정보를 가지고 있는 `modelAndView` 파라미터는 REST API에서는 사용되지 않는다.
            - 전통적인 웹 애플리케이션에서는 `ModelAndView`를 사용하여 서버 측 뷰 렌더링을 위한 정보를 다루지만, REST API에서는 JSON 형식으로 데이터를 직접 반환하기 때문이다.
    3. `afterCompletion()` :
        
        ![image](https://github.com/WeeklyStudy/spring/assets/63441091/dd684782-a21f-41f2-bd0e-c1ceb2a41175) 

        - 실행시점:
            - 뷰 렌더링 작업을 포함하여 모든 작업이 완료된 후에 실행된다.
        - 용도:
            - 요청 처리 중에 사용한 리소스(파일, 데이터베이스 연결 등)를 해제하거나 로깅 등에 사용하기에 적합하다.
### 2. 핸들러 예외 발생 시 인터셉터의 흐름

- 컨트롤러 단에서 예외가 발생했을 때 각 메서드들의 동작은 다음과 같다.
    1. `preHandle()`: 
 
       - 컨트롤러 호출 전에 사용되기 때문에 **영향을 받지 않는다.**
    2. `postHandle()` : 
        - **호출 되지 않는다.**
        - WAS로 예외가 전달되어, `/error`로 재요청하며, 500 페이지를 제공한다.
            - Controller나 그 뒤 계층에서 throw된 Exception은 `DispatcherServlet`이 일단 전달 받은 뒤, 다시 Servlet 밖의 Servlet Container가 처리하게 된다.
            - 따라서, Controller의 작업 중 발생한 Exception을 처리하는 결정 전략 객체인 `HandlerExcetpionResolver`가 등록되어 있지 않다면, 'HTTP Status 500 내부 서버 오류'와 같은 메시지가 브라우저 단에 노출된다.
    3. `afterCompletion()` : 
         - **`HandlerExcetpionResolver`** 을 **함께 사용하는 경우**와 **그렇지 않은 경우**에 따라 **호출 여부**가 결정된다.
            - 함께 사용하는 경우:
                - `ExceptionResolver`가 컨트롤러 단 예외 처리를 우선적으로 담당하기 때문에, 해당 예외 처리에 성공하면 `afterCompletion` 메서드가 호출되고, 예외 처리에 실패하면 `afterCompletion` 메서드는 호출되지 않는다.
            - 함께 사용하지 않는 경우:
                - 컨트롤러단 예외 발생 여부와 관계 없이 항상 호출된다.
                - 예외가 발생하면, 예외 정보(`ex` 파라미터)를 포함해서 호출하기 때문에 예외를 파라미터로 받아서 로그로 출력하거나, 예외와 무관하게 후처리해야 하는 공통 작업을 처리할 수 있다.

> #### 💡 스프링 MVC 인터셉터와 ExceptionResolver 함께 사용 시 예외 처리 비교
> 
> 1. **PageNotFound(404)**: 컨트롤러에서
>     - Interceptor: 미작동
>         - 404는 클라이언트의 요청 URL이 서버에서 찾을 수 없을 때 발생하는 HTTP 상태코드이기 때문에, 인터셉터 실행 전에 실행이 종료된다.
>     - ExceptionResolver: 미작동
>         - 인터셉터와 마찬가지로, 컨트롤러 처리하는 단까지 오기 전에 실행이 종료된다.
> 2. **PageFound, Not Allowed (405 등)**
>     - Interceptor: 미작동
>     - ExceptionResolver: 작동
>         - 405는 이미 컨트롤러나 핸들러가 요청을 받아들이고 있으나, 해당 요청 메소드를 처리할 수 없는 상황이기 때문에 컨트롤러 단에서 발생하는 예외로 간주되기 때문이다.
> 3. **Controller Exception(500)**:
>     - Interceptor.afterCompletion: 미작동
>     - ExceptionResolver: 작동
>         - 컨트롤러에서 발생한 예외는 ExceptionResolver에서 우선적으로 처리하기 때문이다.
> 4. **View Exception(500)**:
>     - Interceptor.afterCompletion(ex): 작동
>         - View 렌더링 중 예외가 발생하면 예외 객체가 afterCompletion으로 전달된다. 따라서, View 렌더링에 대한 예외 처리 등의 후속 작업을 수행할 수 있다.
>     - ExceptionResolver: 미작동.
>         - View 렌더링 과정에서 발생한 예외는 ExceptionResolver가 처리하지 않는다.

### References

- [[Spring Web] Filter & Interceptor](https://kloong.tistory.com/entry/Spring-Web-Filter-Interceptor)
- [[Spring] DispatcherServlet의 예외처리 전략](https://velog.io/@gillog/Java-HandlerInterceptor%EB%8B%A8-Exception-Handling%ED%95%98%EA%B8%B0HandlerExceptionResolver-ExceptionHandler#handlerinterceptor%EB%8B%A8-exception-handling)
- [Spring HandlerInterceptor를 활용하여 컨트롤러 중복 코드 제거하기](https://hudi.blog/spring-handler-interceptor/)
- [[Spring Web] Filter & Interceptor](https://kloong.tistory.com/entry/Spring-Web-Filter-Interceptor)
- [스프링 인터셉터](https://cs-ssupport.tistory.com/494)
- [스프링 MVC 2 - HandlerExceptionResolver 시작](https://5bong2-develop.tistory.com/388)
- [handler 메소드에서 발생하는 예외를 인터셉터에서 처리할 수 있는 방법 문의](https://groups.google.com/g/ksug/c/s1RU4uPUOjE?pli=1)
