---
title: IoC Container(2) - Spring IoC Container와 @Autowired
layout: post
description: "About Spring Framework"
headline: null
modified: '2019-05-21'
category: Spring
tags: [Spring, IoC Container]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-05-21 21:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Spring IoC Container와 Autowired  

<br />
    
## Autowired란 ?  
  
필요한 의존 객체의 **타입(Type)**에 해당하는 빈을 찾아 주입한다.  
  
<br />

### @Autowired  
  
- required: 기본값은 true (따라서 못 찾으면 애플리케이션 구동에 실패한다.)  
  
<br />
  
### 사용할 수 있는 위치  
  
<br />

#### 1. Constructor  
  
```java
@Service
public class BookService {

    BookRepository bookRepository;

    @Autowired
    public BookService(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
}
```
  
#### 만약 BookRepository가 @Component 혹은 @Repository로 선언되지 않았다면  
  
- **Parameter 0 of constructor in com.springstudy.demoppring51.BookService required a bean of type 'com.springstudy.demoppring51.BookRepository' that could not be found.** 라는 문구가 뜬다.
  > 0번째 타입에 맞는 파라미터 (BookRepository bookRepository)를 찾을 수 없다는 뜻. 

<br />

#### 2. Setter  
  
```java
@Service
public class BookService {

    BookRepository bookRepository;

    @Autowired
    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
}
```  
  
<br />

#### 3. Field  
  
```java
@Service
public class BookService {

    BookRepository bookRepository;

    @Autowired
    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
}
```  
  
<br />
  
  
### Bean 등록시...  
  
#### 해당 타입의 Bean이 없는 경우  
  
- 오류 -> required = false 를 통해 오류가 나지 않도록 할 수 있다.  
  
#### 해당 타입의 Bean이 한 개인 경우  
  
- 정상 등록  
  
#### 해당 타입의 Bean이 여러 개인 경우  
  
- Bean 이름으로 시도,
  - 같은 이름의 Bean 찾으면 해당 Bean 등록  
  
<br />  

### 같은 타입의 Bean이 여러 개인 경우 해결 방법  
  
다음과 같이 BookRepository는 interface이며 이를 상속받은 구현체가 ABookRepository, BBookRepository 2개가 존재한다면  
   
#### BookRepository.java  
  
```java
public interface BookRepository {
}
```  

<br />

#### ABookRepository.java  
  
```java
@Repository
public class ABookRepository implements BookRepository{
}

```  

<br />

#### BBookRepository.java  
  
```java
@Repository
public class BBookRepository implements BookRepository{
}
```  

<br />  
  
실행 후 결과는 다음과 같다.  
  
```vim
Field bookRepository in com.springstudy.demoppring51.BookService required a single bean, but 2 were found:
	- ABookRepository: defined in file [/Users/hongsungmoon/Desktop/Dev/SpringStudy/demospring51/target/classes/com/springstudy/demoppring51/ABookRepository.class]
	- BBookRepository: defined in file [/Users/hongsungmoon/Desktop/Dev/SpringStudy/demospring51/target/classes/com/springstudy/demoppring51/BBookRepository.class]


Action:

Consider marking one of the beans as @Primary, updating the consumer to accept multiple beans, or using @Qualifier to identify the bean that should be consumed
```
  
<br />  

### 이러한 경우  
  
<br />
  
#### 1. @Primary Annotation을 사용하여 해결  
  
- 여러개의 Bean이 존재할 경우 Primary Annotation가 붙은 Bean을 주입하도록 표시  
  
#### ABookRepository.java  
  
```java
@Repository @Primary
public class ABookRepository implements BookRepository{
}

```  

<br />
  
#### 2. @Qualifier Annotation을 사용하여 해결  
  
- 주입하고 싶은 Bean의 이름을 직접 입력
  
#### BookService.java  
  
```java
@Service
public class BookService {

    @Qualifier("aBookRepository")
    BookRepository bookRepository;

}

```  
  
> 이 방법 보다는 1번 방법이 조금 더 Type Safe 하므로 추천

<br />

#### 3. 해당 타입의 모든 Bean 주입  
  
#### BookService.java  
  
```java
@Service
public class BookService {

    @Autowired
    List<BookRepository> bookRepositories;

}

```  

<br />

#### 위의 방법들 중 1번 방법을 가장 추천한다.  
  
<br />
  
## @Autowired 동작 원리  
  
BeanPostProcessor라는 LifeCycle Interface의 구현체에 의해 동작한다.  
  
#### BeanPostProcessor  
  
- Bean의 instance를 만든 후 Bean의 초기화 LifeCycle(Bean initialization Life Cycle)이 존재.  
- Bean initialization Life Cycle 이전과 이후에 또 다른 부가적인 작업을 할 수 있는 LifeCycle CallBack  
  
##### Example 1. PostConstruct
  
```java
@Service
public class BookService {

    @Autowired
    BookRepository bookRepository;

    @PostConstruct
    public void setUp() {
        // Do Something..
    }

}
```  

<br />
  
##### Example 2. InitializingBean

```java
@Service
public class BookService implements InitializingBean {

    @Autowired
    BookRepository bookRepository;

    @Override
    public void afterPropertiesSet() throws Exception {
        // Do Something..
    }
    
}
``` 

<br />
  
<a href="https://seongmun-hong.github.io/spring/IoC-Container(1)">**Spring IoC Container와 Bean** </a> 에서 정리한 Bean 초기화 메소드 및 표준 순서의 11-14번은 다음과 같다. 

  **11. BeanPostProcessors의 postProcessBeforeInitialization 메소드**  
  **12. InitializingBean의 afterPropertiesSet**  
  **13. 커스텀 init-method 정의**  
  **14. BeanPostProcessors의 postProcessAfterInitialization 메소드**    
  
  
이러한 postProcessBeforeInitialization, postProcessAfterInitialization 2가지의 Callback 메서드를 더 제공해 주는데 이 중에서 **AutowiredAnnotationBeanPostProcessor**가 동작해서 @Autowired 라는 Annotation을 찾아 해당 타입의 Bean을 주입해 준다.  
  
>  Bean의 initialization 전에!! => postProcessBeforeInitialization에서 해준다.  
  
#### 따라서 위의 PostConstruct Annotation을 통한 작업은 postProcessAfterInitialization 단계에서 동작한다.  
  
<br />

### 조금 더 자세한 동작 원리...  
  
BeanFactory(Application Context)가 자신에게 등록되어 있는 BeanPostProcessor를 찾는다.(AutowiredAnnotationBeanPostProcessor)  
일반적인 Bean들에게 BeanPostProcessor에 있는 Autowired Annotation을 처리하는 로직을 적용한다.  
  
> AutowiredAnnotationBeanPostProcessor 는 기본적으로 등록이 되어있다.  
  
<br />

### AutowiredAnnotationBeanPostProcessor가 등록되어있는지 확인하는 방법  

<br />
  
#### ApplicationRunner를 만들어서 확인  
  
#### MyRunner.java  
  
```java
@Component
public class MyRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext applicationContext;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        AutowiredAnnotationBeanPostProcessor bean =
                applicationContext.getBean(AutowiredAnnotationBeanPostProcessor.class);
        System.out.println(bean);
    }

}
``` 
  
<br />
  
Runner를 만든 후 Application을 실행하면 다음과 같은 결과를 얻을 수 있다.  
  
```vim
...
2019-05-21 21:00:08.357  INFO 68188 --- [           main] c.s.d.DemoSpring51Application            : Started DemoSpring51Application in 2.987 seconds (JVM running for 3.992)
org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor@4ced35ed

``` 

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core