# #4 의존관계 주입이란 무엇이고, 의존관계 주입 3가지 방법(생성자 주입, 수정자 주입, 필드 주입)은 무엇일까?

## 1. 의존관계 주입이란

- 의존관계 주입 : 객체가 의존관계를 직접생성하는 것이 아닌 외부에서 생성하여 주입해주는 것을 말함
- 특징 :
    - 스프링 컨테이너에서 빈을 주입을 통해 OCP, DIP 를 준수할 수 있음
    

## 2. 의존관계 주입 방법

스프링에서 `@Autowired` 어노테이션을 사용하여 스프링 컨테이너에서 관리하는 빈을 주입 받을 수 있다.

### 의존 관계 주입 방법

- 생성자 주입
- 수정자 주입(setter 주입)
- 필드 주입
- 일반 메서드 주입

### 2-1) 생성자 주입

- 생성자를 통해 빈을 주입 받는 방법
- 객체 생성 시점에서 의존관계 주입이 일어남
    - 생성자 호출 시점에 의존 관계를 주입하기 때문에 이후 의존관계 주입이 이루어지지 않음 ⇒ 불변성 보장
    - `final 키워드`를 사용 가능 ⇒ **컴파일 오류** 파악이 쉬움
- 테스트 작성이 용이
    - 테스트 시점에서 가짜 객체를 통해 생성하기 쉽다 ⇒ 객체 생성시 관련 의존관계 전달

### 2-2) 수정자 주입

- 필드 값 변경 메서드(setter 메소드) 이용하여 빈을 주입 받는 방법
- 의존관계를 변경에 열려있기 때문에 위험할 수 있음
- 테스트 작성시 의존관계 주입을 위해 관련 setter 메소드 호출해야 함

### 2-3) 필드 주입

- 필드에 직접 주입하는 방법
- 코드 간결하게 사용 가능
- 테스트 작성시 가짜 객체를 전달할 수 없기 때문에 단위 테스트 작성에 어려움

### 2-4) 일반 메서드 주입

- 일반 메서드를 통해 주입 받은 방법
- 수정자 주입 방법과 유사

## 3. 바람직한 방법

- 스프링 공식 문서에서는 생성자 주입을 권장
- 의존관계 변경되지 않을 경우 : 생성자 주입
- 의존관계가 선택적이거나 변경 가능한 경우 : 수정자 주입(setter 주입)

</br>

# #5 @RequiredArgsConstructor 와 final 키워드의 관계

## 1. @RequiredArgsConstructor 란

final 키워드를 가진 필드의 생성자를 자동으로 생성해주는 롬복 라이브러리의 어노테이션

## 2. 자바의 final 키워드

final : 객체의 생성 시점에 필드를 초기화해야 함 ⇒ 따라서 객체 생성 이후 필드를 수정 불가능

### 3. @RequiredArgsConstructor과 final 의 관계

- 객체생성시 의존관계를 주입하는 생성자 주입 방식에서 `final` 로 선언된 필드에 의존관계를 주입하면 불변성을 보장할 수 있음
- `@RequriedArgsConstructor` 를 추가하면, 불변으로 필드를 주입받을 수 있는 생성자를 자동으로 생성해줌

</br>

# #6  스프링 콜백 메소드에서 이루어지는 작업은 어떤 것이 있을까?

## 1. 스프링 빈 콜백 메서드란 무엇인가?

- 스프링 콜백 메서드 : 스프링 빈 생성이나 소멸 전 빈 내부에 있는 메소드를 호출해주는 기능
- 장점 : 스프링 콜백 메소드를 사용하면 생성과 초기화 작업, 종료 작업을 **분리 가능**
    - 초기화 작업은 무거운 동작을 수행하기에 객체 생성과 명확하게 나누어 설계하는 것이 유지보수 관점에서 좋다
    - 외부라이브러리와 같은 정보를 초기화 할 수 있음
    - 안전한 종료 가능

## 2. 스프링 빈 콜백 메서드 사용 방법

### 사용 방법

- 인터페이스 구현(InitalizingBean, DisposableBean)
- 설정 정보에 초기화 메소드, 종료 메소드 지정
- @PostConstruct, @PreDestroy 어노테이션 지정

