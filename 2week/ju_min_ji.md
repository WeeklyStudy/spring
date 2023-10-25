## Q1.  **@RequiredArgsConstructor와 final 키워드의 관계**

### 1. final 키워드란?
- 의미
    - 변수, 메서드, 또는 클래스에 사용되고, **어떤 곳에 사용되느냐**에 따라 다른 의미를 가진다.
    - 하지만 `final` 키워드를 붙이면 무언가를 **제한**한다는 공통적인 의미를 지닌다.
- 선언 위치
    - 변수:
        - 변수에 final 키워드를 붙이면, 한 번 초기화되면 **최종**이 되어 이후에 **재할당 불가능**하다는 의미이다.
        - 다만, `final` 키워드만으로는 **불변을 보장할 수는 없다**.
            - Primitive 타입에서는 참조값이 존재하지 않기 때문에 외부에서도 그대로 불변으로 존재하게 된다. 
                ```java
                final int num = 3;
                
                num = 4; // compile error
                ``` 
            - 하지만 참조 변수의 경우, `final`이더라도 불변성을 얻을 수는 없다. 객체의 참조는 변경 불가능하지만, 객체의 속성은 변경 가능하기 때문이다.
                | final |  불변 |
                | --- | --- |
                |객체의 실제 값 혹은 상태을 변경할 수 없지만, 참조를 다른 값으로 변경할 수 있음을 의미 |다른 참조나 다른 객체를 가리키도록 객체의 참조를 변경할 수는 없지만, 상태를 변경할 수는 있음을 의미 (e.g. setter methods 사용)|
                ```java
                final StringBuilder sb = new StringBuilder();
                
                // 컴파일 에러 (cannot assign a value to final variable sb) 발생
                sb = new StringBuilder();
                
                // final 변수의 값은 변경할 수 없지만, 객체 내부 값은 변경 가능
                sb.append("modify");
                System.out.println(sb); // output: modify
                ```
        - final 키워드를 사용한 변수에 초기값을 설정하는 방법:
            - 변수를 선언하는 동시에 초기화
                ```java
                @Service
                public class KakaoMapSearchApi {
                    private final double DOCUMENTS_PER_PAGE = 15.0;
                ```  
            - **생성자 내에서 초기화**  
                ```java
                @Service
                public class ShopService {
                    private final ShopRepository shopRepository;
                    private final KakaoMapSearchApi kakaoMapSearchApi;
                
                    public ShopService(ShopRepository shopRepository, KakaoMapSearchApi kakaoMapSearchApi) {
                        this.shopRepository = shopRepository;
                        this.kakaoMapSearchApi = kakaoMapSearchApi;
                    }
                ```  
    - 메서드:
        - 하위 클래스에서 오버라이딩할 수 없다.
    - 클래스:
        - 해당 클래스는 다른 클래스에서 상속할 수 없다.
### 2. 생성자 주입과 **final 키워드의 관계**

- Spring Framework의 IoC 컨테이너는 등록된 빈(bean) 간의 의존관계를 주입하고 관리한다.
- 그 중 **생성자 주입은 생성자 호출 시점에 딱 1번만 호출되기 때문에 주입하는 인스턴스를 변경할 수 없다.**
- `final` 키워드로 필드를 선언하면 해당 필드를 초기화하기 전에 사용하려고 하면 **컴파일 오류**가 발생한다.
- **따라서, 생성자 주입은 `final` 키워드를 사용하기에 이상적이다. 생성자에서 혹시라도 값이 설정되지 않는 오류를 **컴파일 시점**에 막아주기 때문이다.**
- 수정자 주입을 포함한 나머지 주입 방식은 모두 생성자 이후에 호출되므로, 필드에 `final` 키워드를 사용 할 수 없다.

### 3. final 필드에 적용되는 @RequiredArgsConstructor

