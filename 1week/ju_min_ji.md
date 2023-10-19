## Q1. **스프링 프레임워크에서 컨테이너의 역할은 무엇인가?**

### 1. IOC Container

- 스프링 컨테이너는 IoC(Inversion of Control, 제어의 역전) 컨테이너라고 불리기도 한다.
- 역할:
    - 기존 프로그램은 구현 객체가 프로그램의 제어 흐름을 스스로 조종했다.
    - 반면, 스프링은 컨테이너가 **인스턴스의 생성부터 소멸까지의 인스턴스 생명 주기를 관리**한다.
       - 스프링 컨테이너가 관리하는 이 객체를 Bean이라고 한다.
- 효과:
    - 책임 분리
        - 객체 관리를 프레임워크(Container)에 위임하기 때문에 개발자는 **로직에 집중**할 수 있고, **책임**을 명확하게 **분리**하여 객체지향의 **SRP**(Single Responsibility Principle, 단일 책임 원칙)를 준수할 수 있다.

### 2. DI Container

- 스프링 컨테이너는 DI(Dependency Injection, 의존관계 주입) 컨테이너라고 불리기도 한다.
- 역할:
    - 의존 관계는 1) 정적인 클래스 의존관계, 2)실행시점에 결정되는 동적인 객체 인스턴스 의존관계로 분리된다.
    - 스프링에서는 애플리케이션 실행 시 컨테이너가 빈 설정 정보(Bean Definition)를 바탕으로 **실제 구현 객체를 생성**하여 **클라이언트와 서버의 의존관계**를 **연결**한다.
        - 컨테이너가 생성될 때, 즉 ApplicationContext가 로드되는 시점에 Bean Definition을 바탕으로 각 빈의 구성 유효성을 검사한다. 이로 인해 런타임 시 발생할 수 있는 문제를 실행 시점에서 미리 발견할 수 있다.
       - 또한 기본적으로, ApplicationContext 구현체들은 초기화 프로세스의 일부로 모든 싱글톤 빈을 미리 생성(pre-instantiation)하고 의존관계를 주입한다.
        - 하지만 스프링의 이런 특징으로 인해 애플리케이션 설정이 많고 무겁다면, 애플리케이션 초기화에 많은 시간이 소요된다.
        - 이에 '지연 초기화(lazy-initialized)'로 싱글톤 빈이 요청된 시점에 빈 인스턴스를 생성하고 의존관계를 주입하도록 기본 동작을 재정의할 수도 있지만, 지연 초기화가 아닌(not lazy-initialized) 싱글톤 빈의 의존성에 필요할 때는  ApplicationContext 로드 시점에 지연 초기화 빈을 생성하여 다른 싱글톤 빈에 주입한다.
- 효과:
    - 유연한 수정 → 확장에 용이
        - 정적인 클래스 의존관계를 변경하지 않고 동적인 객체 인스턴스 의존관계를 손쉽게 변경할 수 있다.
        - 다른 코드에 대한 파급력, 특히 **클라이언트(Service Layer) 수정 없이** **클라이언트가 호출하는 대상의 인스턴스 타입**을 **변경**할 수 있다.
        - 이로 인해 객체지향의 **OCP**(Open-Closed Principle, 개방-폐쇄 원칙)을 준수할 수 있다.

> #### 💡 의존성 주입(DI) 방법
> - 의존성을 주입하는 방법에는 Setter Injection(수정자 주입), Constructor Injection(생성자 주입), Field Injection(필드 주입)이 있다.
> - Spring은 필수 종속성에는 constructors를 사용하고, 선택적 종속성에는 setter methods 혹은 configuration methods을 사용하는 것을 권장한다. 생성자 주입은 프로그래밍 방식으로 인수를 검증하기 때문이다.
> - 또한, 필드 주입이나 수정자 주입은 객체 생성 시점에는 순환참조가 일어나는지 아닌지 발견할 수 없다. 필드 주입이나 수정자 주입은 빈 생성 후 의존성을 주입하지만, 생성자 주입은 빈 생성하는 시점에 의존성을 주입하기 때문이다.

