---
title: Spring - Spring Expression Language (SpEL)
layout: post
description: "About Spring Framework"
headline: null
modified: '2019-05-27'
category: Spring
tags: Spring -
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-05-27 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Spring Expression Language

<br />

- 객체 그래프를 조회하고 조작하는 기능을 제공한다.
- Unified EL​과 비슷하지만, 메소드 호출을 지원하며, 문자열 템플릿 기능도 제공한다.
- OGNL, MVEL, JBOss EL 등 자바에서 사용할 수 있는 여러 EL이 있지만, SpEL은 모든 스프링 프로젝트 전반에 걸쳐 사용할 EL로 만들었다.
- Spring 3.0 부터 지원한다.

<br />

## 문법  
  
- #{"표현식"}
- ${"Property"}
- 표현식 안에는 Property를 사용할 수 있지만 Property 안에 표현식은 사용할 수 없다.

### #{"표현식"}  

<br />

#### AppRunner.java
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Value("#{1 + 1}")
    int value;

    @Value("#{'Hello' + 'World'}")
    String greeting;

    @Value("#{1 eq 1}")
    boolean trueOrFalse;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("========Application Runner========");
        System.out.println(value);
        System.out.println(greeting);
        System.out.println(trueOrFalse);
    }

}
``` 

<br />
  
위와 같이 ApplicationRunner에서 실행해 보면 결과는 아래와 같다.
  
<br />

```vim
========Application Runner========
2
HelloWorld
true
``` 

<br />

### ${"Property"}  
  
<br />

Property에 있는 내용을 활용할 수 있다. 

#### Application.properties
  
```vim
my.value= 100
``` 
<br />

#### AppRunner.java
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Value("${my.value}")
    int myValue;

    @Value("#{${my.value} eq 100}")
    boolean isMyValue100;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("========Application Runner========");
        System.out.println(myValue);
        System.out.println(isMyValue100);
    }

}
``` 
<br />
  
위와 같이 ApplicationRunner에서 실행해 보면 결과는 아래와 같다. 
  
<br />

```vim
========Application Runner========
100
true
``` 

<br />

#### ! 표현식 안에는 Property를 사용할 수 있지만 Property 안에 표현식은 사용할 수 없다.  

<br />
  
### Bean의 내용을 활용  
  
<br />

SampleBean이라는 Bean을 만든 후 Field와 Method도 읽어올 수 있다.

#### SampleBean.java
  
```java
@Component
public class SampleBean {

    public int data = 200;

    public int getData() {
        return data;
    }

}
``` 
<br />

#### AppRunner.java
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Value("#{sampleBean.data}")
    int sampleBeanData;

    @Value("#{sampleBean.getData()}")
    int sampleBeanData2;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("========Application Runner========");
        System.out.println(sampleBeanData);
        System.out.println(sampleBeanData2);
    }

}
``` 
<br />
  
위와 같이 ApplicationRunner에서 실행해 보면 결과는 아래와 같다. 
  
<br />

```vim
========Application Runner========
200
200
``` 

<br />


### 다른 문법들  
 
- <a href="https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#expressions-language-ref">다른 많은 문법들이 존재하며 해당 링크에서 확인하여 사용할 수 있다.</a>  


<br />

### 사용되는 곳들...
  
- @Value 애노테이션
- @ConditionalOnExpression 애노테이션
- Spring Security
    - 메소드 시큐리티, @PreAuthorize, @PostAuthorize, @PreFilter, @PostFilter
    - XML 인터셉터 URL 설정
    - ...
- 스프링 데이터
    - @Query 애노테이션
- Thymeleaf
- ...

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core