- 인터페이스 구현(InitalizingBean, DisposableBean)
    
    ```java
    public class ExampleBean implements InitializingBean, DisposableBean {
    	@Override
    	public void afterPropertiesSet() throws Exception {
    		// 초기화 콜백
    	}
    
    	@Overried
    	public void destroy() throws Exception {
    		// 소멸 전 콜백
    	}
    }
    ```
    
    - `InitializingBean` 인터페이스는 `afterPropertiesSet()` 메소드 구현하도록 하며, 의존관계 주입 끝나면 콜백 메서드 호출
    - `DisposableBean` 인터페이스는 `destroy()` 메소드 구현하도록 하며, 빈 소멸 전 콜백 메소드를 호출
    - **스프링 전용 인터페이스**이기에 스프링에 의존하게 되며, 수정할 수 없는 **외부 라이브러리에 적용 불가함**
- 설정 정보에 초기화 메소드, 종료 메소드 지정
    
    ```java
    public class ExampleBean {
    
        public void initialize() throws Exception {
            // 초기화 콜백
        }
     
        public void close() throws Exception {
            // 소멸 전 콜백
        }
    }
     
    @Configuration
    class LifeCycleConfig {
     
        @Bean(initMethod = "initialize", destroyMethod = "close")
        public ExampleBean exampleBean() {
            // 생략
        }
    }
    ```
    
    - @Bean 어노테이션의 initMethod, destroyMethod 속성을 통해 콜백할 메소드 지정 가능
    - 콜백 메소드의 이름을 자유롭게 지정 가능하며 스프링 코드에 의존하지 않음
    - 수정할 수 없는 **외부 라이브러리에도 적용 가능**
    - 콜백 메소드의 이름을 추론 기능을 통해 자동 호출 가능 (종료 메서드명 : close, shutdown)
- @PostConstruct, @PreDestroy 어노테이션 지정
    
    ```java
    import javax.annotation.PostConstruct;
    import javax.annotation.PreDestroy;
     
    public class ExampleBean {
     
        @PostConstruct
        public void initialize() throws Exception {
            // 초기화 콜백
        }
     
        @PreDestroy
        public void close() throws Exception {
            // 소멸 전 콜백
        }
    }
    ```
    
    - 콜백 메소드로 지정할 메소드에 어노테이션 붙이면 됨
    - 자바 표준 기술이기 때문에 스프링 아닌 곳에서도 동작 가능
    - 수정이 불가능한 외부 라이브러리에 어노테이션 지정이 불가하다.
    

### 가장 사용하기 좋은 방법

- 최신 스프링에서 가장 권장하는 방법 :  `@PostConstruct`, `@PreDestroy` 어노테이션 지정
- 수정(커스터마이징) 불가한외부 라이브러리를 사용 : `설정 정보에 초기화 메소드, 종료 메소드 지정` 방법

## 3. 실무에서 사용

### 스프링 빈 콜백 메서드에 작성하는 내용

- 애플리케이션 요구사항이나 사용하는 외부 라이브러리에 따라 다양하게 달라짐
- 초기화를 통해 필요한 서비스 및 리소스를 사용할 수 있도록 준비
- 종류
    - 데이터베이스 커넥션 초기화 :
        - 데이터베이스 커넥션 풀 라이브러리 초기화 및 데이터베이스 연결
    - 외부 서비스 연결 설정 :
        - API, 메시징 브로커 등 외부 서비스와 연결 설정하고 초기화
        - 보통 API 클라이언트 라이브러리 초기화하고 연결 정보 설정
    - 캐시 초기화 :
        - 캐시 라이브러리를 초기화하고 설정(캐시 서버와 연결, 캐시 설정 등)
    - 로그 설정 :
        - 로깅 라이브러리 초기화, 로깅 레벨 출력 대상 설정
    - 보안 설정 :
        - 보안 관련 라이브러리 초기화하고 보안 정책, 권한 설정 등 수행

## 참고자료

- [**[Spring] 빈 생명주기(Bean LifeCycle) 콜백 알아보기**](https://dev-coco.tistory.com/170)
