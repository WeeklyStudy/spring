## Q1. **스프링 프레임워크에서 컨테이너의 역할은 무엇인가?**

### 1. IOC Container

- 스프링 컨테이너는 IoC(Inversion of Control, 제어의 역전) 컨테이너라고 불리기도 한다.
- 역할:
    - 기존 프로그램은 구현 객체가 프로그램의 제어 흐름을 스스로 조종했다.
    - 반면, 스프링은 컨테이너가 **인스턴스의 생성부터 소멸까지의 인스턴스 생명 주기를 관리**하기 때문이다.
    - 스프링 컨테이너가 관리하는 이 객체를 Bean이라고 한다.
- 효과:
    - 책임 분리
        - 객체 관리를 프레임워크(Container)에 위임하기 때문에 개발자는 **로직에 집중**할 수 있고, **책임**을 명확하게 **분리**하여 객체지향의 **SRP**(Single Responsibility Principle, 단일 책임 원칙)를 준수할 수 있다.

### 2. DI Container

- 스프링 컨테이너는 DI(Dependency Injection, 의존관계 주입) 컨테이너라고 불리기도 한다.
- 역할:
    - 의존 관계는 1) 정적인 클래스 의존관계, 2)실행시점에 결정되는 동적인 객체 인스턴스 의존관계로 분리된다.
    - 스프링에서는 컨테이너가 **런타임**에 빈 설정 정보(Bean Definition)를 바탕으로 **실제 구현 객체를 생성**하여 **클라이언트와 서버의 의존관계**를 **연결**하기 때문이다.
        - 엄밀히 말하자면, Lazy-initialized 설정(`@Lazy`)을 하지 않는 이상, Bean Definition을 통해 의존 관계를 먼저 구성하고, 이를 바탕으로 생성 순서를 정한다.
        - 이렇게 생성된 빈은 해당 빈이 호출되는 런타임에 생성된 순서대로 초기화된다.
- 효과:
    - 유연한 수정 → 확장에 용이
        - 정적인 클래스 의존관계를 변경하지 않고 동적인 객체 인스턴스 의존관계를 손쉽게 변경할 수 있다.
        - 다른 코드에 대한 파급력, 특히 **클라이언트(Service Layer) 수정 없이** **클라이언트가 호출하는 대상의 인스턴스 타입**을 **변경**할 수 있다.
        - 이로 인해 객체지향의 **OCP**(Open-Closed Principle, 개방-폐쇄 원칙)을 준수할 수 있다.

> 의존성 주입(DI)하는 3가지 방법
> 
> - Setter Injection(수정자 주입)
> - Constructor Injection(생성자 주입)
> - Method Injection(필드 주입)

### 3. 결론

- 스프링 컨테이너는 객체의 생성, 초기화, 서비스, 소멸에 대한 권한을 지니고(IoC), 객체의 관계를 연결(DI)하여 **객체 간의 의존성(결합도)을 낮추는 역할**을 한다.

### References

