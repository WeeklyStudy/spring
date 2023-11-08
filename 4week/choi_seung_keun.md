# #10 MVC패턴이란 무엇이고, MVC1과 MVC2의 차이는?

## 1. MVC 패턴이란?

> 애플리케이션을 구성하는 구성 요소들을 세 가지 역할로 구분하는 방법으로 model, view, controller로 역할을 구분한다.

### 1-1) MVC 패턴의 역할

- Model
    - 애플리케이션의 데이터 및 비즈니스로직을 나타냄
    - 데이터 변경, 관리 등 `정보의 가공` 및 `비즈니스 업무 로직 처리` 역할
    - View, Controller에 대해서 어떠한 정보를 알지 않아야 함
        
        ⇒ 서로 다른 계층끼리 의존하지 않음
        
- View
    - 데이터의 시각적 표현을 담당
    - UI(사용자 인터페이스)를 구성하고 모델의 데이터를 사용자에게 표시하는 역할
    - 모델의 변경을 감지하여 UI 업데이트 등 작업 진행
    - Model 정보를 따로 저장해서는 안됨
    - Model, Controller 등 다른 구성 요소에 대해 알지 않아야 함
- Controller
    - Model, View 사이의 중개하는 중개자 역할
    - 사용자 입력 처리 등 Model이 데이터를 어떻게 처리할지 알려주는 역할
    - 요청을 받아 적절한 모델 호출하고, 결과를 뷰로 전달
    - Model, View에 대해서 알고 있어야 함

### 1-2) 장점

- 데이터, 표현, 제어로직 등 역할에 따라 분리함으로써 애플리케이션을 모듈화하고 유지보수를 용이하게 함
- 구성요소끼리 응집성있고 결합도가 낮기 때문에 각 요소는 독립적으로 개발 및 테스트 가능하며 변경 사항이 다른 부분에 미치는영향을 최소화할 수 있음

</br>

## 2. MVC1, MVC2 차이와 장단점

MVC패턴은 MVC1패턴과 MVC2 패턴으로 나눌 수 있다.

