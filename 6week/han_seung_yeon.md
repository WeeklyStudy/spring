# https://github.com/WeeklyStudy/spring/issues/16 필터와 인터셉터란?

## 💡필터와 인터셉터란?

필터와 인터셉터는 코드 중복을 줄일 목적으로, 공통 작업을 처리할 때 사용한다.

### 1. 필터의 개념

> `Dispatcher Servlet`에 요청이 전달되기 전/후에 url 패턴에 맞는 모든 요청에 대해 부가 작업을 처하는 기능을 제공한다.
> 

### 2. 인터셉터의 개념

> `Dispatcher Servlet`이 `Controller`를 호출하기 전/후에 요청과 응답에 대한 부가작업을 처리하는 기능을 제공한다.
> 

### 3. 필터와 인터셉터의 차이

> 📍**필터와 인터셉터 흐름**
> 
> - HTTP 요청 → WAS → `필터` → DispatcherServlet → `인터셉터` → 컨트롤러
> 
> ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/7faad4fc-02ab-4e5e-9105-126c226c9bb6/1fa32e94-87fb-412d-8374-52e7976abacf/Untitled.png)
> 
1. 필터는 `웹 컨테이너(톰캣)`에 의해 관리되고, 인터셉터는 `스프링 컨테이너`에 의해 관리된다.
2. 필터는 스프링에 의한(`ControllerAdvice`와 `ExceptionHandler`를 이용한) 예외처리가 불가능하고, 인터셉터는 스프링에 의한 예외처리가 가능하다.
3. 필터는 요청/응답 객체를 조작할 수 있지만, 인터셉터는 조작할 수 없습니다.
    - 필터는 `chain.doFilter(request, response)` 메서드를 호출해서 다음 필터를 호출하는 방식이다.
        
        ```java
        public class MyFilter implements Filter {     
            @Override
            public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)    throws IOException, ServletException {        
                // 다른 request와 response를 넣어줄 수 있음        
                chain.doFilter(request, response);    
            }
        }
        ```
        
    - 인터셉터는 `DispatcherServlet`이 순차적으로 인터셉터를 호출하는 방식이다. true를 반환하면 다음 인터셉터가 실행되거나 컨트롤러로 요청이 전달되고, false를 반환하면 요청이 중단된다.
        
        ```java
        public class MyInterceptor implements HandlerInterceptor {     
            @Override
            public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)     throws Exception {        
                // Request, Response를 교체할 수 없고 boolean 값만 반환 가능        
                return true;    
            }
        }
        ```
        
4. 필터는 스프링과 무관하게 처리해야하는 작업인 경우에 사용하고, 인터셉터는 스프링을 사용할 수 있는 환경에서 사용한다.
    - 필터 : 이미지 압축, 문자열 인코딩
    - 인터셉터 : 인증/인가
5. 필터는 단순하게 DispatcherServlet 호출 전/후를 위한 `doFilter()` 하나만 제공하지만, 인터셉터는 컨트롤러 호출 전에 `preHandle()`, 호출 후에`postHandle()`, 요청 완료 이후에`afterCompletion()` 로 세분화된 메서드를 제공한다.

## Reference

