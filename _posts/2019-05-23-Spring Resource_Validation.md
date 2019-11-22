---
title: Spring - Resource와 Validation
layout: post
description: "About Spring Framework"
headline: null
modified: '2019-05-23'
category: Spring
tags: Spring -
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-05-23 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Spring의 Resource와 Validation의 추상화  

<br />

## Resource의 추상화  

<br />

### org.springframework.core.io.Resource
  
java.net.URL 클래스를 감싸 Low Level에 있는 Resource에 접근하능 기능을 만들었다.

<br />
  
### 추상화 한 이유
- 클래스패스 기준으로 리소스 읽어오는 기능 부재
- ServletContext를 기준으로 상대 경로로 읽어오는 기능 부재
- 새로운 핸들러를 등록하여 특별한 URL 접미사를 만들어 사용할 수는 있지만 구현이 복잡하고 편의성 메소드가 부족하다.
  
<br />

### 주요 메소드  
  
- getInputStream()  
- exitst()  
- isOpen()  
- getDescription(): 전체 경로 포함한 파일 이름 또는 실제 URL  

<br />
  
### 구현체  
  
- UrlResource: java.net.URL 참고, 기본으로 지원하는 프로토콜 http, https, ftp, file, jar.
- ClassPathResource: 지원하는 접두어 classpath: (ClassPath 기준)
- FileSystemResource (FileSystem 기준)
- ServletContextResource: 웹 애플리케이션 루트에서 상대 경로로 리소스 찾는다.
- ... 
  
<br />

### 리소스 읽어오기
- Resource의 타입은 locaion 문자열과 ApplicationContext의 타입에 따라 결정 된다.  
    - ClassPathXmlApplicationContext -> ClassPathResource  
    - FileSystemXmlApplicationContext -> FileSystemResource  
    - WebApplicationContext -> ServletContextResource  
  
- **ApplicationContext의 타입에 상관없이 리소스 타입을 강제하려면 java.net.URL 접두어(+ classpath:)중 하나를 사용할 수 있다.** 
    - classpath:me/whiteship/config.xml -> ClassPathResource  
    - file:///some/resource/path/config.xml -> FileSystemResource  
  
> 접두어를 사용한다면 어디에서 사용하더라도 명시적으로 확인이 가능하다. 따라서 이 방법을 추천하고 싶다.  
  
<br />
<br />
  
## Validation 추상화   
  
<br />

### org.springframework.validation.Validator  
  
- 애플리케이션에서 사용하는 객체 검증용 인터페이스  
  
<br />
  
### 특징  
- 어떤한 계층과도 관계가 없다. => 모든 계층(웹, 서비스, 데이터)에서 사용해도 좋다.
- 구현체 중 하나로, JSR-303(Bean Validation 1.0)과 JSR-349(Bean Validation 1.1)을 지원한다. (LocalValidatorFactoryBean)
- DataBinder에 들어가 바인딩 할 때 같이 사용되기도 한다.

<br />

### 스프링 부트 2.0.5 이상 버전을 사용할 때  
  
- LocalValidatorFactoryBean는 빈으로 자동 등록된다.
- JSR-380(Bean Validation 2.0.1) 구현체로 hibernate-validator 사용  

<br />

## Validator 사용  
  
<br />

### 1. Validator를 만들어서 사용  
  
#### Validation 대상 Class 생성  
  
#### Event.java  
  
```java
@Component
public class Event {

    Integer id;
    String title;

}
```  

- Getter, Setter는 생략함

<br />

#### Validator 생성  
  
#### EventValidator.java  
  
```java
public class EventValidator implements Validator {

    @Override
    public boolean supports(Class<?> aClass) {
        return Event.class.equals(aClass);
    }

    @Override
    public void validate(Object o, Errors errors) {
        ValidationUtils.rejectIfEmptyOrWhitespace(errors, "title", "nonempty", "Empty title is not allowed");
        // rejectIfEmptyOrWhitespace(errors, field, errorCode, defaultMessage)
        // errorCode = message resource에서 실제 메세지를 가져오기 위한 Key
        // defaultMessage = errorCode로 사용할 메세지를 찾지 못하였을때 사용할 메세지
    }

}
```  

<br />

#### Runner에서 검증  
  
### AppRunner.java  
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Override
    public void run(ApplicationArguments args) throws Exception {

       Event event = new Event();
       EventValidator eventValidator = new EventValidator();
       Errors errors = new BeanPropertyBindingResult(event, "event");
       // 실제로 Errors라는 interface는 자주 보겠지만 BeanPropertyBindingResult과 같은 구현체는
       // Spring mvc가 자동으로 생성해서 parameter에 전달해 줄 것이기 때문에 직접 사용할일은 없을 것이다.

       eventValidator.validate(event, errors);

       System.out.println(errors.hasErrors());

       errors.getAllErrors().forEach( e -> {
           System.out.println("===== error code =====");
           Arrays.stream(e.getCodes()).forEach(System.out::println);
           System.out.println(e.getDefaultMessage());
       });

    }

}
```  

<br />  

#### 출력 결과  

```vim
true
===== error code =====
nonempty.event.title
nonempty.title
nonempty.java.lang.String
nonempty
Empty title is not allowed
```  

<br />

### 2. LocalValidatorFactoryBean를 사용하여 Annotation을 통한 Validation  

- 위에서 설명하였듯 스프링 부트 2.0.5 이상 버전을 사용할 때 LocalValidatorFactoryBean는 빈으로 자동 등록된다.  
  
#### 검증 대상 Class에 Annotation 작성  
  
#### Event.java  
  
```java
public class Event {

    Integer id;
    String title;

    @NotNull @Min(0)
    Integer limit;

    @Email
    String email;

}
```  

- Getter, Setter는 생략  
  
<br />

#### Runner에서 검증  

#### AppRunner.java  
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    Validator validator;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        System.out.println(validator.getClass());

        Event event = new Event();
        event.setLimit(-1);
        event.setEmail("seongmun");
        Errors errors = new BeanPropertyBindingResult(event, "event");

        validator.validate(event, errors);

        System.out.println(errors.hasErrors());

        errors.getAllErrors().forEach( e -> {
            System.out.println("===== error code =====");
            Arrays.stream(e.getCodes()).forEach(System.out::println);
            System.out.println(e.getDefaultMessage());
        });

    }

}
```  

<br />
  
#### 출력 결과  
  
```vim
class org.springframework.validation.beanvalidation.LocalValidatorFactoryBean
true
===== error code =====
Min.event.limit
Min.limit
Min.java.lang.Integer
Min
반드시 0보다 같거나 커야 합니다.
===== error code =====
Email.event.email
Email.email
Email.java.lang.String
Email
이메일 주소가 유효하지 않습니다.
```  
  
<br />
  
## 정리  
  
- 복잡한 로직의 검증에는 validator를 만들어서 사용할 수 있으며 간단한 검증은 LocalValidatorFactoryBean를 사용할 수 있다.

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core