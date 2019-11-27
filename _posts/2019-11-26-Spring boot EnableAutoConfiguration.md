---
title: Spring Boot @SpringBoot Application 의 원리와 이해
layout: post
description: ""
headline: null
modified: '2019-11-26'
category: SpringBoot
tags: [SpringBoot]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-11-26 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Spring Boot @SpringBootApplication 의 원리와 이해

Spring boot project를 생성한 후 main method는 아래와 같다.

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

@SpringBootApplication Annotation이 존재하며 그 안을 확인해 보면 아래와 같다.

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
    ...
```


위와 같이 구성되어 있다.
이렇듯 **@SpringBootApplication** 는 

- @SpringBootConfiguration
- @ComponentScan
- @EnableAutoConfiguration

3가지의 역할을 수행한다. 또한 내부적으로 2단계에 걸쳐서 Bean을 등록한다.

1. @ComponentScan 
    - @Component @Configuration @Repository @Service @Controller @RestController 
    - 해당 어노테이션이 선언된 하위 패키지에서 위와 같은 Annotation을 찾아서 Bean으로 등록한다.

2. @EnableAutoConfigure

Spring Boot의 의존성중 하나인 org.springframework.boot:spring-boot-autoconfigure 를 확인해 보자

{:.post-img}
![spring-boot-autoconfigure](/images/post/EnableAutoConfiguration/spring-boot-autuconfigure.png) 

위와 같이 META-INF에 spring-factories 파일이 존재한다. 이 파일을 확인해보면 아래와 같다.

```java
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
...
org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
...
```

위와 같이 **org.springframework.boot.autoconfigure.EnableAutoConfiguration**라는 Key값이 존재하며 하위에 많은 Class를 가지고 있다. 또한 해당 Class들은 상단에 @Configuration이라는 Annotation을 가지고 있다.(@Configuration은 Bean을 등록하는 Java 설정파일) 이러한 키값을 통하여 명시된 많은 Class들이 AutoConfiguration의 대상이 된다.

> 하지만 실제로 모든 Class들이 다 Bean으로 등록되진 않는다. 실제로 해당 클래스들을 확인해 보면 @ConditionalOnXxx... 와 같은 형태로 Condition에 적합한 경우 생성하고 생성하지 않게 설정되어 있다. 또한 각 Bean의 생성 우선순위 등 여러 다른 설정들도 포함한다.

<br />

위와 같은 과정을 거치기 때문에 @SpringBootApplication Annotation 하나로 Spring Boot Application을 실행할 수 있게 된다.