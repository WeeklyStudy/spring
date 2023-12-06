# 필터와 인터셉터란?

## 1. 개념

공통된 작업을 처리하기 위해서 필터, 인터셉터, AOP 사용할 수 있음

### 필터

J2EE 표준 스펙이며, 클라이언트 요청 시 **WebContext 영역 안**에서 Dispatcher Servlet에 요청이 전달되기 전/후에 **URL 패턴에 맞는 모든 요청에 대해 부가 작업을 처리**할 수 있는 기능을 제공하는 하는 것

![image](https://github.com/WeeklyStudy/spring/assets/77659341/a94daaf6-14d1-482f-ba66-af3f79334e66)

### 인터셉터

스프링 MVC가 제공하는 기능이며, **Dispatcher Servlet 과 Controller 사이에서** **Controller 호출하기 전/후에서  부가 작업을 처리**하는 기능 제공하는 것

![image](https://github.com/WeeklyStudy/spring/assets/77659341/ef1b67c7-a2e6-466b-b9c3-a3f540a9a1fe)


- 인터셉터 존재하지 않는 경우 바로 컨트롤러 호출

## 2. 차이 & 용도

### 차이점

- 동작과정
    - 필터 : HTTP 요청 → WAS → 필터(1~n)  → 서블릿 → 컨트롤러
    - 인터셉터 : HTTP 요청 → WAS → 필터 → 서블릿 → 인터셉터(1~n) → 컨트롤러
- 구조
    - 필터
        
        ```java
        public interface Filter {
            public default void init(FilterConfig filterConfig) throws ServletException {}
            
            public void doFilter(ServletRequest request, ServletResponse response,
                    FilterChain chain) throws IOException, ServletException;
                    
            public default void destroy() {}
        }
        ```
        
        - init 메서드 : 필터 객체 초기화, 서비스 추가 하기 위해 사용
        - doFilter 메서드: 필터 처리 구현할 수 있으며 FilterChain 파라미터 사용하여 다음 필터로 요청 넘길 수 있음
        - destroy 메서드 : 필터 제거, 자원 반환으로 사용
    - 인터셉터
        
        인터셉터를 추가하기 위해서는 org.springframework.web.servlet의 HandlerInterceptor 인터페이스를 구현(implements)해야 함.
        
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
        
        인터셉터 기능 구현 실행 시점을 설정하여 구성가능하다.
        
        - preHandle
            - 컨트롤러 호출 전에 실행
            - 반환 타입이 boolean 타입으로 true일 경우 계속 진행, false일 경우 진행을 멈춘다. → 컨트롤러를 호출하지 못하도록 함
        - postHandle
            - HandlerAdaptor 호출 후에 실행(컨트롤러 호출된 후 실행)
        - afterCompletion
            - 요청이 완료된 후 실행되며 뷰가 렌더링 된 후에 실행된다.

### 용도

- 필터: 인증/인가 작업, 로깅 처리
- 세부적인 인증/인가 공통 작업, controller에서 처리할 데이터 가공

## 정리

![image](https://github.com/WeeklyStudy/spring/assets/77659341/57e9d53e-d4e9-4c2e-ada4-71a1909bf8da)

- 필터의 경우 웹 컨텍스트 안에서 실행, 인터셉터의 경우 스프링 컨텍스트 안에서 실행
- 필터의 경우 HttpServlet request / response 객체를 조작할 수 있지만 인터셉터의 경우 불가능

### 참고자료

- [[Spring] 필터(Filter)와 인터셉터(Interceptor) 차이](https://mozzi-devlog.tistory.com/9)
- [[Spring] 필터(Filter) vs 인터셉터(Interceptor) 차이 및 용도 - (1)](https://mangkyu.tistory.com/173)

</br>

#  쿠키와 세션이란?

## 1. 개념 & 특징

### 1-1. 쿠키와 세션의 필요성

HTTP 프로토콜의 Connectionless, stateless 특성을 보안하기 위해 사용

- Connectionless : 클라이언트의 요청에 대한 응답을 받으면 클라이언트와 서버의 연결을 끊는 특징
- stateless : 연결을 끊는 순간 통신이 끝나며 상태를 유지하지 않는 특징

### 1-2. 쿠키

클라이언트(브라우저) 로컬에 저장되는 키와 값이 들어있는 작은 데이터 파일

- 사용자 인증이 유효한 시간을 명시할 수 있음
- 유효 시간이 정해지면 브라우저가 종료되어도 인증이 유지됨
- 클라이언트의 상태 정보를 로컬에 저장했다가 서버에 요청 시 참조

구성 요소 : `이름`, `값`, `유효시간`, `도메인`, `경로`

### 1-3. 세션

서버측에 요청의 상태를 유지하는 기술 

- 세션은 쿠키를 기반하고 있지만 쿠키와 달리 세션은 서버 측에서 관리
- 클라이언트 구분을 위한 세션 ID를 부여
- 웹 브라우저가 서버에 접속해서 `브라우저를 종료`하거나 접속 시간에 제한을 두어 `일정 시간 응답이 없기 전`까지 인증상태를 유지
- 사용자에 대한 정보를 서버에 두어 쿠키보다 보안에 좋지만, 서버 메모리를 사용하기 때문에 세션이 많아질 수록 메모리 공간을 차지(리소스 차지)
- 서버 리소스 차지는 사용자 수에 따라 과부하 및 성능 저하의 요인이 됨

## 2. 동작과정

### 쿠키

1. 클라이언트가 페이지를 요청
2. 서버에서 쿠키를 생성
3. HTTP 헤더에 쿠키를 포함 시켜 응답
4. 브라우저가 종료되어도 쿠키 만료 기간이 있다면 클라이언트에서 보관하고 있음
5. 같은 요청을 할 경우 HTTP 헤더에 쿠키를 함께 보냄
6. 서버에서 쿠키를 읽어 이전 상태 정보를 변경 할 필요가 있을 때 쿠키를 업데이트 하여 변경된 쿠키를 HTTP 헤더에 포함시켜 응답

### 세션

1. 클라이언트가 서버에 접속하면 서버는 고유한 식별자를 생성하고 쿠키를 통해 클라이언트로 전송
2. 클라이언트는 쿠키를 저장하여 식별자를 통해 서버와의 상태를 유지
3. 서버는 세션에 필요한 정보를 저장하고 클라이언트가 가진 식별자를 통해 해당 세션을 찾아 처리

## 3. 보안 관련 이슈

### 쿠키

- 클라이언트 측에 저장되는 형태이므로 쿠키를 수정 및 조작 가능
- 쿠키를 탈취하거나 저정된 정보를 가져갈 수 있다.
- 주요 정보를 노출하지 않기 위해 암호화와 서명을 적용할 수 있다.

### 세션

- 세션 식별자를 탈취하여 다른 사용자의 세션에 접근하는 **세션 하이재킹**의 문제
- 유효한 세션의 식별자를 획득하여 세션을 조작할 수 있다.
- 세션의 유효시간을 설정하여 보안을 강화할 수 있음

## 참고자료

- ****[쿠키와 세션 개념](https://interconnection.tistory.com/74)****

</br>

# 스프링 인터셉터는 어떻게 동작하는가?
## 1. 스프링 인터셉터 구조

```
public interface HandlerInterceptor {

    default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws Exception {

        return true;
    }

    default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
        @Nullable ModelAndView modelAndView) throws Exception {
    }

    default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
        @Nullable Exception ex) throws Exception {
    }
}
```

- `preHandle()` :
    - 컨트롤러(핸들러 어댑터) 호출 전에 실행
    - 요청 정보 가공/추가하는 경우에 사용
    - 응답값이 true면 다음 인터셉터/핸들러 어댑터를 호출, 응답 값이 false면 중단
- `postHandle()` :
    - 컨트롤러(핸들러 어댑터) 호출 후에 실행
- `afterCompletion()` :
    - 뷰 렌더링 이후 모든 작업이 완료된 후에 실행
    - 컨트롤러 하위 계층에서 예외가 발생하더라도 항상 호출된다.

## 2. 스프링 인터셉터 동작 방식

1. DispatcherServlet으로 클라이언트의 HTTP 요청 전달
2. DispatcherServlet은 HandlerMapping을 통해 해당 요청을 처리할 Controller를 매핑
3. 이후 HandlerAdapter를 매핑
4. 인터셉터의 `preHandle()`를 호출
5. HandlerAdapter는 Controller 호출
6. Controller는 로직을 처리 결과를  반환
7. DispatcherServlet는 인터셉터의 posHandle()을 호출
8. DispatcherServlet은 View를 매핑
9. DispatcherServlet은 View에게 Model을 전달
10. DispatcherServlet은 인터셉터의 afterCompletion() 호출
11. DispatcherServlet은 응답을 클라이언트에게 반환
