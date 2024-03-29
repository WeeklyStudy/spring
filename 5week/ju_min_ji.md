## https://github.com/WeeklyStudy/spring/issues/13 **@RequestParam, @ModelAttribute, @RequestBody의 차이는?**

### 1. 요청 데이터 종류

- 요청 파라미터
    - GET
        - 바디 없이 **쿼리 파라미터**에 데이터 포함해서 전달한다.
        - 사용 예시: 검색, 필터, 페이징
    - POST - HTML 폼
        - **메세지 바디**에 쿼리 파라미터 형식(name1=value1&name2=value2)으로 전달한다.
        - 사용 예시: 회원가입, 상품 주문, HTML Form
- 요청 메세지
    - HTTP message body에 데이터 직접 담아서 요청한다.
        - 주로 HTTP API(REST API)에서 주로 사용한다.
        - JSON, XML, TEXT 정보를 담아서 사용하며, 이 중에서 주로 **JSON** 데이터 형식으로 사용한다.
        - POST, PUT, PATCH 등에 다 사용 가능하다.

### 2. 요청 파라미터를 조회하는 기능 - **@RequestParam, @ModelAttribute**

- `@RequestParam`
    - 1개의 HTTP 요청 파라미터를 받기 위해서 사용된다.
    - 파라미터 필수 여부 (`required`)와 기본값(`defaultValue`)을 설정할 수 있으며, 기본적으로 필수여부가 true이다.
- `@ModelAttribute`
    - HTTP 요청 파라미터들을 특정 **Java 객체**에 **바인딩(맵핑)** 하기 위해 사용된다.
        - **객체** 생성 후, 요청 파라미터의 이름으로 해당 객체의 속성을 찾는다.
        - 그리고 해당 속성의 setter를 호출해서 파라미터의 값을 **바인딩**한다.
        - 스프링은 타입을 검사하여 바인딩 오류가 생기면 BindException을 발생시킨다.
- 파라미터에 어노테이션 생략 시 자동으로 적용되는 어노테이션
    - String, int, Integer와 같은 Primitive 타입, Wrapper 클래스 → `@RequestParam`
    - 그 외 나머지(ArgumentResolver(예시: HttpServletResponse)로 지정해둔 타입 제외) → `@ModelAttribute`

### 3. HTTP 요청 **메시지** **바디를** 직접 조회하는 기능 - @RequestBody

- `@RequestBody`
    - HTTP 요청 본문(JSON 및 XML 등)을 **Java 객체**로 **변환**하기 위해 사용된다.
    - HTTP 요청 본문 데이터는 Spring에서 제공하는 `HttpMessageConverter`를 통해 타입에 맞는 객체로 변환된다.

### 4. 결론

- `@RequestParam`, `@ModelAttribute` 는 **요청 파라미터**를 조회하는 어노테이션, `@RequestBody`는 **요청 메세지 바디**를 조회하는 어노테이션이다.
- Primitive Type, Wrapper 클래스 파라미터에 관련 어노테이션을 생략하면 `@RequestParam`이, 개발자가 만든 클래스 **파라미터**에 관련 어노테이션을 생략하면 기본적으로 `@ModelAttribute`가 적용된다.
- 어노테이션은 기능을 갖고 있지는 않으므로 Spring에서는 실제로는 `ArgumentResolver` 인터페이스가 해당 어노테이션이 있음을 인지하고, 어노테이션에 맞는 처리기가 동작하는 방식으로 동작한다.

### References

