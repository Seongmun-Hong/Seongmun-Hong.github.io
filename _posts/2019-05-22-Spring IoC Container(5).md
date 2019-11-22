---
title: [Spring] IoC Container(5) - Environment - Profile과 Property
layout: post
description: "About Spring Framework"
headline: null
modified: '2019-05-22'
category: Spring
tags: [Spring, IoC Container]
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

# Environment
  
<a href="https://seongmun-hong.github.io/spring/IoC-Container(1)">지난 포스트(Spring IoC Container와 Bean)</a> 에서 공부하였듯 ApplicationContext는 BeanFactory 이외의 기능도 수행한다.  
  
ApplicationContext Interface를 확인해 보면 다음과 같은 상속관계를 갖는다.  
  
```java
public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory,
 HierarchicalBeanFactory, MessageSource, ApplicationEventPublisher, ResourcePatternResolver {
    ...
}
```  
  
<br />

위와 같은 클래스들 중 EnvironmentCapable는 2가지 기능을 가지고 있다.
  
1. Profile  
2. Property  
  
이러한 기능들을 살펴보고자 한다.  
  
<br />
  
## 1. Profile 
  
- Bean들의 묶음  
- Environment의 역할은 활성화할 프로파일 확인 및 설정한다.  
  
### 사용 이유  
  
- 특정 환경에서만 특정 Bean을 만들고 싶을때 (@Profile("test"))
    - test일 때만 생성
- 특정 환경이 아닐때만 Bean을 만들고 싶을때 (@Profile("!test"))
    - test가 아닐때만 생성  
  
> Profile 표현식 - !(not), &(and), |(or)
  
### Profile 확인  
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext ctx;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        Environment environment = ctx.getEnvironment();
        System.out.println(Arrays.toString(environment.getActiveProfiles()));
        System.out.println(Arrays.toString(environment.getDefaultProfiles()));

    }

}
```  
  
따로 설정한 프로파일이 없을 경우 출력은 아래와 같다.  
  
```vim
...
[]
[default]
```  
  
<br />  
  
## Profile 등록 방법  
  
### 1. Configuration Class에 @Profile을 설정  
  
#### BookRepository.java  
  
```java
public interface BookRepository {
}
```  
  
<br /> 
  
#### TestBookRepository.java  
  
```java
public class TestBookRepository implements BookRepository{
}
```  
  
<br /> 
  
#### TestConfiguration.java  
  
```java
@Configuration
@Profile("test")
public class TestConfiguration {

    @Bean
    public BookRepository bookRepository() {
        return new TestBookRepository();
    }

}
```  
  
위와 같이 프로젝트를 실행한다면 "test"라는 프로파일을 설정하지 않는다면 해당 Bean은 생성되지 않는다.  
  
### 2. ComponentScan 으로 등록되는 Bean Class에 @Profile을 설정  
  
TestConfiguation.java 를 지우고 TestBookRepository를 수정  
  
#### TestBookRepository.java  
  
```java
@Repository
@Profile("test")
public class TestBookRepository implements BookRepository{
}
```   
  
<br />
  
## Intellij에서 Profile 설정하기 
  
<br /> 
  
### 1. Active Profiles 설정  
   
{:.post-img}
![IntellijProfile1](/images/post/intellij_profile1.png)  
  
위의 그림과 같이 **EDIT Configurations...**를 선택  
  
{:.post-img}
![IntellijProfile2](/images/post/intellij_profile2.png)  
  
위의 그림과 같이 Active Profiles에 값 입력  
  
<br />

### 2. Environment - VM options에 설정  
  
EDIT Configurations...을 클릭 후 아래와 같이 설정

{:.post-img}
![IntellijProfile3](/images/post/intellij_profile2.png)  
  
Environment - VM options 에 -Dspring.profiles.avtive="test, test1" 와 같이 설정  
  

<br />
<br />
  
# Property  
  
- 다양한 방법으로 정의할 수 있는 설정값 (Key Value 쌍)
- Environment의 역할은 프로퍼티 소스 설정 및 프로퍼티 값 가져오기  
  
## Property의 우선순위  
  
- StandardServletEnvironment의 우선순위
    - ServletConfig 매개변수
    - ServletContext 매개변수
    - JNDI (java:comp/env/)
    - JVM 시스템 프로퍼티 (-Dkey=”value”)
    - JVM 시스템 환경 변수 (운영 체제 환경 변수)  

<br />
  
## Property를 설정 방법

<br />
  
### 1. -DKey="Value" 를 사용하여 설정  
  
### 2. properties 파일 사용  
- 기본적으로 Spring Boot에선  application.properties 소스를 지원  
  
새로 만든 프로퍼티 파일을 적용하는 방법은 다음과 같다.  
  
사용하고자 하는 properties파일을 만든 후 해당 파일을 통해 읽도록 설정해주면 된다.  
  
예를 들어 resource/app.properties 를 만든 후  
  
#### app.properties  
  
```vim
app.name=springEnvironment
```   
  
<br />
  
Application Class에서 다음과 같이 설정한다.  
  
```java
@SpringBootApplication
@PropertySource("classpath:/app.properties")
public class EnvironmentApplication {

    public static void main(String[] args) {
        SpringApplication.run(EnvironmentApplication.class, args);
    }

}
```

<br />
  
### 3. yml 파일 사용 

<br />
   
#### yml 파일은 PropertySource를 통해 특정 yml 파일을 불러올 수 없다. 
  
#### application.properties를 삭제 한 후 application.yml 파일을 생성 후 작성한다.  
  
```vim
spring:
  profiles:
    active: local

---
spring:
  profiles: test
 
app:
  name: testApplication

---
spring:
  profiles: prod

app:
  name: prodApplication
---
```  
  
- spring.profiles.active를 통하여 여러가지 설정파일을 동시에 설정할 수 있다.  
- 각 profile는 "---" 를 사용하여 구분한다.  

<br />
  
## Property 사용 방법  

<br />
  
### 1. Environment.getProperty  
  
#### appRunner.java  
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext ctx;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        Environment environment = ctx.getEnvironment();

        System.out.println(environment.getProperty("app.name"));

    }

}
```  
  
<br />   
  
### 2. @Value 사용  
  
#### appRunner.java  
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Value("${app.name}")
    String appName;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        System.out.println(appName);

    }

}
```  

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core