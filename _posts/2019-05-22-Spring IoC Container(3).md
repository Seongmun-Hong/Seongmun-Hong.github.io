---
title: [Spring] IoC-Container(3) - @Component와 ConponentScan
layout: post
description: "About Spring Framework"
headline: null
modified: '2019-05-22'
category: Spring
tags: [Spring, IoC-Container]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-05-22 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# @Component와 ConponentScan  

<br />
    
## ConponentScan  
  
1. Component를 스캔한다.  
    - basePackage는 스캔을 시작할 패키지를 정의  
        - 패키지 이름을 String으로 작성하기 때문에 Type Safe하지 않다.  
    - 조금 더 Type Safe한 basePackageClasses가 존재한다.  
    - basePackage가 없다면 Application 시작 지점부터(해당 패키지와 그 이하에 있는 패키지까지) 스캔을 시작한다.  
<br />  
2. Filter - 어떤 Annotation을 스캔할지, 하지 않을지 설정할 수 있다.
  
<br />

## @Component  
  
- @Service
- @Repository
- @Controller
- @Contiguration

<br />

### Function을 사용한 Bean 등록 방법  
  
- **Application 구동 시** 성능상의 이점이 존재
    - Reflection, CGLIB 등을 사용하지 않기 때문!
  
<br />

#### DemoSpring51Application.java
  
```java
@SpringBootApplication
public class DemoSpring51Application {

    public static void main(String[] args) {

        var app = new SpringApplication(DemoSpring51Application.class);
        app.addInitializers((ApplicationContextInitializer <GenericApplicationContext>) ctx -> {
            if(조건) {
                ctx.registerBean(MyService.class);
            }
            ctx.registerBean(ApplicationRunner.class, () -> args1 -> 
                System.out.println("Functional Bean Definition!!"));
        });
        app.run(args);
    }

}
```  
  
위와 같이 코드를 추가할 수 있어 조금 더 유연한 빈 생성이 가능할 수 있다.  
  
<br />
  
## ComponentScan 구동  
  
> BeanFactoryPostProcessor를 구현한 ConfigurationClassPostProcessor에서 처리된다.  
  
BeanPostProcessor과 비슷하지만 실행 시점이 다르다.  
  
다른 Bean들(사용자가 등록하고자 선언한 Bean들 - @Bean, @Component, function 등)을 등록하기 이전에 적용한다.

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core