- `@RequiredArgsConstructor`은 각 필드에 대해 1개의 매개변수를 갖는 생성자를 자동으로 생성한다.
- **그런데 이 때, 초기화되지 않은 `final` 필드와 `@NonNull` 어노테이션이 적용된 필드에 대해서만 매개변수가 생성된다.**
- 프로젝트 코드 예제
    - 어노테이션 적용 전
        ```java
        @Service
        public class ShopService {
            private final ShopRepository shopRepository;
            private final KakaoMapSearchApi kakaoMapSearchApi;
        
            public ShopService(ShopRepository shopRepository, KakaoMapSearchApi kakaoMapSearchApi) {
                this.shopRepository = shopRepository;
                this.kakaoMapSearchApi = kakaoMapSearchApi;
            }
        ```
    - 어노테이션 적용 후
        ```java
        @RequiredArgsConstructor
        @Service
        public class ShopService {
            private final ShopRepository shopRepository;
            private final KakaoMapSearchApi kakaoMapSearchApi;
        ```
        -  이처럼 `@RequiredArgsConstructor`를 사용한 경우, ShopService 클래스의 생성자는 this.shopRepository 필드에 shopRepository를 매개변수로, this.kakaoMapSearchApi 필드에 kakaoMapSearchApi를 매개변수로 자동 생성한다. 
        - 또한, 생성자가 하나뿐이기 때문에 `@Autowired` 어노테이션을 붙이지 않아도 의존성 주입이 자동으로 처리되어 '생성자 주입' 동작이 발생한다.
### 4. 결론
- `@RequiredArgsConstructor` 어노테이션은 `final` 필드나 `@NonNull` 필드에 대해서만 생성자를 자동으로 생성하며, 이를 통해 생성자 주입 코드를 간결하게 유지할 수 있다.
### References