### 3. 결론

- 스프링 컨테이너는 객체의 생성, 초기화, 서비스, 소멸에 대한 권한을 지니고(IoC), 객체의 관계를 연결(DI)하여 **객체 간의 의존성(결합도)을 낮추는 역할**을 한다.

### References
- [[Spring Framework Doc] Bean Dependency Injection *](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html)
- [[Spring Framework Doc] Lazy-initialized Beans *](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-lazy-init.html)
- [Spring 컨테이너란?(DI Container, IoC Container)](https://woopi1087.tistory.com/28)
- [스프링 컨테이너(IoC 컨테이너)와 빈](https://yeo-computerclass.tistory.com/268)
- [생성자 주입을 @Autowired를 사용하는 필드 주입보다 권장하는 하는 이유](https://madplay.github.io/post/why-constructor-injection-is-better-than-field-injection)
- [[Inflearn] 빈 생성과 의존관계 주입시점에 대하여](https://www.inflearn.com/questions/139218/%EB%B9%88-%EC%83%9D%EC%84%B1%EA%B3%BC-%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84-%EC%A3%BC%EC%9E%85%EC%8B%9C%EC%A0%90%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC)
- [[Inflearn] 빈 객체의 빈 저장소 등록 순서](https://www.inflearn.com/questions/89770/spring%EC%9D%98-bean%EC%A3%BC%EC%9E%85-%EA%B4%80%EB%A0%A8-%EC%A7%88%EB%AC%B8%EC%9E%85%EB%8B%88%EB%8B%A4)
- [[Inflearn] 스프링 컨테이너의 빈 등록 시점](https://www.inflearn.com/questions/122360/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%97%90-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88-%EB%93%B1%EB%A1%9D-%EC%8B%9C%EC%A0%90%EC%97%90-%EB%8C%80%ED%95%B4-%EC%A7%88%EB%AC%B8%ED%95%A9%EB%8B%88%EB%8B%A4)
- [[Inflearn] 빈 의존관계주입과 초기화 시점](https://www.inflearn.com/questions/667241/%EB%B9%88-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84%EC%A3%BC%EC%9E%85%EA%B3%BC-%EC%B4%88%EA%B8%B0%ED%99%94-%EC%8B%9C%EC%A0%90-%EC%A7%88%EB%AC%B8)

## Q2. **@Component와 @Bean의 차이점은 무엇인가?**

### 1.  **@Component와 @Bean의 공통점**

- 두 어노테이션 모두 Spring(IOC) Container에 Bean을 등록하도록 하는 메타데이터를 기입한다.

### 2. **@Component와 @Bean의 차이점**

- 선언 레벨:
    - Component:
        - **클래스 레벨**에서 선언한다.
            - `@Controller`, `@Service` , `@Repository` 등이 이에 해당된다.
    - @Bean:
        - **메서드 레벨**에서 선언한다.
- 활용:
    - @Component:
        - **사용자가 작성한 클래스**를 `@ComponentScan` 대상으로 삼아 빈으로 등록하고 싶을 때 사용한다.
        - 또한, 특정 어노테이션을 클래스에 붙이면 빈으로 등록되는 어노테이션으로 만들고 싶다면 `@Component` 어노테이션으로 만들 수 있다.
    - @Bean:
        - 설정이나 개발자가 **제어불가능**한 **외부 라이브러리**들을 모아 `@Configuration` 어노테이션이 추가된 클래스에서 주로 사용된다.
            - 예를 들어, Spring Security에서 제공하는 PasswordEncoder는 개발자에 의해 따로 수정하는 것은 힘들기 때문에 사용할 메서드에 `@Bean` 어노테이션을 붙여서 Bean 등록할 수 있다.
            - 단, `@Configuration` 없이 `@Bean` 만 사용해도 빈 등록은 되지만, 메서드 호출을 통해 객체를 생성할 때 싱글톤을 보장하지 않으므로 주의해야 한다.

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
