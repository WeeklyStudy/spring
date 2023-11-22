# #13 @RequestParam, @ModelAttribute, @RequestBody의 차이는?

## 1. HTTP 요청으로 데이터 전달 방법

### 1.1 GET-쿼리파라미터 방법

URL의 쿼리 파라미터에 데이터를 전달하는 방식

- HTTP 요청 메시지 헤더의 파라미터에 데이터를 전달
- 검색, 필터, 페이징 등에서 많이 사용
- 클라이언트에게 직접적으로 노출이 되기 때문에 민감한 정보는 제외해야함

```jsx
/url?username=kim&age=20
```

### 1.2 POST - HTML Form

HTML의 Form을 활용하여 서버로 데이터를 전송하는 방식

- 메시지 바디에 쿼리 파리미터 형식으로 전달
- 메세지 바디의 미디어 형식을 나타내는 content-type를 가짐
    
    ⇒ content-type: application/x-www-form-urlencoded
    
- 회원 가입, 상품 주문, HTML Form 사용

### 1.3 HTTP Request Message Body

HTTP 요청 메시지의 Body에 데이터를 담아 전달하는 방식

- HTTP message body에 데이터를 직접 담아서 요청
- 주로 `JSON`, `XML`, `TEXT` 형식의 데이터를 자주 사용한다.
    
    ⇒ 사실상 JSON이 표준이 되었음
    

## 2. @RequestParam, @ModelAttribute, @RequestBody 어노테이션의 차이

스프링 프레임워크에서 사용되는 어노테이션으로, 각각 다른 역할을 수행

- @RequestParam, @ModelAttribute - HTTP 요청 파라미터 정보를 매핑
- @RequestBody - HTTP 요청 바디의 정보를 매핑

### 2.1 @RequestParam

- HTTP 요청 메세지 파라미터를 매개변수로 매핑할 때 사용
- 쿼리 문자열이나 폼 데이터에서 특정한 파라미터 값을 추출할 때 사용
- 기본적으로 필수 파라미터로 간주되며(required=true), 필수가 아닌 경우 **`required`** 속성을 **`false`**로 설정 가능
- 단일 값 또는 여러 값들을 받을 수 있음

```java
@GetMapping("/example")
public String example(@RequestParam String param) {
    // ...
}
```

### 2.2 @ModelAttribute

- 요청 파라미터나 HTTP 세션 등에서 데이터를 바인딩하여 모델 객체를 생성
- 주로 HTML 폼 데이터를 객체에 매핑할 때 사용
- 모델 **객체의 필드는 요청 파라미터 이름과 일치**해야 함
    
    ⇒ 파라미터이름을 통해 setter 메서드 호출
    

```java
@PostMapping("/submitForm")
public String submitForm(@ModelAttribute User user) {
    // ...
}
```

### 2.3 @RequestBody

- HTTP 요청의 바디의 데이터를 자바 객체로 매핑할 때 사용
- JSON, XML, TEXT 등 형식의 데이터를 자바 객체로 변환
- POST 또는 PUT 메서드와 함께 사용되며 RESTful API에서 자주 사용
- 주로 **`application/json`** **`application/xml`**과 같은 미디어 타입과 함께 사용

💡HttpMessageConverter 가 요청 바디의 데이터를 대상클래스타입과 미디어 타입 확인하여 변환해줌

## 3. 정리
- HTTP 요청으로 데이터 전달 방법으로 GET-쿼리파라미터, POST-HTML Form, HTTP Request Message Body 방법이 있다.
- **`@RequestParam`**: URL 쿼리 문자열이나 폼 데이터에서 파라미터를 추출합니다.
- **`@ModelAttribute`**: 요청 파라미터나 다른 소스의 데이터를 객체에 바인딩하고 모델에 추가하여 뷰로 전달합니다.
- **`@RequestBody`**: HTTP 요청의 본문(body)을 자바 객체로 변환하여 메서드의 파라미터로 전달합니다.

</br>

# #14 ResponseEntity와 @ResponseBody의 차이점은 무엇인가?
### 1.1 **ResponseEntity**

HTTP 응답의 상태코드, 헤더, 본문(body) 등을 포괄적으로 다룰 수 있는 방식입니다.

- **동작**
    - **`ResponseEntity`**는 HTTP 응답의 모든 측면을 표현하는 클래스
    - 메서드의 반환 타입으로 사용되며, 응답의 상태코드, 헤더, 본문 등을 설정 가능

```java
javaCopy code
@GetMapping("/example")
public ResponseEntity<String> example() {
    HttpHeaders headers = new HttpHeaders();
    headers.add("Custom-Header", "foo");

    return new ResponseEntity<>("Hello, World!", headers, HttpStatus.OK);
}

```

### 1.2 ResponseBody

메서드가 반환하는 값을 HTTP 응답의 본문(body)으로 직접 사용하는 방식

- **동작**
    - **`@ResponseBody`** 어노테이션을 메서드에 적용하면 해당 메서드의 반환값이 HTTP 응답의 본문으로 사용
    - 객체를 반환하면 자동으로 객체를 변환하여 클라이언트에 전달
- **예시:**
    
    ```java
    javaCopy code
    @GetMapping("/example")
    @ResponseBody
    public String example() {
        return "Hello, World!";
    }
    ```
    

## 2. 정리

- **`ResponseEntity`**: HTTP 응답의 상태코드, 헤더, 본문 등을 표현하는 클래스로, 포괄적인 설정이 가능
- **`@ResponseBody`**: 메서드가 반환하는 값을 HTTP 응답의 본문으로 사용하며, 메서드에 직접 적용하여 사용

