# https://github.com/WeeklyStudy/spring-core-principles/issues/4 의존관계 주입(DI)이란 무엇이고, 의존관계 주입 3가지 방법(생성자 주입, 수정자 주입, 필드 주입)은 무엇일까?

## 💡의존관계 주입(DI: Dependency Injection)이란?

> 필요한 객체를 직접 생성하는 것이 아니라 외부에서 생성한 후 주입시키는 것이다.
> 
- 스프링에서는 `@Autowired` 어노테이션을 통해 의존관계 주입을 제공한다.
    - `@Autowired(required = true)` : 자동 주입 대상이 없으면 오류가 발생한다. → 기본 설정
    - `@Autowired(required = false)` : 자동 주입할 대상이 없으면 메서드 자체가 호출되지 않는다. 즉, 오류가 발생하지 않는다.

## 💡의존관계 주입 3가지 방법

### 1. 생성자 주입(Constructor Injection)

> 생성자를 통해 의존관계를 주입받는 방법이다.
> 
- 스프링에서 권장하는 방식으로, 가장 자주 사용한다.
- 생성자가 1개이고 주입받을 객체가 빈으로 등록되어있다면, `@Autowired` 를 생략할 수 있다.
- 객체 생성과 의존관계 주입이 동시에 이루어진다.(생성자가 객체를 만들 때 파라미터에 주입이 이루어져야 하기 때문)
- 장점
    - 필드에 `final` 키워드를 사용할 수 있다.
        - 생성자 호출 시점에 한 번만 할당되기 때문에, 객체의 불변성을 보장한다.
        - 생성자에서 값이 초기화되지 않으면 컴파일 오류가 발생하므로, `NPE(NullPointerException)` 을 방지할 수 있다.
    - 순환 참조 문제를 방지할 수 있다.
        - 생성자 주입은 앱 구동 시점에 (빈 생성과 주입이 발생하기 때문에) `BeanCurrentlyInCreationException` 오류가 발생한다.
        - 필드 주입과 수정자 주입은 (앱 구동 시점에 주입하지 않기 때문에) 앱 구동 이후 실제 코드가 호출될 때 오류가 발생한다.
    - 테스트 코드 작성이 용이하다.
        - 스프링 컨테이너 없이도 의존관계를 주입하여 사용할 수 있다.

### 1.1. 예제

> [방법1] 생성자에 `@Autowired` 사용하기
> 

의존관계 주입이 필요한 필드를 `final` 로 선언하고, 생성자에 `@Autowired` 를 사용한다.

```java
@Component
public class OrderServiceImpl implements OrderService{
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

> [방법2] 롬복 라이브러리의 `@RequiredArgsConstructor` 사용하기 → 최근 권장 방식
> 

의존관계 주입이 필요한 필드를 `final` 로 선언하고, 클래스 위에 `@RequriedArgsConstructor` 를 사용한다.

```java
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService{
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
}
```

- `@RequriedArgsConstructor` : `final` 이 붙은 필드를 파라미터로 갖는 생성자를 자동으로 생성한다.

### 2. 수정자 주입(Setter Injection)

> 필드의 값을 변경하는 수정자 메서드(setter)를 통해 의존관계를 주입하는 방법이다.
> 
- 객체 생성 후 의존관계 주입이 일어난다.
- 단점
    - `public` 접근 제어자를 가진 `setter 메서드`를 통해 의존관계를 변경할 수 있다.
    - `NPE(NullPointerException)`이 발생할 수 있다.

### 2.1. 예제

setter 메서드 위에 `@Autowired` 를 사용한다.

```java
@Component
public class OrderServiceImpl implements OrderService{
    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;

    @Autowired
    public void setMemberRepository(MemberRepository memberRepository) {
        System.out.println("memberRepository = " + memberRepository);
        this.memberRepository = memberRepository;
    }

    @Autowired
    public void setDiscountPolicy(DiscountPolicy discountPolicy) {
        System.out.println("discountPolicy = " + discountPolicy);
        this.discountPolicy = discountPolicy;
    }
}
```

### 3. 필드 주입(Field Injection)

> 필드에 바로 주입하는 방법이다.
> 
- 객체 생성 후 의존관계 주입이 일어난다.
- 장점
    - 코드가 매우 간결하다.
- 단점
    - 스프링 프레임워크에 의존적이다.
        - 스프링 컨테이너를 거치지 않고 의존관계를 주입할 수 없어 단위 테스트(순수 자바 코드로 테스트)가 어렵다.
    - 필드에 `final` 키워드를 사용할 수 없다.
        - 객체의 불변성을 보장하지 않는다.

### 3.1. 예제

필드에 `@Autowired` 를 사용한다.

```java
@Component
public class OrderServiceImpl implements OrderService{
    @Autowired
		private MemberRepository memberRepository;
    
		@Autowired
		private DiscountPolicy discountPolicy;
}
```

### 4. 메서드 주입

> 일반 메서드를 통해서 주입받는 방법이다.
> 
- 일반적으로 잘 사용하지 않는 방식이다.
- 한 번에 여러 필드를 주입 받을 수 있다.

### 4.1. 예제

```java
@Component
public class OrderServiceImpl implements OrderService{
    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;