### 2-1) MVC1
![image](https://github.com/WeeklyStudy/spring/assets/77659341/cba8aa2d-4c42-4997-8932-3548e41b13bf)

- 특징
    - View, Controller 모두 JSP가 담당하는 형태
    - JSP 가 유저의 요청을 받고 응답을 처리
- 장점
    - 구현 난이도가 쉽다.
- 단점
    - 재사용성이 떨어지고 가독성이 좋지 않음
    - 유지보수의 문제가 발생

### 2-2) MVC2
![image](https://github.com/WeeklyStudy/spring/assets/77659341/3a655bc4-c253-49ba-b3a9-3c82d11d6a77)

- 특징
    - 표준으로 사용되는 패턴
    - 요청을 컨트롤러가 받으며, View와 Controller 분리되어 있음
- 장점
    - Model, View, Controller 의 역할이 분리되어 있어 유지보수에 용이
- 단점
    - 해당 패턴을 직접 구현하는 것에는 시간이 걸림

</br>

## 참고자료
- [**[Spring] Spring의 MVC 패턴과 MVC1과 MVC2 비교**](https://chanhuiseok.github.io/posts/spring-3/)

</br>

# #11 어노테이션 @Controller와 @RestController의 작동방식의 차이는 무엇인가

## 1. @Controller 작동 방식

### 1-1) View를 반환

@Controller 어노테이션 사용시 주로 사용하는 방식으로 `View 반환`하기 위해 사용

![image](https://github.com/WeeklyStudy/spring/assets/77659341/3c934ea3-7130-4fa4-92dd-b6f8e95a49d8)

- 동작과정
1. DispatcherServlet이 클라이언트로부터 요청을 받음
2. 요청 URL 을 HandlerMapping에 넘겨 호출할 핸들러(컨트롤러) 정보를 얻는다.
3. HandlerAdapter 객체를 가져온다
4. HadlerAdapter 객체의 메소드를 실행한다.
5. Controller는 비즈니스 로직을 처리하고 View에 전달할 Model 객체를 저장
6. Controller는 HandlerAdapter에 ViewName 반환
7. DispatcherServlet에 ModelView를 반환
8. DispatcherServlet은 ViewName을 ViewResolver에 전달
9. ViewResolver 통해 View 객체 얻음
10. View 객체는 뷰(JSP, Thymeleaf)를 호출하며, 뷰는 model 객체에서 화면 표시에 필요한 데이토 가져와 화면표시 처리

### 1-2) 데이터를 반환

@Controller 어노테이션 사용하며 `Data 반환`하기 위해 사용 가능

⇒ @ResponseBody 어노테이션 사용하여 데이터 반환(뷰 X)할 수 있도록 함

⇒ 단순 텍스트, JSON, XML 등 형태로 반환 가능

![image](https://github.com/WeeklyStudy/spring/assets/77659341/566d6e17-0713-46be-9987-33334bfd58b3)

- 동작과정
1. DispatcherServlet이 클라이언트로부터 요청을 받음
2. 요청 URL 을 HandlerMapping에 넘겨 호출할 핸들러(컨트롤러) 정보를 얻는다.
3. HandlerAdapter 객체를 가져온다
4. HadlerAdapter 객체의 메소드를 실행한다.
5. Controller는 비즈니스 로직을 처리
6. Controller는 HttpMessageConverter 통해 변환한 데이터를 반환
7. 변환된 데이터(Json, xml 등)를 클라이언트에게 반환
- 특징
    - 뷰 대신 데이터를 반환하기 위해 HttpMessageConverter 사용된다.
    - 데이터의 종류에 따라 StringHttpMessageConverter, MappingJackson2HttpMessageConverter 사용됨
    - HTTP Accept 헤더와 컨트롤러 반환 타입 정보 조합하여 HttpMessageConverter 선택하여 처리
    - MessageConverter가 동작하는 시점은 HandlerAdapter와 Controller가 요청과 응답을 주고 받는 시점(직렬화, 역직렬화)
        - 클라이언트 요청으로 Json 등 데이터가 들어왔다면 컨트롤러에 객체로 변환하여 전달
        - 컨트롤러에서 처리한 반환 값을 객체에서 데이터로 변환하여 전달
- 중요한점
    - 뷰가 아닌 데이터를 응답하기 위해서 @ResponseBody 어노테이션 필요
    
</br>

## 2. @RestController 작동방식

> @RestController는 @Controller에 @ResponseBody가 추가된 형태

⇒ 모든 메서드에 @ResponseBody 적용되기 때문에 `Data 형태로 반환`하기 위해 사용된다.

![image](https://github.com/WeeklyStudy/spring/assets/77659341/c3e14c73-8a42-4064-bf27-5bdd4a42b11d)

- 동작과정
    @Controller + @ResponseBody 붙인 것과 완벽히 동일하게 동작한다.
    
</br>

## 3. 정리

- 뷰를 반환하기 위해서는 단순히 @Controller 만을 사용하면 된다.
- 데이터를 반환하기 위해서는 @Controller + @ResponseBody 를 사용하면 된다.
- 데이터를 반환하는 방식에서는 뷰와 관련된 ViewResolver가 사용되지 않으며, 응답 형태의 데이터로 변환하기 위해 `HttpMessageConverter`가 사용된다.
- @RestController는 @Controller + @ResponseBody의 형태이기 때문에 `데이터를 반환하는 방식`으로 동작한다.

</br>

## 참고자료

- [**[Spring] @Controller와 @RestController 차이**](https://mangkyu.tistory.com/49)*
- [**Spring MVC 동작원리 / 구성요소**](https://starkying.tistory.com/entry/Spring-MVC-%EB%8F%99%EC%9E%91%EC%9B%90%EB%A6%AC-%EA%B5%AC%EC%84%B1%EC%9A%94%EC%86%8C)

</br>

# #12 Spring MVC에서 DispatcherServlet의 역할을 무엇인가?

## 1. DispatcherServlet

HTTP 프로토콜로 들어오는 요청을 가장 먼저 받아 적합한 컨트롤러에 위임해주는 프론트 컨트롤러

즉, DispatcherServlet은 클라이언트의 모든 요청을 받아 처리해주는 역할

## 2. 하는 일

1. HTTP 요청 처리
    - 클라이언트의 HTTP 요청을 받아들이고 요청을 처리할 핸들러(컨트롤러)를 결정
2. 핸들러(컨트롤러) 선택
    - 요청을 어떤 핸들러가 처리할지 결정하기 위해 핸들러 매핑을 사용
    - URL 패턴, 요청메서드와 관련된 컨트롤러를 찾아냄
3. 핸들러 어댑터 찾아서 전달
    - 컨트롤러로 요청을 위임할 수 있는 HandlerAdapter 찾음
4. 핸들러 어댑터를 통한 컨트롤러 요청 위임
    - 핸들러 어댑터를 통해 선택된 컨트롤러에 요청을 전달함
    - 컨트롤러에 필요한 데이터들을 전달하기 위한 MessageConverter  실행등
    - 컨트롤러에서 비즈니스 로직처리 및 View에 전달할 Model 데이터를 생성
    - ViewName 반환 받음
5. View 선택
    - ViewName을 기반으로 ViewResolver와 상호작용하여 뷰 객체를 얻는다.
6. 응답 생성
    - 렌더링된 뷰를 사용하여 HTML, JSON, XML 등의 형식으로 응답을 변환하고, 클라이언트에게 응답을 전송

## 3. 정리

- DispatcherServlet은 MVC 구조에서 클라이언트 요청을 가장 먼저 받아 처리하는 프론트 컨트롤러의 역할을 한다.
- DispatcherServlet은 HTTP 요청을 처리하기 위해 다양한 구성요소와 상호작용한다.
- 애플리케이션의 요청을 처리하고 응답을 생성하는 핵심 역할을 함
