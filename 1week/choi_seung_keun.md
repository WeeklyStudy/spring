# #1 @Component와 @Bean의 차이점은 무엇인가?

## Bean 등록 방법 차이

- 컴포넌트 스캔을 통한 빈 등록
    1. 스프링 어플리케이션 실행시 @SpringBootBoot 어노테이션 내부의 @ComponentScan 동작
    2. CompnentScan 으로 인한 @Component 를 가진 클래스는 빈으로 등록
    
    💡 @Controller, @Service, @Repository, @Configuration 어노테이션 내부에 @Component 포함하고 있음
    
- Configuration 을 통한 빈 등록
    1. 구성 클래스의 상단부에 @Configuration 어노테이션 사용
    2. 메서드에 @Bean 어노테이션을 가진 팩토리 메소드를 통해 객체 등록
 
</br>

## #2 스프링 프레임워크에서 컨테이너의 역할은 무엇인가?

## 컨테이너의 역할

1. 빈 관리 : 애플리케이션에서 사용되는 객체(빈)을 생성, 구성, 관리
2. 의존성(관계) 주입 : 빈 간의 의존관계를 관리하고 필요한 의존성을 주입해줌
    - 해당 과정은 객체의 결합도를 낮추어 DIP, OCP 와 같은 OOP 원칙을 지킬 수 있도록 도움
3. 라이프사이클 관리 : 빈의 초기화 및 소멸 단계를 제어하며 객체의 생명주기를 관리함
    - 생성시 초기화 메소드 호출, 종료시 소멸 메소드 호출
4. 스코프 관리 :  빈의 생명주기와 범위를 정의하며 빈 스코프를 관리함
    - 스코프의 종류 : 싱글톤, 프로토타입, 요청, 세션 등 존재
5. 환경 설정 : 애플리케이션의 설정 정보를 관리하고 환경변수, 프로퍼티 파일, XML 설정 파일, 어노테이션을 통해 애플리케이션의 동작을 구성
6. AOP(Aspect-Oriented Programming) 지원: 스프링 컨테이너는 AOP를 지원하며, 관점 지향 프로그래밍을 통해 애플리케이션의 공통 관심사(예: 로깅, 보안, 트랜잭션)를 분리하여 모듈화할 수 있습니다.
7. 이벤트 처리(Event Handling): 스프링 컨테이너는 이벤트 기반 프로그래밍을 지원하며, 애플리케이션 이벤트를 발행하고 처리할 수 있습니다.
8. 프로파일(Profile) 관리: 스프링 컨테이너는 다양한 환경에 따라 설정 프로파일을 활성화하고 비활성화할 수 있으며, 프로파일에 따라 다른 빈 설정을 사용합니다.

### 정리

스프링 컨테이너는 애플리케이션의 구조관리, 환경 설정 및 빈의 관리와 제어를 한다.

</br>

# #3 ApplicationContext 부가기능은 어떠한 기능을 제공할까?

> 스프링 빈(Bean)
  스프링에서 제어권을 가지고 관리되는 객체로 스프링프레임워크로 부터 생성 및 의존관계를 부여받는다.
    

## 1. BeanFactory vs ApplicationContext

- BeanFactory
    - 빈의 생성, 의존관계 설정 기능을 담당하는 기본적인 IoC 컨테이너
    - 스프링 컨테이너의 최상위 인터페이스
    - 스프링 빈을 조회 및 관리하는 역할
    - Lazy-loading 방식으로 빈을 사용할 때 로딩하는 경량 컨테이너
    
    💡 BeanFactory 를 개별적으로 사용하는 일은 거의 없으며 특별한 이유 없으면 ApplicationContext 사용 권장
    

- ApplicationContext
    - BeanFactory를 상속받아 확장하는 인터페이스
        - 구조상 BeanFactory 확장한 `ListableBeanFactory`, `HierachicalBeanFactory`를 ApplicationContext가 확장
    - Eager-loading 방식으로 런타임시 모든 빈을 로딩시킴
        - 실행시 모든 빈이 한 번에 컨테이너에 올라가기 때문에 많은 실행시간이 소요
        - 테스트 환경에서 모든 빈을 올리게 되면 많은 실행시간이 소요되기 때문에 실행시간을 줄이기 위해 테스트 실행에 필요한 빈만 컨테이너에 등록할 수 있음
    - BeanFactory 빈 관리 기능 + `부가기능` 제공
        - BeanFactory 이외에 `MessageSource`, `EnviromentCapable`, `ApplicationEventPublisher`, `ResourceLoader` 상속받음
    
    💡 엔터프라이즈 개발을 위해서는 빈의 생성, 관리 이외의 부가 기능이 필요한데, ApplicationContext 에서 부가적인 기능을 제공함
    