- [Spring 컨테이너란?(DI Container, IoC Container)](https://woopi1087.tistory.com/28)
- [Spring Core Technolohies 1. IoC Container (2) Dependency Injection *](https://blog.leaphop.co.kr/blogs/45)
- [스프링 컨테이너(IoC 컨테이너)와 빈](https://yeo-computerclass.tistory.com/268)
- [스프링 핵심 원리 기본편(4) - AppConfig 리팩터링, 새로운 구조와 할인 정책 적용, IoC, DI, 컨테이너, 스프링으로 전환하기 by 인프런 김영한](https://scoring.tistory.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B84-AppConfig-%EB%A6%AC%ED%8C%A9%ED%84%B0%EB%A7%81-%EC%83%88%EB%A1%9C%EC%9A%B4-%EA%B5%AC%EC%A1%B0%EC%99%80-%ED%95%A0%EC%9D%B8-%EC%A0%95%EC%B1%85-%EC%A0%81%EC%9A%A9-IoC-DI-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%8A%A4%ED%94%84%EB%A7%81%EC%9C%BC%EB%A1%9C-%EC%A0%84%ED%99%98%ED%95%98%EA%B8%B0)

## Q2. **@Component와 @Bean의 차이점은 무엇인가?**

### 1.  **@Component와 @Bean의 공통점**

- 두 어노테이션 모두 Spring(IOC) Container에 Bean을 등록하도록 하는 메타데이터를 기입한다.

### 2. **@Component와 @Bean의 차이점**

- 선언 레벨:
    - Component:
        - 클래스 레벨에서 선언한다.
            - `@Controller`, `@Service` , `@Repository` 등이 이에 해당된다.
    - @Bean:
        - 메서드 레벨에서 선언한다.
- 활용:
    - @Component:
        - **사용자가 작성한 클래스**를 `@ComponentScan` 대상으로 삼아 빈으로 등록하고 싶을 때 사용한다.
        - 또한, 특정 어노테이션을 클래스에 붙이면 빈으로 등록되는 어노테이션으로 만들고 싶다면 `@Component` 어노테이션으로 만들 수 있다.
    - @Bean:
        - 설정이나 개발자가 제어가 **불가능**한 **외부 라이브러리**들을 모아 `@Configuration` 어노테이션이 붘은 클래스에서 주로 사용된다.
            - 예시:
                - Spring Security에서 제공하는 PasswordEncoder는 개발자에 의해 따로 수정하는 것은 힘들기 때문에 사용할 메서드에 `@Bean` 어노테이션을 붙여서 Bean 등록할 수 있다.
            - 조건:
                - `@Configuration` 없이 `@Bean` 만 사용해도 빈 등록되지만, 메서드 호출을 통해 객체를 생성할 때 싱글톤을 보장하지 않는다.

### References

- [[Spring] @Bean vs @Component](https://yunyoung1819.tistory.com/199)
- [[Spring] Annotation 정리 & @Component vs @Bean 차이점](https://velog.io/@tjdkzz97/Spring-Annotation-%EC%A0%95%EB%A6%AC-Component-vs-Bean-%EC%B0%A8%EC%9D%B4%EC%A0%90)
- [[Spring] XML 설정 방식을 Annotation으로 바꾸기](https://kimcoder.tistory.com/443)
- [[Spring] Bean 설정, 컴포넌트 스캔(Component Scan)](https://wpunch2000.tistory.com/m/18)

## Q3. **ApplicationContext의 부가기능은 어떠한 기능을 제공할까?**

### 1. ApplicationContext와 BeanFactory

- Spring이 제공하는 Container에는 대표적으로 BeanFactory와 ApplicationContext가 있다.
- BeanFactory는 DI Container의 기본이 되는 인터페이스인데, Bean을 관리하는 역할을 하는 인터페이스이다.
- ApplicationContext는 BeanFactory를 상속한 인터페이스로 BeanFactory의 기능 외에 추가적으로 AOP와 같이 대규모 웹 프로젝트에 필요한 여러 확장기능들을 포함하고 있다.

### 2. ApplicationContext 부가기능

- Environment
    - Profile 활성화 및 설정
        - `@Profile` 어노테이션에 profile value를 주어 빈에 등록하게 되면 해당 value로 빈들이 묶이게 되는데, 이 중에서 어떤 프로파일을 활성화할 것인지 `Environment` 에서 선택할 수 있다.
    - Property 소스 설정 및 값 가져오기
        - application.properties나 운영체제, JVM에서 넘겨받는 key-value 쌍의 Property에 접근하여 새로 등록하거나 가져올 수 있다.
- MessageSource
    - 메세지에 대한 국제화
        - 메세지 설정 파일을 모아서 로컬라이징하여, 각 지역에 맞춤 메세지를 제공할 수 있다.
- ApplicationEventPublisher
    - 이벤트 관련 동작
        - 이벤트 프로그래밍에 필요한 인터페이스를 제공하여, 이벤트를 발생시키거나 이벤트 Handler를 설정할 수 있다.

### References

- [스프링 프레임워크 핵심기술 2 - ApplicationContext의 다양한 기능(1)](https://kyun-s-world.gitbook.io/nowstart/spring/springframeworkcore/2-applicationcontext)
- [스프링 프레임워크 핵심기술 3 - ApplicationContext의 다양한 기능(2)](https://kyun-s-world.gitbook.io/nowstart/spring/springframeworkcore/2-applicationcontext-2)