- [[Spring] 필터(Filter)와 인터셉터(Interceptor)의 개념 및 차이](https://dev-coco.tistory.com/173)
- [[Spring] Filter, Interceptor, AOP 차이 및 정리](https://goddaehee.tistory.com/154)
- [Filter를 사용하면 더 좋은 경우가 있을까요?](https://www.inflearn.com/questions/279262/filter%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%A9%B4-%EB%8D%94-%EC%A2%8B%EC%9D%80-%EA%B2%BD%EC%9A%B0%EA%B0%80-%EC%9E%88%EC%9D%84%EA%B9%8C%EC%9A%94)
- [ArgumentResolver 에서 null 체크](https://www.inflearn.com/questions/1071936/argumentresolver-%EC%97%90%EC%84%9C-null-%EC%B2%B4%ED%81%AC)
- [인터셉터 로그 순서 문의](https://www.inflearn.com/questions/1051169/%EC%9D%B8%ED%84%B0%EC%85%89%ED%84%B0-%EB%A1%9C%EA%B7%B8-%EC%88%9C%EC%84%9C-%EB%AC%B8%EC%9D%98)
- [spring security 사용 vs interceptor 구현](https://www.inflearn.com/questions/614369/spring-security-%EC%82%AC%EC%9A%A9-vs-interceptor-%EA%B5%AC%ED%98%84)
- [[Spring] 필터(Filter) vs 인터셉터(Interceptor) 차이 및 용도 - (1)](https://mangkyu.tistory.com/173)

# https://github.com/WeeklyStudy/spring/issues/17 쿠키와 세션이란?

## 💡쿠키

> 웹 사이트에 접속할 때 생성되는 정보를 담은 작은 파일이다.
> 

### 1. 특징

- 클라이언트에 text 형태로 저장된다.
- 만료 날짜를 지정할 수 있다.
    - 영속 쿠키: 만료 날짜를 설정하면, 해당 날짜까지 유지
    - 세션 쿠키: 만료 날짜를 생략하면, 브라우저 종료하기 전까지 유지
- 쿠키는 탈취, 변조의 위험이 있어 보안이 취약하다. → 쿠키에 중요한 정보를 저장하면 안된다!
- 쿠키는 정보를 바로 참조하면 되기 때문에 속도가 빠르다.

### 2. 동작 방식

1. 클라이언트가 로그인에 성공한다.
2. 서버는 쿠키를 생성해서 응답한다.
3. 클라이언트는 쿠키를 로컬 PC(브라우저)에 저장한다.
4. 클라이언트는 이후 모든 요청에 쿠키를 담아 요청을 보낸다.
5. 서버는 쿠키값을 읽어서 요청을 처리하고 응답한다.

## 💡세션

> 같은 사용자로부터 들어오는 요청들을 하나의 상태로 유지하는 기술이다.
> 

### 1. 특징

- 서버에 Object 형태로 저장된다.
- 세션은 중요 정보를 서버에 저장하기 때문에 보안이 좋다.
    - 예측 불가능한 고유한 세션ID를 사용하기 때문에, 변경해도 문제가 없다.
    - 세션을 강제로 날릴 수 있다.
- 사용자가 많아질 경우 서버 메모리 사용량이 늘어나 성능이 저하될 수 있다.
- 세션ID로 서버에서 세션 정보를 조회하기 때문에, 쿠키보다 속도가 느리다.

### 2. 동작 방식

1. 클라이언트가 로그인에 성공한다.
2. 서버는 (고유한 세션ID, 정보)를 세션 저장소에 저장하고 세션ID를 쿠키에 담아 응답한다.
3. 클라이언트는 세션ID가 담긴 쿠키를 로컬 PC(브라우저)에 저장한다.
4. 클라이언트는 이후 모든 요청에 세션ID가 담긴 쿠키를 담아 요청을 보낸다.
5. 서버는 쿠키에 담긴 세션ID로 세션 저장소의 세션 정보를 조회해서 요청을 처리하고 응답한다.

## 💡정리

- 쿠키는 클라이언트에 text 형태로 저장되고, 세션은 서버에 Object 형태로 저장된다.
- 쿠키는 탈취, 변조의 위험이 있어 보안이 취약하지만, 세션은 중요 정보를 서버에 저장하기 때문에 보안이 좋다.
- 쿠키는 정보를 바로 참조하면 되기 때문에 속도가 빠르지만, 세션은 세션ID로 서버에서 세션 정보를 조회하기 때문에 속도가 느리다.

## Reference

- [쿠키(Cookie)와 세션(Session)의 차이 (+캐시(Cache))](https://dev-coco.tistory.com/61)
- [[Code.presso] 쿠키와 세션🤔❓개념과 활용 예시를 알아보자.](https://velog.io/@sorzzzzy/Code.presso-%EC%BF%A0%ED%82%A4%EC%99%80-%EC%84%B8%EC%85%98%EA%B0%9C%EB%85%90%EA%B3%BC-%ED%99%9C%EC%9A%A9-%EC%98%88%EC%8B%9C%EB%A5%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)
- [인증 방식(쿠키 & 세션 & jwt)](https://velog.io/@narangke3/%EC%9D%B8%EC%A6%9D-%EB%B0%A9%EC%8B%9D%EC%BF%A0%ED%82%A4-%EC%84%B8%EC%85%98-jwt)

# https://github.com/WeeklyStudy/spring/issues/18 스프링 인터셉터는 어떻게 동작하는가?

## 💡스프링 인터셉터

### 1. **인터셉터(HandlerInterceptor)의 메서드**

```java
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

- `preHandle()` : 컨트롤러(핸들러 어댑터) 호출 전에 실행된다.
    - 전처리 작업, 요청 정보 가공/추가하는 경우에 사용한다.
    - 응답값이 true면 다음 인터셉터/핸들러 어댑터를 호출하고, false면 중단된다.
- `postHandle()` : 컨트롤러(핸들러 어댑터) 호출 후에 실행된다.
    - 컨트롤러 하위 계층에서 예외가 발생하면 호출되지 않는다.
- `afterCompletion()` : 뷰 렌더링 이후 모든 작업이 완료된 후에 실행된다.
    - 컨트롤러 하위 계층에서 예외가 발생하더라도 항상 호출된다.

### 2. 스프링 인터셉터 동작 방식

1. `DispatcherServlet`으로 클라이언트의 HTTP 요청이 들어온다.
2. `DispatcherServlet`은 `HandlerMapping`을 통해 해당 요청을 처리할 `Controller`를 찾는다.
3. `DispatcherServlet`은 `Controller`를 실행할 수 있는 `HandlerAdapter`를 찾는다.
4. `**DispatcherServlet`은 인터셉터의 `preHandle()`를 호출한다.**
    1. **응답값이 true면 다음 인터셉터/핸들러 어댑터를 호출하고, false면 중단한다.**
5. `HandlerAdapter`는 `Controller`의 메서드를 호출한다.
6. `Controller`는 비즈니스 로직을 처리하고 결과를 `Model`에 저장하고 `view name`을 리턴한다.
7. `**DispatcherServlet`은 인터셉터의 `posHandle()`을 호출한다.**
8. `DispatcherServlet`은 `ViewResolver`을 통해 `view name`에 해당하는 `View`를 찾는다.
9. `DispatcherServlet`은 `View`에게 `Model`을 전달하고 화면 표시를 요청한다.
    - `Model`이 null이면 `View`를 그대로 사용하고, null이 아니면 `View`에 `Model` 데이터를 렌더링한다.
10. `**DispatcherServlet`은 인터셉터의 `afterCompletion()`을 호출한다.**
11. `DispatcherServlet`은 `View 결과(HttpServletResponse)`를 클라이언트에게 반환한다.

## 💡정리

1. `HandlerMapping`을 통해 `Controller` 조회
2. `Controller`를 실행할 수 있는 `HandlerAdapter` 조회
3. **인터셉터 `preHandle()` 호출**
4. `Controller` 호출
5. **인터셉터 `posHandle()` 호출**
6. View 렌더링
7. **인터셉터 `afterCompletion()` 호출**

## Reference

- [[Spring] 필터(Filter) vs 인터셉터(Interceptor) 차이 및 용도 - (1)](https://mangkyu.tistory.com/173)
- [[Spring MVC] 인터셉터는 어떻게 동작하는가(feat. HandlerExecutionChain, 인터셉터의 예외처리)](https://ttl-blog.tistory.com/1282)
- [[Spring] 스프링 Interceptor의 동작 과정](https://livenow14.tistory.com/61)