BeanFactory는 빈을 생성하고 관리하는 IoC 기본 기능에 중점을 둔 것

ApplicationContext는 별도의 정보를 참고하여 제어의 중점을 둔 것

## 2. ApplicationContext 부가기능

- ApplicationContext 구조
    
    ```java
    public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory,
    		MessageSource, ApplicationEventPublisher, ResourcePatternResolver {		
    		...
    }
    ```
    
    BeanFactory를 확장한 ListableBeanFactory, HierarchicalBeanFactory 와 EnvironmentCapable, MessageSource, ApplicationEventPublisher, ResourcePatternResolver 를 상속받음
    

### 2-1. EnvironmentCapable

1. 프로파일 활성화 및 설정
    
    profile value 설정하여 빈을 등록하면 profile value로 빈들이 묶이게 되고, 활성화할 프로파일을 Environment 에서 선택하면 해당 프로파일을 가진 빈 묶음을 가져올 수 있음
    
    - @Profile 어노테이션 이용한 Bean 등록
        
        ```java
        @Configuration
        @Profile("test")
        public class TestConfiguration {
            
        		@Bean
            public BookRepository bookRepository() {
                return new TestBookRepository();
            }
            
        }
        ```
        
    - ConfigurableApplicationContext 등 ApplicationContext 에서 얻은 Environment 이용해 환경 설정
        
        ```java
        @Component
        public class ProfileTestRunner implements ApplicationRunner {
            @Autowired
            ConfigurableApplicationContext ctx;       
        
            @Override
            public void run(ApplicationArguments args) throws Exception {
                ConfigurableEnvironment environment = ctx.getEnvironment();
                environment.setActiveProfiles("dev");       
                System.out.println(Arrays.toString(environment.getActiveProfiles()));
        }
        ```
        
    - @ActiveProfiles 어노테이션 이용한 프로파일 활성화
        
        ```java
        @SpringBootTest
        @ActiveProfiles("test")
        class TestConfigurationTest { ...	}
        ```
        
2. 프로퍼티 소스 설정 및 프로퍼티 값을 가져옴
    - 애플리케이션에 등록된 key-value 쌍의 프로퍼티들을 접근할 수 있도록 도움
        - getProperty() 메소드를 통해 프로퍼티 가져올 수 있음
            
            ```java
            Environment environment = ctx.getEnvironment();
            environment.getProperty();
            ```
            
        - 프로퍼티의 종류는 구성파일(application.properties) 또는 운영체제나 JVM에서 넘겨 받는 프로퍼티 등
            
            ```java
            -Dspring.profiles.active="test"
            ```
            

### 2-2. MessageSource

국제화 및 메시지 처리를 위한 인터페이스 

- `MessageSource`는 각 지역에 대한 다국어 지원을 위해 메시지설정 파일(번들)을 모아서 각 국가마다 로컬라이징(로드)함으로써 각 지역에 맞춤 메시지를 제공
- 메세지 설정 파일을 위해 프로퍼티 파일을 사용해야 함
    - 한글_한국 메시지 : message_ko_KR.properties
    - 영어_미국 메시지 : message_en_US.properties

### 2-3. ApplicationEventPublisher

스프링에서 이벤트 프로그래밍에 필요한 인터페이스 제공

- publishEvent() 메소드를 사용하여 이벤트 발행 가능

### 2-4. **ResourceLoader**

자원(파일, 클래스 경로 등)을 로드하고 읽는 메서드를 제공

- 외부 리소스 파일, 프로퍼티 파일, 이미지 등을 액세스할 수 있음

### 참고 자료

- [**BeanFactory 와 ApplicationContext의 차이**](https://velog.io/@saint6839/BeanFactory-%EC%99%80-ApplicationContext%EC%9D%98-%EC%B0%A8%EC%9D%B4)
- [**스프링 프레임워크 핵심기술 3 - ApplicationContext의 다양한 기능**](https://kyun-s-world.gitbook.io/nowstart/spring/springframeworkcore/2-applicationcontext-2)
- [**EnvironmentCapable**](https://velog.io/@yu-jin-song/Spring-EnvironmentCapable)
