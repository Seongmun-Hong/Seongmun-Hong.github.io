---
title: Spring - AOP(2) - Proxy 기반 AOP
layout: post
description: null
headline: null
modified: '2019-05-27'
category: Spring
tags: [Spring, AOP]
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

# Spring AOP - Proxy 기반의 AOP  
  
<br />

## 스프링 AOP 특징
  
- **Proxy 기반의 AOP** 구현체
- **Spring Bean에만 AOP를 적용**할 수 있다.
- 모든 AOP 기능을 제공하는 것이 목적이 아니라 스프링 IoC와 연동하여 엔터프라이즈 애플리케이션에서 가장 흔한 문제에 대한 해결책을 제공하는 것이 목적이다.
  
<br />

## Proxy 패턴의 사용 이유 

- **기존 코드의 변경 없이 접근제어, 부가기능의 추가**를 위하여 사용한다.  
  
{:.post-img}
![Proxy_Pattern](/images/post/proxy_pattern.png)   
  
<br />

## Proxy 기반의 AOP 예제
  
<br />

### 1. AOP를 사용하지 않는다면 ...(crosscutting concern)

#### EventService.java

```java
public interface EventService {

    void createEvent();

    void publishEvent();

    void deleteEvent();
}
```

<br />

#### SimpleEventService.java

```java
@Service
public class SimpleEventService implements EventService {

    @Override
    public void createEvent() {

        System.out.println("Created an event");

    }

    @Override
    public void publishEvent() {

        System.out.println("Published an event");

    }

    @Override
    public void deleteEvent() {
        System.out.println("Deleted an event");
    }

}
```

<br />

### 실행을 위한 AppRunner 작성  
  
<br />

#### AppRunner.java  
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    EventService eventService;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        eventService.createEvent();
        eventService.publishEvent();
        eventService.deleteEvent();
    }

}
```

<br />

위와 같은 Application이 있을 때 

### EventService의 createEvent, publishEvent 메서드의 실행 시간을 알고싶다면...?  
  
SimpleEventService를 다음과 같이 수정할 수 있다.  
  
```java  
@Service
public class SimpleEventService implements EventService {

    @Override
    public void createEvent() {
        long beginTime = System.currentTimeMillis();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Created an event");
        System.out.println(System.currentTimeMillis() - beginTime);
    }

    @Override
    public void publishEvent() {
       long beginTime = System.currentTimeMillis();
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Published an event");
       System.out.println(System.currentTimeMillis() - beginTime);
    }

    @Override
    public void deleteEvent() {
        System.out.println("Deleted an event");
    }
}
```  
  
<br />  
  
#### 위와 같이 프로그래밍 하는 것이 Crosscutting Concern이다 ! 
- 시간을 측정하는 코드가 메서드 사이사이에 중복되어 포함된다.  

<br />

### 2. Proxy Pattern을 통한 해결  

#### SimpleEventService.java  
  
```java
@Service
public class SimpleEventService implements EventService {

    @Override
    public void createEvent() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Created an event");
    }

    @Override
    public void publishEvent() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Published an event");
    }

    @Override
    public void deleteEvent() {
        System.out.println("Deleted an event");
    }
}
```  

<br />

#### ProxySimpleEventService.java  
  
```java
@Primary
@Service
public class ProxySimpleEventService implements EventService {

    @Autowired
    SimpleEventService simpleEventService;

    @Override
    public void createEvent() {
        long beginTime = System.currentTimeMillis();
        simpleEventService.createEvent();
        System.out.println(System.currentTimeMillis() - beginTime);
    }

    @Override
    public void publishEvent() {
        long beginTime = System.currentTimeMillis();
        simpleEventService.publishEvent();
        System.out.println(System.currentTimeMillis() - beginTime);
    }

    @Override
    public void deleteEvent() {
        simpleEventService.deleteEvent();
    }
}
```   
  
<br />

위와 같이 Proxy Class로 감싸 해결할 수 있다!
  
<br />
 
### 위의 방식의 문제점  
  
1. 매번 Proxy  클래스를 작성한다.
2. 이러한 방법도 코드의 반복이 계속된다.
3. 여러 클래스 여러 메소드에 적용하려면...?
   - 모든 클래스에 Proxy 클래스를 만들어 중복된 코드를 심어야 한다.
4. 객체들 관계가 복잡해진다.
5. 위와 같은 방법은 컴파일시 AOP를 사용하는 방법이다.


<br />

### 그래서 등장한 것이 Spring AOP
- 스프링 IoC Container가 제공하는 기반 시설과 Dynamic Proxy를 사용하여 여러 복잡한 문제 해결하였다.
- Dynamic Proxy: 동적으로 Proxy 객체 생성하는 방법
   - 자바가 제공하는 방법은 인터페이스 기반 Proxy 생성.
   - CGlib은 클래스 기반 Proxy도 지원.
- 스프링 IoC: 기존 Bean을 대체하는 동적 Proxy Bean을 만들어 등록 시켜준다.
   - 클라이언트 코드 변경이 없다.
   - AbstractAutoProxyCreator​ (​BeanPostProcessor의 구현체로 새로운 Bean Instance를 조작할 수 있는 기능을 제공) 
      - Bean Instance를 만든 이후 그 Bean을 감싼 AOP Proxy Bean을 만들어 주는 역할을 수행한다. 
        

<br />

### <a href="https://seongmun-hong.github.io/spring/Spring-Aspect-Oriented-Programming(AOP)(3)">Spring - AOP (3) - @AOP 에서 계속...</a>

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core