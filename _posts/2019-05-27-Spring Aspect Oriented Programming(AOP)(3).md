---
title: Spring - AOP(3) - @AOP
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

# Spring AOP - @AOP

- Annotation 기반의 Spring @AOP
  
<br />

## Spring AOP 사용해보기  

- <a href="https://seongmun-hong.github.io/spring/Spring-Aspect-Oriented-Programming(AOP)(2)">이전 포스트</a> 에서 사용한 EventService, SimpleEventService, AppRunner가 있어야 합니다.

<br />

### 1. Dependency 추가
  
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

<br />

### 2. Aspect 정의, Pointcut 정의
 
- @Aspect
- 빈으로 등록해야 하므로 @Component도 추가.

#### PerformanceAspect.java  
  
```java
@Component
@Aspect
public class PerformanceAspect {

   // Around는 메서드를 감싸는 형태로 적용된다. (메서드호출 이전, 이후, 에러가 났을때 특정한 작업 등 다용도로 쓰일 수 있는 Annotation이다.
   // Around 뒤에는 Pointcut 이름 혹은 Pointcut을 직접 정의할 수 있다.
   @Around("execution(* com.springstudy..*.EventService.*(..))")
   public Object logPerformance(ProceedingJoinPoint pip) throws Throwable {
       // ProceedingJoinPoint pip 는 대상 메서드 자체를 의미
       long beginTime = System.currentTimeMillis();
       Object letVal = pip.proceed(); // 메서드 호출
       System.out.println(System.currentTimeMillis() - beginTime);
       return letVal;
   }

}
```

위와 같이 메서드를 실행한다면 코드의 중복 없이 시간을 출력하는 Aspect를 사용할 수 있다.  
- 하지만 EventService 전체의 메서드에 적용이 되었다.

<br />


## Pointcut 정의 방법

<br />

### 1. execution 

- 위의 방법과 같다.

<br />

### 2. @annotation

- Annotation을 만들어 Pointcut을 정의할 수 있다. 우선 Annotation을 생성한다.

#### PerformanceLogging.java  

```java
@Documented // javadoc 만들때 Dacumantation 될 수 있게 만듬.
@Target(ElementType.METHOD) // 타겟은 메서드
@Retention(RetentionPolicy.CLASS) // - Default가 CLASS
// Annotation에서  RetentionPolicy는 Annotation 정보를 얼마나 유지할 것인가 ? (Class File에 유지)
// RetentionPolicy.SOURCE 컴파일 하면 사라짐
public @interface PerformanceLogging {
}
```

위와 같이 Annotation을 생성한 후 Pointcut에 @annotation을 명시해준다.  

<br />

#### PerformanceAspect.java 
 
```java
@Component
@Aspect
public class PerformanceAspect {

    @Around("@annotation(PerformanceLogging)")
    // PerformanceLogging Annotation이 붙은 메서드에만 적용
    public Object logPerformance(ProceedingJoinPoint pip) throws Throwable {
        long beginTime = System.currentTimeMillis();
        Object letVal = pip.proceed();
        System.out.println(System.currentTimeMillis() - beginTime);
        return letVal;
    }

}
```

<br />

마지막으로 사용할 메서드에 PerformanceLogging Annotation을 적용한다. 

<br />

```java
@Service
public class SimpleEventService implements EventService {

    @PerformanceLogging
    @Override
    public void createEvent() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Created an event");
    }

    @PerformanceLogging
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

#### 위와 같이 Annotation을 만들어 사용한다면 좀더 명시적이고 원하는 메서드에 간편하게 사용이 가능하다!!
  
<br />

### 3. Bean 

- pointcut에 Bean을 명시하여 해당 Bean 전체에 적용할 수 있다.

#### PerformanceAspect.java

```java
@Component
@Aspect
public class PerformanceAspect {

   @Around("bean(simpleEventService)")
   // PerformanceLogging Annotation이 붙은 메서드에만 적용
   public Object logPerformance(ProceedingJoinPoint pip) throws Throwable {
       // ProceedingJoinPoint pip 는 대상 메서드 자체를 의미
       long beginTime = System.currentTimeMillis();
       Object letVal = pip.proceed(); // 메서드 호출
       System.out.println(System.currentTimeMillis() - beginTime);
       return letVal;
   }

}
```

<br />

## Advice의 종류 

위에서 사용한 Around Annotation은 메서드를 감싸는 형태로 적용되며 다용도로 쓰일 수 있는 Annotation이다.  
  
그 Advice는 아래와 같은 종류가 존재한다.

- @Before
- @AfterReturning
- @AfterThrowing
- @Around  
  
<br />

이 외의 pointcut과 advice에 대한 정의는 <a href="https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#aop- pointcuts">Spring Document</a>에서 확인할 수 있다.

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core