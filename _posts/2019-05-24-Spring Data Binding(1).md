---
title: Spring - Data Binding(1) - PropertyEditor
layout: post
description: "About Spring Framework"
headline: null
modified: '2019-05-24'
category: Spring
tags: [Spring]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-05-24 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Data Binding 추상화 - PropertyEditor

<br />

## Data Binding 이란 ?  

- 기술적인 관점 : 프로퍼티 값을 타겟 객체에 설정하는 기능
- 사용자 관점 : 사용자 입력값을 애플리케이션 도메인 모델에 동적으로 변환해 넣어주는 기능
    - 입력값은 대부분 **문자열**인데, 그 값을 객체가 가지고 있는 int, long, Boolean, Date 등 심지어 Event, Book 같은 도메인 타입으로도 변환해서 넣어주는 기능.

<br />

## org.springframework.validation.DataBinder  

- Spring이 제공해주는 Data Bind interface  

<br />

## PropertyEditor  
  
- 주로 Web MVC에서 주로 사용하지만 그 뿐만 아니라 ApplicationContext의 XML 설정 파일에 입력한 정보(문자열)를 Bean이 가지고 있는 적절한 타입으로 변환할 때에도 쓰이며 Spring Expression Language에서도 사용된다.
    - Web MVC에서만 특화된 것이 아니고 여러곳에서 쓰이는 Spring 의 핵심 기술  
- **스프링 3.0 이전까지 DataBinder가 변환 작업 사용하던 인터페이스**
- Thread Safe하지 않다. (상태 정보 저장 하고 있음, 따라서 Bean으로 등록하여 사용하면 위험하다!)
- Object와 String 간의 변환만 할 수 있어, 사용 범위가 제한적이다.
  
<br />

## PropertyEditor 사용해 보기  

<br />
  
### 1. Event, Controller 생성  
  
#### Event.java
```java
public class Event {

    Integer id;
    String title;

}
```  

Construct, Getter, Setter, toString 생략

<br />

#### EventController.java  
  
```java
@RestController
public class EventController {

    @GetMapping("/event/{event}")
    public String getEvent(@PathVariable Event event) {
        System.out.println(event);
        return event.getId().toString();
    }
}
```  

<br />

### 2. MockMVC를 활용한 테스트 코드 작성  

#### test/EventControllerTest.java  
  
```java
@WebMvcTest
public class EventControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void getTest() throws Exception {
        mockMvc.perform(get("/event/1"))
                .andExpect(status().isOk())
                .andExpect(content().string("1"));
    }
    
}
```
  
<br />
  
결과는 아래와 같다.  
  
```vim
...
2019-05-24 12:41:03.131  WARN 33698 --- [           main] .w.s.m.s.DefaultHandlerExceptionResolver : Resolved [org.springframework.web.method.annotation.MethodArgumentConversionNotSupportedException: Failed to convert value of type 'java.lang.String' to required type 'com.springstudy.databinding.Event'; nested exception is java.lang.IllegalStateException: Cannot convert value of type 'java.lang.String' to required type 'com.springstudy.databinding.Event': no matching editors or conversion strategy found]
...
java.lang.AssertionError: Status 
Expected :200
Actual   :500
...
```  

<br />
  
### 테스트 코드로 부터 전달받은 값은 String 이지만 Controller에서는 Event 타입으로 받기 때문! 

<br />
  
## 문제 해결  
  
### PropertyEditor 구현  
  
#### EventEditor.java  
  
```java
public class EventEditor extends PropertyEditorSupport {

    @Override
    public String getAsText() {
        Event event = (Event) getValue();
        return event.getId().toString();
    }

    @Override
    public void setAsText(String text) throws IllegalArgumentException {
        setValue(new Event(Integer.parseInt(text)));
    }

}
```
  
<br />

### getValue(), setValue() 여기서 Value란 PropertyEditor가 가지고 있는 값  
### 서로 다른 Thread에게 공유가 된다 - Thread Safe하지 않다!!  

> 따라서 Bean으로 등록하지 않는 것이 안전하며 꼭 등록해야 할 경우 Thread Scope의 Bean으로 등록해서 사용해야 한다.  
  
## Bean으로 등록하지 않고 사용하는 방법  
  
### @InitBinder 사용
  
#### EventController.java  
  
```java
public class EventEditor extends PropertyEditorSupport {

    @Override
    public String getAsText() {
        Event event = (Event) getValue();
        return event.getId().toString();
    }

    @Override
    public void setAsText(String text) throws IllegalArgumentException {
        setValue(new Event(Integer.parseInt(text)));
    }

}
```
  
<br /> 

위와 같이 등록하여야 한다.

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core