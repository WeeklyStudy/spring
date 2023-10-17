# https://github.com/WeeklyStudy/spring-core-principles/issues/1 @Component와 @Bean의 차이점은 무엇인가?

## 💡스프링 빈 등록 방법

### **1. 컴포넌트 스캔과 자동 의존관계 설정(자동 빈 등록)**

1. 클래스 선언부 위에 `@Component` 어노테이션을 추가한다.
    - `@Component` 어노테이션이 붙은 모든 클래스는 스프링 컨테이너에 의해 자동으로 스프링 빈으로 등록된다.
    - `@Controller`, `@Service`, `@Repository`은 모두 `@Component`를 포함한다.

### **2. 자바 코드로 직접 스프링 빈 등록(수동 빈 등록)**

1. 자바 설정 클래스를 만들고 `@Configuration` 어노테이션을 클래스 선언부 위에 추가한다.
    - 싱글톤 보장을 위해 반드시 `@Configuraion`을 추가해야 한다.
    - XML로 스프링 빈을 등록하는 방법도 있지만 최근에는 거의 사용하지 않는다.
2. 특정 타입을 리턴하는 메소드를 만들고 메서드 위에 `@Bean` 어노테이션을 추가한다.

## 💡@Bean과 @Component의 차이

1. 선언 위치
    - `@Bean`은 메소드 레벨에 선언한다.
    - `@Component`는 클래스 레벨에서 선언한다.
2. 사용 목적
    - `@Bean`은 개발자가 컨트롤이 불가능한(외부 라이브러리가 제공하는) 객체를 빈으로 등록하고 싶을 때 사용한다.
        - `ObjetMapper`를 빈으로 등록하고 싶은 경우, `ObjectMapper`의 인스턴스를 생성하는 메소드를 만들고 해당 메소드에 `@Bean`을 선언하여 빈으로 등록한다.
    - `@Component`는 개발자가 직접 컨트롤이 가능한(내가 직접 만든) 클래스를 빈으로 등록하고 싶을 때 사용한다.

## Reference

- [[Spring] 스프링 빈을 등록하는 두 가지 방법(@Component, @Bean)](https://dev-coco.tistory.com/69)
- [@Bean vs @Component](https://jojoldu.tistory.com/27)
- [Bean과 Component 차이](https://medium.com/sjk5766/bean%EA%B3%BC-component-%EC%B0%A8%EC%9D%B4-96a8d0533bfd)
- [[Spring] 빈 등록을 위한 어노테이션 @Bean, @Configuration, @Component 차이 및 비교 - (1/2)](https://mangkyu.tistory.com/75)

# https://github.com/WeeklyStudy/spring-core-principles/issues/2 스프링 프레임워크에서 컨테이너의 역할은 무엇인가?
 
## 💡IoC(제어의 역전)

> 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것이다.
> 
- 스프링 프레임워크에서는 사용자가 아닌 스프링 컨테이너가 객체를 관리하는 것이다.

## 💡IoC 컨테이너(스프링 컨테이너)

> 스프링 프레임워크에서 객체의 생명주기와 의존성을 관리하는 컨테이너이다.
> 

### 1. 특징

- 빈 객체의 라이프사이클(생성, 초기화, 소멸, 스코프)을 관리한다.
- 빈 객체 간 의존관계를 관리한다.
- 빈 객체의 스코프를 관리한다.
- 다양한 설정 형식을 지원한다.(XML, 어노테이션, 자바 설정 클래스 등)
- 스프링 IoC 컨테이너 종류는 `BeanFactory`와 `ApplicationContext`가 있다.

### 2. 장점

- 개발자는 비즈니스 로직에 집중할 수 있다.
- 객체 생성 코드가 없어 TDD가 용이하다.

## Reference

- [[Spring] IoC 컨테이너 (Inversion of Control) 란?](https://dev-coco.tistory.com/80)


# https://github.com/WeeklyStudy/spring-core-principles/issues/3 ApplicationContext 의 부가기능은 어떠한 기능을 제공할까?

## 💡BeanFactory와 ApplicationContext

<img src = "https://github.com/WeeklyStudy/spring-core-principles/assets/48237976/495c2485-55ad-4f06-9b7f-60434de2e6e4" width=600>


### 1. BeanFactory

- 스프링 컨테이너의 최상위 인터페이스다.
- 빈을 등록, 생성, 조회, 반환 관리하는 기능을 제공한다.
- 빈을 조회하는 `getBean()` 메소드가 정의되어 있다.
- `Lazy-Loading` 방식을 사용한다.(빈을 사용할 때 빈을 로딩함)

### 2. ApplicationContext

- BeanFactory 인터페이스를 상속한 인터페이스다.
- BeanFactory와 동일하게 빈을 등록, 생성, 조회, 반환 관리하는 기능을 제공한다.
- `Eager-Loading` 방식을 사용한다.(런타임 실행시 모든 빈을 미리 로딩함)
- 부가 기능을 추가로 제공한다.
    - **메시지소스를 활용한 국제화 기능(MessageSource)** : 국제화가 지원되는 텍스트 메시지를 관리(e.g. 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력)
    - **환경변수(EnvironmentCapable)** : 로컬 환경(내 PC), 개발(테스트) 환경, 운영 환경 등을 구분해서 처리(e.g 환경에 따라 어떤 데이터 베이스에 연결해야할지)
    - **애플리케이션 이벤트(ApplicationEventPublisher) :** 이벤트를 발행하고 구독하는 모델을 편리하게 지원
    - **편리한 리소스 조회(ResourceLoader) :** 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회

## 💡정리

- `BeanFactory` 와 `ApplicationContext`를 스프링 컨테이너라고 한다.
- `ApplicationContext`는 `BeanFactory`의 기능을 상속받는다.
- `ApplicationContext`는 빈 관리기능과 편리한 부가 기능을 제공한다.
- 대부분의 애플리케이션에서는 `BeanFactory`보다는 `ApplicationContext`를 사용하는 것이 좋다.

## Reference

- [[Spring] IoC 컨테이너 (Inversion of Control) 란?](https://dev-coco.tistory.com/80)
- [BeanFactory 와 ApplicationContext의 차이](https://velog.io/@saint6839/BeanFactory-%EC%99%80-ApplicationContext%EC%9D%98-%EC%B0%A8%EC%9D%B4#applicationcontext-%EC%82%AC%EC%9A%A9%EC%9D%84-%EA%B6%8C%EC%9E%A5%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0)