- [What is the difference between immutable and final in java?](https://stackoverflow.com/questions/34087724/what-is-the-difference-between-immutable-and-final-in-java)
- [final vs Immutability in Java](https://www.geeksforgeeks.org/final-vs-immutability-java/)
- [java의 final 키워드의 이해](https://kimeuncheol.tistory.com/109)
- [Java 에서 final 키워드 사용하기](https://hudi.blog/java-final/)
- [[Java] final 키워드에 대해서 알아보자](https://sabarada.tistory.com/148)
- [final은 불변을 보장할까?](https://velog.io/@kbsat/final%EC%9D%80-%EB%B6%88%EB%B3%80%EC%9D%84-%EB%B3%B4%EC%9E%A5%ED%95%A0%EA%B9%8C)
- [불변 객체(Immutable Object)](https://velog.io/@ohzzi/%EB%B6%88%EB%B3%80-%EA%B0%9D%EC%B2%B4Immutable-Object)
- [[Lombok Doc] @NoArgsConstructor, @RequiredArgsConstructor, @AllArgsConstructor](https://projectlombok.org/features/constructor)

## Q2. **의존관계 주입(DI)이란 무엇이고, 의존관계 주입 3가지 방법(생성자 주입, 수정자 주입, 필드 주입)은 무엇일까?**

### 1. 의존관계 주입이란?

- 의존한다는 것은?
    - 의존한다는 것은 내가 저 컴포넌트, 혹은 레이어를 알고 있다는 것이다.
    - 즉, “A가 B를 의존한다.”는 의미는 B의 기능이 추가 또는 변경되거나 형식이 바뀌면 그 영향이 A에 미친다는 것이다.
- 의존관계 주입(DI)이란?
    - DI란 그 의존관계를 외부에서 결정하고 주입하는 것을 의미한다.
    - 토비의 스프링에서는 다음의 세 가지 조건을 충족하는 작업을 의존관계 주입이라 말한다.
        - 클래스 모델이나 코드에는 런타임 시점의 의존관계가 드러나지 않는다. 그러기 위해서는 **인터페이스**만 의존하고 있어야 한다.
        - **런타임 시점의 의존관계**는 컨테이너나 팩토리 같은 **제3의 존재가** **결정**한다.
        - 의존관계는 사용할 오브젝트에 대한 레퍼런스를 **외부에서 제공(주입)**해줌으로써 만들어진다.

### 2. **의존관계 주입 3가지 방법**

- 생성자 주입
    
    ```java
    @Component
    public class A {
    
    	private final B b;
    
    	public A(B b) {
    		this.b = b;
    	}
    ```
    
    - 이름 그대로 생성자를 통해서 의존 관계를 주입 받는 방법이다.
    - Bean을 구성하기 위해 특정 인수를 사용하여 팩토리 메소드를 호출한다.
    - 생성자 호출 시점에 1번 호출하여 **불변**이거나 **필수** 의존관계에 사용된다.
- 수정자 주입
    
    ```java
    @Component
    public class A {
    
    	private B b;
    
    	public void setB(B b) {
    		this.b = b;
    	}
    ```
    
    - setter라 불리는 필드의 값을 변경하는 수정자 메서드를 통해 의존관계를 주입하는 방법이다.
    - 인수 없는 생성자 또는 인수 없는 `static`팩토리 메소드를 호출한 후 setter 메소드를 호출한다.
    - **선택적**이거나 **변경가능**할 때 사용하긴 하지만, 주로 생성자 주입을 사용하고 수정자 주입을 사용하는 경우는 드물다.
- 필드 주입
    
    ```java
    @Component
    public class A {
    
    	@Autowired
    	private B b;
    ```
    
    - 필드에 `@Autowired`를 붙여 의존관계를 주입하는 방법이다.
    - 외부에서 의존성을 설정할 수가 없어서 **테스트 시** 테스트용 인스턴스에 의존성을 주입하기 위해 setter를 따로 열어야 한다.
    - 하지만 애플리케이션의 실제 코드와 관계 없는 테스트 코드에서는 문제 없이 간편하게 사용할 수 있다.
    - 따라서, 코드가 간결하고 좋지만, 특정 케이스(애플리케이션의 실제 코드와 관계 없는 테스트 코드 등) 외에는 권장되지 않는다.

### 3. 어떤 의존관계 주입 방법이 바람직한가?

- Spring을 포함한 DI 프레임워크의 대부분이 몇 가지 이유로 생성자 주입을 권장하고 있다.
    - 명확한 종속성
        - 생성자 주입은 필요한 모든 의존성을 클래스의 생성자 매개변수로 명시적으로 선언하기 때문에 어떤 종속성이 필요한지 명확하게 드러나 코드의 가독성과 유지보수에 도움된다.
    - 객체의 불변성
        - 개발을 하다 보면 의존 관계의 변경이 필요한 상황은 거의 없다.
        - 따라서, 생성자 주입을 통해 변경의 가능성을 배제하고 불변성을 보장하는 것이 유지보수에 도움된다.
    - ****순환 참조 에러 방지****
        - 생성자 주입을 사용하면 객체 생성 시점에 의존성이 주입되기 때문에 객체 생성 시점이자, 애플리케이션 실행 시점에 순환 참조 에러를 방지할 수 있다.
    - 컴파일 시점 검출
        - 생성자 주입은 컴파일 시점에 필요한 의존성이 부족한 경우 에러를 발생시키므로 런타임 중에 발생할 수 있는 오류를 사전에 방지할 수 있다.

### References

- [[Spring Doc] Dependency Injection](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html)
- [의존관계 주입(Dependency Injection) 쉽게 이해하기](https://tecoble.techcourse.co.kr/post/2021-04-27-dependency-injection/)
- [[Spring] 다양한 의존성 주입 방법과 생성자 주입을 사용해야 하는 이유 - (2/2)](https://mangkyu.tistory.com/125)

## Q3. **스프링 빈 콜백 메서드에서 이루어지는 작업은 어떤 것이 있을까?**

### 1. **데이터베이스 커넥션 풀 (Database Connection Pool)**

- 데이터베이스 커넥션 풀이란 애플리케이션 서버와 데이터베이스의 연결을 미리 맺어놓고, 요청이 올 때 연결을 재활용 하는 것을 의미한다.
- 초기화 메서드에서 데이터베이스 연결을 설정하고 소멸 메서드에서 연결을 해제할 수 있다.

### 2. 캐시 관리

- 스프링 빈이 캐시를 초기화하고 정리하는 데 사용됩니다. 초기화 메서드에서 캐시를 로드하고 소멸 메서드에서 캐시를 비우는 등의 작업을 수행할 수 있다.

### 3. **네트워크 소켓**

- DB 커넥션 풀과 마찬가지로, 초기화 메서드에서는 소켓을 생성하고 초기화 작업을, 소멸 메서드에서는 소켓을 종료하고 리소스 해제하는 작업을 할 수 있다.

### 4. 결론

- 이처럼 스프링 빈 콜백 메서드를 활용하면 스프링 빈 라이프사이클(초기화, 소멸)에 맞춰 외부 라이브러리나 리소스 연결 및 연결 해제 작업, 캐시 등을 효과적으로 관리할 수 있다.