</br>

# #15 HttpMessageConverter 역할은 무엇인가?
## 1. HttpMessageConter란?

> 스프링에서 HTTP 요청과 응답의 데이터 변환을 처리하는 인터페이스  
> 미디어 타입 ↔ 자바 객체 변환 수행

### 1.1 역할

- 데이터 변환
    - HTTP **요청** 바디의 **데이터를** 읽어 **자바 객체로** 변환 (read 작업)
        
        ex) `JSON 또는 XML` → `자바 객체`
        
    - **자바** 객체, **데이터를** 적절한 형식의 **HTTP 응답 데이터로** 변환(write 작업)
        
        ex) `자바객체` → `JSON 또는 XML`
        
- 미디어 타입 지원
    - HTTP 요청 내부의 클라이언트 요청 미디어타입(Content-Type) 과 서버 응답 미디어타입(Accept)을 확인하여 처리
    - 어떤 형식의 데이터를 요청하고 응답하는지 결정하는 역할
- 다양한 데이터 형식 지원 및 처리
    - JSON, XML, HTML, 텍스트 등 다양한 형식 지원하며 데이터 형식에 따라 HttpMessageConverter 선택 처리

### 1.2 종류

미디어 타입과 데이터 형식에 따라 다양한 종류의 MessageConverter 존재

1. **`ByteArrayHttpMessageConverter`**
    - **`application/octet-stream`** 미디어 타입의 데이터를 처리하는데 사용
    - 주로 파일 업로드 및 다운로드와 관련된 작업에서 활용
2. **`StringHttpMessageConverter`**
    - 텍스트 데이터를 처리하는데 사용
    - 주로 **`text/plain`**, **`text/html`**, **`application/json`**, **`application/xml`** 등과 같은 미디어 타입의 데이터를 처리
3. **`FormHttpMessageConverter`**
    - **`application/x-www-form-urlencoded`** 미디어 타입의 데이터를 처리하는데 사용
    - 주로 HTML 폼 데이터와 관련된 작업에서 활용
4. **`MappingJackson2HttpMessageConverter`**
    - JSON 형식의 데이터를 처리하는데 사용
    - **Jackson 라이브러리를 기반**으로 하며, 자바 객체를 JSON으로 변환하거나 JSON을 자바 객체로 변환하는 작업에서 주로 사용
5. **`MappingJackson2XmlHttpMessageConverter`**
    - XML 형식의 데이터를 처리하는데 사용
    - **Jackson 라이브러리를 기반**으로 하며, 자바 객체를 XML로 변환하거나 XML을 자바 객체로 변환하는 작업에서 주로 사용
6. **`Jaxb2RootElementHttpMessageConverter`**
    - JAXB를 사용하여 XML을 처리하는데 사용
    - 자바 객체를 XML로 변환하거나 XML을 자바 객체로 변환하는 작업에서 주로 사용됩니다.
    
    💡JAXB(Java Architecture for XML Binding) :
    
    자바 프로그래밍 언어 애플리케이션에서 XML 컨텐츠의 사용을 간소화해주는 기술
    
7. **`BufferedImageHttpMessageConverter`**
    - 이미지 데이터를 처리하는데 사용
    - **`image/jpeg`**, **`image/png`** 등과 같은 이미지 미디어 타입의 데이터를 처리
8. **`ResourceHttpMessageConverter`**
    - **`org.springframework.core.io.Resource`** 타입의 데이터를 처리하는데 사용
    - 파일 다운로드와 관련된 작업에서 활용

💡 많이 사용되는 구현체  
  **`ByteArrayHttpMessageConverter`,** **`StringHttpMessageConverter`, `MappingJackson2HttpMessageConverter`, `MappingJackson2XmlHttpMessageConverter`**  

## 2. 동작 방법

### 2.1 HTTP 요청 처리

클라이언트가 보낸 HTTP 요청 메시지의 데이터를 읽어 변환하는 과정

- 과정
    1. @RequestBody 또는 HttpEntity 파라미터를 사용
    2. 메시지 컨버터가 읽을 수 있는지 확인을 위해 `canRead()`를 호출
        - 대상 클래스 타입, HTTP 요청 Content-Type 미디어 타입 지원 여부 확인
    3. `read()` 호출하여 HTTP 요청 바디를 변환

### 2.2 HTTP 응답 데이터 읽기

- 과정
    1. @ReponseBody 또는 HttpEntity로 값이 반환
    2. 메시지 컨버터가 메시지를 쓸 수 있는지 확인하기 위해서 `canWrite()`를 호출
        - 대상 클래스 타입, HTTP 요청 Accept 미디어 타입 지원 여부 확인
    3. `write()`를 호출하여 HTTP 응답 메시지 바디에 데이터 생성  

## 3. 정리

- `HttpMessageConverter`는 스프링에서 HTTP 요청과 응답의 메시지를 변환하는 역할을 하는 인터페이스이다.
- 스프링에서는 다양한 미디어 타입과 형식을 지원하기 위해 여러 종류의 구현체를 제공하고 있다.
- `ByteArrayHttpMessageConverter`, `StringHttpMessageConverter`, `MappingJackson2HttpMessageConverter`, `MappingJackson2XmlHttpMessageConverter` 등의 구현체가 자주 사용된다.
- HttpMessgeConverter 선택은 요청시에 Content-Type 미디어타입과 대상클래스 타입을 확인하고, 응답시에는 Accept 미디어 타입과 대상 클래스 타입 확인하여 선택한다.