    @Autowired
    public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

## 💡결론

- 기본적으로 `생성자 주입`을 사용하는 것이 좋다. 필드를 `final` 로 선언하고 `@RequriedArgsConstructor` 를 사용하자.
- 가끔 옵션 처리가 필요한 경우(주입할 빈이 없어도 동작해야 하는 경우)에는 `수정자 주입` 을 사용한다.
- `필드 주입`은 특수한 경우(`@SpringBootTest` 테스트 코드)외에는 사용하지 않는 것이 좋다.

## Reference

- [[Spring] 의존성 주입 3가지 방법 - (생성자 주입, Field 주입, Setter 주입)*](https://dev-coco.tistory.com/70)
- [Spring DI(Dependency Injection) - 의존 관계 주입 핵심 정리*](https://backendcode.tistory.com/249)
- [[Spring Boot] 순환참조문제](https://ch4njun.tistory.com/269)
- [Spring - Spring Security 적용시 순환 참조 발생 (Spring circular reference)*](https://green-bin.tistory.com/52)
- [스프링 - 생성자 주입을 사용해야 하는 이유, 필드인젝션이 좋지 않은 이유](https://yaboong.github.io/spring/2019/08/29/why-field-injection-is-bad/)
- [생성자 주입의 경우엔 @Autowired(required=false)를 쓸 수 없는건가요?](https://www.inflearn.com/questions/214902/%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A3%BC%EC%9E%85%EC%9D%98-%EA%B2%BD%EC%9A%B0%EC%97%94-autowired-required-false-%EB%A5%BC-%EC%93%B8-%EC%88%98-%EC%97%86%EB%8A%94%EA%B1%B4%EA%B0%80%EC%9A%94)
- [[Spring] 스프링 의존성 주입(DI : Dependency Injection) 4가지 방법 (의존 관계 자동 주입)](https://ittrue.tistory.com/227)

# https://github.com/WeeklyStudy/spring-core-principles/issues/5 @RequiredArgsConstructor와 final 키워드의 관계

## 💡@RequiredArgsConstuctor 어노테이션과 final 키워드

### 1. @RequiredArgsConstuctor 어노테이션

> `final` 이나 `@NotNull` 이 붙은 필드의 생성자를 자동으로 생성해주는 롬복 어노테이션이다.
> 

### 2. final 키워드

> 초기화 이후, 재할당(수정)이 불가능하다.
> 

| 위치 | 설명 |
| --- | --- |
| final 변수, 인자 | 해당 변수를 초기화 후 재할당할 수 없음 |
| final 클래스 | 해당 클래스를 상속할 수 없음 |
| final 메서드 | 해당 메소드를 오버라이드할 수 없음 |

> 📍final 변수 초기화 방법 2가지
> 
1. 선언과 동시에 초기화
    
    ```java
    class Car {
        private static final int MOVE_STEP = 1;
    }
    ```
    
2. 생성자에서 초기화
    
    ```java
    class Car {
        private final int position;
    
        Car() {
            this.position = 0;
        }
    }
    ```
    

## 💡결론

- 생성자 주입 방식에서 필드를 `final` 로 선언하면, 객체의 불변성을 보장하고 NPE를 방지할 수 있는 장점이 생긴다.
- `@RequiredArgsConstuctor` 는 `final` 이나 `@NotNull` 이 붙은 필드의 생성자를 자동으로 생성해준다.
- 생성자가 1개이고 주입받을 객체가 빈으로 등록되어있으면, `@Autowired` 를 생략할 수 있다.
- 필드를 `final` 로 선언하고 클래스 위에 `@RequriedArgsConstructor` 를 추가하면, 간편하게 생성자 주입을 구현할 수 있다.

## Reference

- [@RequiredArgsConstructor 를 이용한 의존성 주입(Dependency Injection)](https://medium.com/webeveloper/requiredargsconstructor-%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9D%98%EC%A1%B4%EC%84%B1-%EC%A3%BC%EC%9E%85-dependency-injection-4f1b0ac33561)
- [생성자 주입 시, final 키워드를 넣어야만 할까요?](https://www.inflearn.com/questions/901173/%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A3%BC%EC%9E%85-%EC%8B%9C-final-%ED%82%A4%EC%9B%8C%EB%93%9C%EB%A5%BC-%EB%84%A3%EC%96%B4%EC%95%BC%EB%A7%8C-%ED%95%A0%EA%B9%8C%EC%9A%94)
- [final 키워드 (feat. 스프링 DI(의존성 주입) & 필드 변수 생성자주입)](https://velog.io/@tjdtn4484/%EC%8A%A4%ED%94%84%EB%A7%81-DI%EC%9D%98%EC%A1%B4%EC%84%B1-%EC%A3%BC%EC%9E%85%EC%99%80-%ED%95%84%EB%93%9C-%EB%B3%80%EC%88%98%EC%9D%98-finalfeat.-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A3%BC%EC%9E%85)
- [Java 에서 final 키워드 사용하기*](https://hudi.blog/java-final/)

# https://github.com/WeeklyStudy/spring-core-principles/issues/6 스프링 빈 콜백 메서드에서 이루어지는 작업은 어떤 것이 있을까?

## 💡스프링 빈의 라이프 사이클

> 스프링 컨테이너 생성 → 스프링 빈 생성 → 의존관계 주입 → `초기화 콜백` 메서드 호출 → … → `소멸전 콜백` 메서드 호출→ 스프링 종료
> 
- `생성자 주입` : 객체의 생성과 의존관계 주입이 동시에 일어남
- `Setter 주입, Field 주입` : 객체의 생성 이후 의존관계 주입이 일어남

## 💡스프링 빈 생명주기 콜백 지원 방법 3가지

### 1. **InitializingBean, DisposableBean 인터페이스 구현하기 → 현재는 거의 사용X**

> `InitializingBean`과 `DisposableBean` 인터페이스를 `implements`하여 초기화, 소멸 메서드를 Override하는 방식이다.
> 
- `InitalizingBean`은 `afterPropertiesSet()` 메소드로 초기화를 지원한다.
- `DisposableBean`은 `destory()` 메소드로 소멸을 지원한다.
- 단점
    - 초기화, 소멸 메서드명을 변경할 수 없다.
    - 스프링 전용 인터페이스(`InitalizingBean`, `DisposableBean`)에 의존한다.
    - 코드를 고칠 수 없는 외부 라이브러리에는 적용할 수 없다.

```java
public class ExampleBean implements InitializingBean, DisposableBean {
 
    @Override
    public void afterPropertiesSet() throws Exception {
        // 초기화 콜백 (의존관계 주입이 끝나면 호출)
    }
 
    @Override
    public void destroy() throws Exception {
        // 소멸 전 콜백 (메모리 반납, 연결 종료와 같은 과정)
    }
}
```

### 2. **설정 정보에 초기화 메소드, 종료 메소드 지정**

> 설정 정보에 ****`@Bean(initMethod = 초기화 메서드명, destroyMethod = 소멸 메서드명)`를 지정하는 방식이다.
> 
- 소멸 메서드 추론 기능을 제공하여 소멸 메서드명이 close, shutdown인 경우 ****`destroyMethod` 를 생략할 수 있다.
- 장점 → 1번 방식의 모든 단점 해결
    - 초기화, 소멸 메서드명을 자유롭게 지정할 수 있다.
    - 스프링 코드에 의존하지 않는다.
    - 코드를 고칠 수 없는 외부 라이브러리에도 적용할 수 있다.
- 단점
    - 수동 빈 등록 방식에서만 사용할 수 있다.

```java
public class ExampleBean {
 
    public void initialize() throws Exception {
        // 초기화 콜백 (의존관계 주입이 끝나면 호출)
    }
 
    public void close() throws Exception {
        // 소멸 전 콜백 (메모리 반납, 연결 종료와 같은 과정)
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

### 3. **@PostConstruct, @PreDestroy 어노테이션 지원 → 최신 스프링 권장 방법**

> `@PostConstruct` , `@PreDestroy` 어노테이션을 초기화, 소멸 메서드 위에 사용하는 방식이다.
> 
- 장점
    - 초기화, 소멸 메서드명을 자유롭게 지정할 수 있다.
    - 어노테이션만 붙이면 동작하기 때문에 매우 편리하다.
    - 자바 표준 기능으로 스프링이 아닌 컨테이너에서도 동작한다.
        - `@PostConstruct` , `@PreDestroy` 어노테이션은 `javax.annotation` 에서 제공함
- 단점
    - 코드를 고칠 수 없는 외부 라이브러리에는 적용할 수 없다.

```java
public class ExampleBean {
 
    @PostConstruct
    public void initialize() throws Exception {
        // 초기화 콜백 (의존관계 주입이 끝나면 호출)
    }
 
    @PreDestroy
    public void close() throws Exception {
        // 소멸 전 콜백 (메모리 반납, 연결 종료와 같은 과정)
    }
}
```

## 💡스프링 빈 콜백 메서드 작업

- 데이터베이스 커넥션 풀 초기화
- 외부 리소스 초기화
- 캐시 초기화
- 로깅 작업

## 💡결론

- 초기화 콜백은 빈이 생성되고 의존관계 주입이 완료된 후 호출된다.
- 소멸전 콜백은 빈이 소멸되기 직전에 호출된다.
- 기본적으로 `@PostConstruct`, `@PreDestroy` 어노테이션을 사용해 초기화, 소멸 메서드를 지정하자.
- 외부 라이브러리에 적용해야하는 경우에는 설정 정보에 초기화 메서드, 소멸 메서드 지정하는 방식을 사용하자.

## Reference

- [[Spring] 빈 생명주기(Bean LifeCycle) 콜백 알아보기](https://dev-coco.tistory.com/170)