- [[Spring] @RequestBody, @ModelAttribute, @RequestParam의 차이](https://mangkyu.tistory.com/72)
- [@RequestBody vs @ModelAttribute](https://tecoble.techcourse.co.kr/post/2021-05-11-requestbody-modelattribute/)

## https://github.com/WeeklyStudy/spring/issues/14 ResponseEntity와 @ResponseBody의 차이점은 무엇인가?

### 1. HttpEntity 클래스 - ResponseEntity의 상위 클래스 

- 정의 및 특징:
    - Spring Framework에서는 `HttpEntity` 클래스를 제공한다.
    - `HttpEntity` 클래스는 HTTP 요청 또는 응답에 해당하는 **헤더**와 **바디**를 포함하는 클래스다.
    - 따라서 이 클래스는 다음과 같은 생성자와 필드를 갖는다. 
        ```java
        public class HttpEntity<T> {
        
            // 바디나 헤더가 없는 비어있는 객체
        	public static final HttpEntity<?> EMPTY = new HttpEntity<>();
        	
        	private final HttpHeaders headers;
        	
        	@Nullable
        	private final T body;
        
        	protected HttpEntity() {
        		this(null, null);
        	}
        
        	public HttpEntity(T body) {
        		this(body, null);
        	}
        
        	public HttpEntity(MultiValueMap<String, String> headers) {
        		this(null, headers);
        	}
        
        	public HttpEntity(@Nullable T body, @Nullable MultiValueMap<String, String> headers) {
        		this.body = body;
        		this.headers = HttpHeaders.readOnlyHttpHeaders(headers != null ? headers : new HttpHeaders());
        	}
        }
        ```
    - **`HttpEntity` 클래스를 상속받아 구현한 클래스가 `RequestEntity`, `ResponseEntity` 클래스이다.**

### 2. ResponseEntity 클래스

- 정의 및 특징:
    - 응답 자체의 독립된 값이나 표현형태로, 사용자의 `HttpRequest`에 대한 **응답 데이터**를 포함하는 클래스이다.
    - 따라서 **상태코드**,**헤더**, **바디**를 포함한다.
- 사용 사례:
    - `RestTemplate`:
        - 예시 1
            
            ```java
             ResponseEntity<String> entity = template.getForEntity("https://example.com", String.class);
             String body = entity.getBody();
             MediaType contentType = entity.getHeaders().getContentType();
             HttpStatus statusCode = entity.getStatusCode();
            ```
            
            - `RestTemplate` 클래스에서 `getForEntity()`와 `exchange()` 메서드의 응답 객체로도 사용하고 있다.
                - `getForEntity()` : 주어진 URL 주소로 GET 요청을 보내고, ResponseEntity를 반환한다.
                - `exchange()` : `getForEntity()` 보다 더 유연하게 HTTP 요청을 조작할 수 잇는 메서드로, 요청 메서드(GET, POST 등)와 함께 URL을 지정하여 HTTP 요청을 보내고, ResponseEntity를 반환한다.
        - 예시 2 - 프로젝트 코드
            
            ```java
            @Service
            @RequiredArgsConstructor
            public class KakaoMapSearchApi {
             public String[] searchByRoadAddressName(String roadAddressName, Double curLnt, Double curLat) {
                    // 1. API 호출을 위한 요청 설정
                    HttpHeaders headers = new HttpHeaders();
                    headers.set("Authorization", "KakaoAK " + API_KEY);
                    HttpEntity<String> entity = new HttpEntity<>(headers);
            
                    String apiURL = "https://dapi.kakao.com/v2/local/search/keyword.JSON?"
                            + "query=" + roadAddressName + DEFAULT_QUERY_WORD
                            + "&x=" + curLnt
                            + "&y=" + curLat;
            
                    // 2. API 호출하여 응답 받기
                    JsonNode result = restTemplate.exchange(apiURL, HttpMethod.GET, entity, JsonNode.class)
                            .getBody()
                            .get("documents");
            
                    ..
                }
            ```
            
            - `HttpEntity`는 `HttpHeaders`를 통해 HTTP 요청의 헤더를 설정하고, 이를 `RestTemplate`의 `exchange()` 메서드를 호출할 때 사용하여 API 호출에 필요한 정보들을 담고 있다.
    - Spring MVC의 `@Controller` 메서드:
        - 예시 1
            
            ```java
            // 생성자 사용
            @RequestMapping("/handle")
             public ResponseEntity<String> handle() {
               URI location = ...;
               HttpHeaders responseHeaders = new HttpHeaders();
               responseHeaders.setLocation(location);
               responseHeaders.set("MyResponseHeader", "MyValue");
               return new ResponseEntity<String>("MyBody", responseHeaders, HttpStatus.CREATED);
             }
            
            // 빌더 패턴 사용 (가독성 측면에서 권장)
            @RequestMapping("/handle") 
            public ResponseEntity<String> handle() { 
            	URI location = ...;
                return ResponseEntity.created(location)
            												 .header("MyResponseHeader", "MyValue")
            												 .body("MyBody"); 
            }
            ```
            
            - `ResponseEntity` 객체 자체를 반환 값으로 두는 것이다.
        - 예시 2 - 프로젝트 코드
            
            ```java
            @RequestMapping("/favorites")
            @RestController
            @RequiredArgsConstructor
            @Slf4j
            public class FavoriteController {
                private final FavoriteService favoriteService;
                private final ShopService shopService;
            
                @PreAuthorize("isAuthenticated()")
                @GetMapping(value = "")
                public ResponseEntity<List<FavoriteResponse>> showFavoritesList(@AuthenticationPrincipal MemberContext memberContext,
                                                                                @RequestParam @NotNull Double userLat,
                                                                                @RequestParam @NotNull Double userLng
                                                                                ) {
            
                    List<FavoriteResponse> favoriteResponses = favoriteService.getFavoritesList(memberContext.getId(), userLat, userLng);
            
                    return ResponseEntity.ok(favoriteResponses);
                }
            ```
            

### 3. @ResponseBody 어노테이션

- 정의 및 특징:
    - 핸들러 메서드(controller)에 붙일 수 있는 어노테이션으로, 어노테이션 추가만으로 **객체**를 **응답 본문**(body)에 **직접 담아** 전달할 수 있다.
    - 하지만 HTTP **헤더**는 설정하기가 어렵고, **상태 코드**는 `@ResponseStatus` 를 추가로 붙여야 설정이 가능하다.
- 사용 사례:
    - Spring MVC의 @RestController
        ```java
        @Slf4j
        @RestController
        public class ResponseBodyController {
        
          @ResponseStatus(HttpStatus.OK)
          @GetMapping("/response-body-json-v2")
          public HelloData responseBodyJsonV2() {
              HelloData helloData = new HelloData();
              helloData.setUsername("userA");
              helloData.setAge(20);
              return helloData;
           }
        }
        ```
        - `@RestController`를 사용하면 자동으로 모든 핸들러 메서드에 `@ResponseBody`가 적용된다.

### 4. 결론

#### 4-1. `ResponseEntity`와 `@ResponseBody`의 비교
  - `ResponseEntity`(`HttpEntity`)와 `@ResponseBody` 모두 핸들러 메서드(혹은 controller)가 데이터를 반환할 때 필요 시 `HttpMessageConverter` 에 의해 직렬화(객체 → 반환 타입)되는 것은 동일하다.
  - 그러나 `@ResponseBody`는 어노테이션 추가만으로 **간략한 HTTP 응답 바디 작성** 기능을 지원하는 반면, `ResponseEntity` 는 **보다 다양한 HTTP 옵션을 설정**할 수 있기 때문에 더 유연하다.
  - 따라서 바디 뿐만 아니라 **상태코드, 헤더** 또한 효과적으로 처리하거나, 조건에 따라 **상태코드**를 **동적으로** 반환해야 하는 경우,  **HTTP 응답**을 **래핑**한 `ResponseEntity` 를 사용하는 것이 유용하다.
#### 4-2. `ResponseEntity + @Controller`와 `ResponseEntity + @RestController`의 비교
  - `ResponseEntity`는 전체 응답을 나타내므로 `ResponseEntity` 사용 시 `@ResponseBody`를 명시할 필요는 없다.
  - 따라서 `@ResponseBody`를 포함한 `@RestController` 대신 `@Controller`를 사용해도 JSON, XML 등의 객체 반환이 정상적으로 이루어진다.
  - 하지만 [공식 문서](https://docs.spring.io/spring-framework/docs/4.3.3.RELEASE/spring-framework-reference/htmlsingle/#mvc-ann-restcontroller)에 따르면, @RestController는 @ResponseBody와 @Controller를 결합한 스테레오타입 주석이지만, 그 이상으로 컨트롤러에 더 많은 의미를 부여하고 프레임워크의 향후 릴리스에서 추가 의미를 전달할 수도 있다고 한다.
  - 따라서 목적과 구조의 명확성을 위해 뷰를 포함하는 전통적인 웹 애플리케이션에서는`@Controller`를, 순수한 REST API를 개발하는 경우, `@RestController`를 사용하고, 필요에 따라 `ResponseEntity`를 결합하는 것이 적합하다고 생각한다.


### References

- [[Spring Doc] HttpEntity<T>](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/HttpEntity.html#field.summary)
- [[Springboot] ResponseEntity, HttpEntity란?](https://esoongan.tistory.com/118)
- [ResponseEntity란?](https://bimmm.tistory.com/34)
- [[Spring] @ResponseEntity가 뭘까? @ResponseBody와 차이점?](https://dev-coco.tistory.com/99)
- [[Spring] @ResponseBody VS ResponseEntity<T> : 무엇을 사용할까?](https://ksh-coding.tistory.com/89)
- [[StackOverFlow] When use ResponseEntity<T> and @RestController for Spring RESTful applications](https://stackoverflow.com/questions/26549379/when-use-responseentityt-and-restcontroller-for-spring-restful-applications)

## https://github.com/WeeklyStudy/spring/issues/15 **HttpMessageConverter의 역할은 무엇인가?**

### 1.HttpMessageConverter의 역할
- `HandlerMethodArgumentResolver`(ArgumentResolver)와 `HandlerMethodReturnValueHandler`(ReturnValueHandler)는 HTTP 메세지 바디 데이터를 처리하지 못한다.
- `HttpMessageConverter`는 `ArgumentResolver`와 `ReturnValueHandler`가 처리하지 못하는 바디 데이터를 대신 처리해주는 인터페이스이다.
    - 요청의 경우, 메시지를 읽어서 객체로 바꾼 다음 컨트롤러에 파라미터로 넘겨주는 역할을 한다.
    - 응답의 경우, 컨트롤러에서 리턴 값을 가지고 HTTP 응답 본문 메시지에 담는 역할을 한다.
- `canRead()`, `canWrite()`, `read()`, `write()` 메서드가 존재한다.
    - canRead()와 canWrite()는 메시지 컨버터가 해당 클래스, 미디어 타입을 지원하는지 체크한다.
    - read()와 write()는 메시지 컨버터를 통해서 메시지를 읽거나 쓰는 기능이다.

### 2. HttpMessageConverter의 종류

- HttpMessageConverter에는 다음과 같은 10가지 구현체가 있다.
    - `ByteArrayHttpMessageConverter`: byte[] 형식으로 데이터를 변환한다.
    - `StringHttpMessageConverter`: 문자열을 변환합니다. 주로 text/plain, text/html, application/json, application/xml 등의 미디어 타입을 처리한다.
    - `ResourceHttpMessageConverter`: org.springframework.core.io.Resource를 변환한다.
    - `FormHttpMessageConverter`: application/x-www-form-urlencoded 또는 multipart/form-data 형식의 폼 데이터를 처리한다.
    - `MappingJackson2HttpMessageConverter`: JSON 데이터를 변환하는 데 사용되며, Jackson 라이브러리를 기반으로 한다.
    - `MappingJackson2XmlHttpMessageConverter`: XML 데이터를 변환하는 데 사용되며, Jackson 라이브러리를 기반으로 한다.
    - `Jaxb2RootElementHttpMessageConverter`: XML 데이터를 변환하는 데 사용되며, JAXB (Java Architecture for XML Binding)를 기반으로 한다.
    - `BufferedImageHttpMessageConverter`: java.awt.image.BufferedImage를 변환한다.
    - `AtomFeedHttpMessageConverter`: Atom 피드를 변환한다.
    - `RssChannelHttpMessageConverter`: RSS 채널을 변환한다.
- 이외에도 사용자 정의 `HttpMessageConverter`를 만들어서 특정 형식의 데이터를 HTTP 요청 및 응답으로 변환할 수 있다.

### 3. HttpMessageConverter의 동작 우선순위
- Message Converter 간의 동작 우선순위가 있다.

- 다음과 같은 우선순위를 기반으로 대상 **클래스 타입**과 **미디어 타입** 두 조건을 모두 체크해서 사용여부를 결정하고, 만약 만족하지 않으면 다음 메시지 컨버터로 우선순위가 넘어간다.
    - 0 : ByteArrayHttpMessageConverter
        - 클래스 타입: byte[]
        - 미디어타입: */*(전부 다 ok)
    - 1 : StringHttpMessageConverter
        - 클래스 타입: String
        - 미디어타입: */*
    - 2: MappingJackson2HttpMessageConverter
        - 클래스 타입: 객체 또는 HashMap
        - 미디어타입 application/json
### 4. HttpMessageConverter의 동작 과정

<img src="https://github.com/WeeklyStudy/spring/assets/63441091/5965aaf0-59db-4090-bda1-7a4c13b01407" width="600">

- `HttpMessageConverter`는 `ArgumentResolver`나 `ReturnValueHandler` 단계에서 작동한다.

- HTTP 요청 데이터 읽는 과정
  - `@RequestBody` 또는 `HttpEntity`(`RequestEntity`) 파라미터가 있을 때 메시지 Converter가 메시지를 읽을 수 있는지 확인하기 위해서 `canRead()`를 호출한다.
    - 대상 클래스 타입을 지원하는가?
    - Type 미디어 타입을 지원하는가?
  - `canRead()`에서 두 조건을 모두 만족하면, `read()`를 호출해서 객체를 반환한다.
- HTTP 응답 데이터 생성 과정
  - `@ReponseBody` 또는 `HttpEntity`(`ResponseEntity`) 파라미터가 있을 때 Converter가 메세지를 쓸 수 있는지 확인하기 위해서 canWrite()를 호출한다.
    - 리턴 대상 클래스 타입을 지원하는가?
    - HTTP 요청의 Accept 미디어 타입을 지원하는가?
  - `canWrite()`에서 두 조건을 모두 만족하면, `write()`를 호출해서 HTTP 응답 메시지 바디에 데이터를 생성한다.


### References

- [spring - MessageConverter 인터페이스의 구현체 종류 정리](https://data-study-clip.tistory.com/214)
- [[Spring] HttpMessageConverter가 적용되는 시점에 대해](https://bepoz-study-diary.tistory.com/374)
- [HttpMessageConverter](https://shirohoo.github.io/spring/spring-mvc/2021-11-29-http-message-converter/)
