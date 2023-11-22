# https://github.com/WeeklyStudy/spring/issues/13 @RequestParam, @ModelAttribute, @RequestBody의 차이는?

## 💡HTTP 요청 데이터 전달 방법 3가지

### 1. 쿼리 파라미터 : `GET`

> URL의 쿼리 파라미터에 데이터를 포함해서 전달하는 방식이다.
> 

```
- 요청 URL: http://localhost:8080?username=hello&age=20
```

### 2. HTML Form : `POST`

> 메시지 바디에 쿼리 파리미터 형식으로 데이터를 전달하는 방식이다.
> 

```
- 요청 URL: http://localhost:8080/request-param
- content-type: application/x-www-form-urlencoded
- message body: username=hello&age=20
```

### 3. HTTP 메시지 바디 : `POST`, `PUT`, `PATCH`

> 메시지 바디에 JSON, XML, TEXT 형식의 데이터를 직접 담아서 전달하는 방식이다.
> 

```
- 요청 URL: http://localhost:8080/request-json
- content-type: application/json
- message body: {"username": "hello", "age": 20}
```

## 💡@RequestParam, @ModelAttribute, @RequestBody의 차이

### 1. @RequestParam → 1, 2번 처리 방법

> 1개의 HTTP 요청 파라미터를 **맵핑**한다.
> 
- 기본으로 `required=false`로 설정되어, 전송되지 않으면 400 에러가 발생한다.

### 2. @ModelAttribute → 1, 2번 처리 방법

> HTTP Body나 요청 파라미터들을 Java 객체에 **맵핑**한다.
> 
- `Query String`, `Form 데이터`만 처리할 수 있다.
- 객체는 `필드를 바인딩할 생성자` 혹은 `기본 생성자 + setter 메서드`가 필요하다.
    - 기본 생성자가 있으면 인스턴스를 우선 생성하고, setter 메서드로 바인딩을 시도한다.

### 3. @RequestBody → 3번 처리 방법

> HTTP 요청 Body(JSON, XML 등)을 `HttpMessageConverter`를 통해 Java 객체로 **변환**한다.
> 
- 내부적으로 `ObjectMapper`를 통해 JSON 값을 Java 객체로 역직렬화한다.
    - 역직렬화를 하기 위해 `기본 생성자`가 필요하다.
    - 바인딩을 위해 필드명을 알아 내려면, `getter`나 `setter` 중 1가지는 정의되어 있어야 한다.
        - `getter`나 `setter` 메서드가 모두 정의되어있지 않으면, `HttpMessageNotWritableException` 예외가 발생한다.

> **📍역직렬화**
> 
> 
> 생성자를 거치지 않고 리플렉션을 통해 객체를 구성하는 메커니즘
> 

> **📍Reflection(리플렉션)**
> 
> 
> 구체적인 클래스 타입을 알지 못해도 그 클래스의 메서드, 타입, 변수들에 접근할 수 있도록 해주는 **자바** API
> 
> - 객체 생성, 메서드 호출, 필드값 조회 및 설정
> - e.g. 스프링의 Dependency Injection, Jackson 라이브러리의 ObjectMapper

## Reference

- [신입 개발자 기술면접 질문 정리 - 백엔드](https://dev-coco.tistory.com/163)
- [[HTTP] Request 요청 데이터를 전달하는 방법](https://dd-developer.tistory.com/36)
- [[우아한테크코스] 기본 생성자가 필요한 이유 (Why the default constructor is needed) (feat. Jackson ObjectMapper + Reflection)](https://da-nyee.github.io/posts/woowacourse-why-the-default-constructor-is-needed/)
- [@RequestBody vs @ModelAttribute*](https://tecoble.techcourse.co.kr/post/2021-05-11-requestbody-modelattribute/)
- [스프링 ModelAttribute 데이터 바인딩 과정 이해하기](https://jaykaybaek.tistory.com/15)
- [자바 리플렉션 (Reflection) 기초](https://hudi.blog/java-reflection/)
- [@ModelAttribute을 setter 없이 사용할 수 있는 이유*](https://hyeon9mak.github.io/model-attribute-without-setter/)
- [Spring MVC - @ModelAttribute의 장점(@RequestParam와 @ModelAttribute)](https://galid1.tistory.com/769)

# https://github.com/WeeklyStudy/spring/issues/14 ResponseEntity와 @ResponseBody의 차이점은 무엇인가?

## 💡ResponseEntity<T>와 @ResponseBody의 차이

### 1. ResponseEntity<T>

> HTTP 응답을 만들어주기 위한 객체다.
> 

```java
@PostMapping("/")
public ResponseEntity<ResponseDto> method(...) {
    ...
    return ResponseEntity.ok()
                    .headers(headers)
                    .body(responseDto);
}
```

- `HttpEntity`를 상속 받고 있기 때문에 여러 HTTP 옵션을 설정할 수 있다.
- `ResponseEntity` 빌더를 통해서 여러 HTTP 옵션을 설정할 수 있다.

### 2. @ResponseBody

> HTTP 규격에 맞는 응답을 만들어주기 위한 어노테이션이다.
> 

```java
@PostMapping("/")
public ResponseDto method(...) {
    ...
    return responseDto;
}
```

- HTTP 헤더는 설정하기 어렵다.
- 상태 코드는 `@ResponseStatus`를 추가로 붙여야 설정할 수 있다.

## Reference

- [[Spring] @ResponseEntity가 뭘까? @ResponseBody와 차이점?](https://dev-coco.tistory.com/99)
- [[Spring] @ResponseBody VS ResponseEntity<T> : 무엇을 사용할까?](https://ksh-coding.tistory.com/89)
- [ResponseEntity - Spring Boot에서 Response를 만들자*](https://tecoble.techcourse.co.kr/post/2021-05-10-response-entity/)

# https://github.com/WeeklyStudy/spring/issues/15 HttpMessageConverter의 역할은 무엇인가?

## 💡HttpMessageConverter

> HTTP API처럼 JSON 데이터를 HTTP 메시지 바디에서 직접 읽거나 쓰는 경우 사용한다.
> 

### 1. HttpMessageConverter 종류

- ByteArrayHttpMessageConverter
    - 클래스 타입: `byte[]` , 미디어타입: `*/*`
- StringHttpMessageConverter
    - 클래스 타입: `String` , 미디어타입: `*/*`
- MappingJackson2HttpMessageConverter
    - 클래스 타입: `객체` 또는 `HashMap` , 미디어타입: `application/json` 관련

### 2. HttpMessageConverter 동작 과정

- 요청: `@RequestBody` 혹은 `HttpEntity`를 처리하는 `ArgumentResovler`가 `HttpMessageConverter`를 사용해서 필요한 객체 생성
- 응답: `@ResponseBody` 혹은 `HttpEntity`를 처리하는 `ReturnValueHandler`에서 `HttpMessageConverter`를 사용해서 응답 메시지 바디에 데이터 생성

## Reference

- [HttpMessageConverter 간단 정리](https://jaimemin.tistory.com/1823#recentComments)
