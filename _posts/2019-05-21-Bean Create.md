---
title: Spring - Bean을 등록하는 여러가지 방법
layout: post
description: "About Create Beans"
headline: null
modified: '2019-05-21'
category: Spring
tags: [Spring, Bean]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-05-21 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Bean을 등록하는 여러가지 방법들  
  
  
<br />

### 우선 Spring boot Project 만들고  
### BookService, BookRepository를 생성   
<br />
#### BookRepository.java  
  
```java
public class BookRepository {
}
```  
  
#### BookService.java  
  
```java
public class BookService {

    BookRepository bookRepository;

    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }

}
```  

<br />

## 1. xml - Bean 태그 이용  
  
### Resource 폴더에 application.xml 생성 
  
#### application.xml  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>
    <bean id="bookService"
          class="com.springstudy.springapplicationcontext.BookService">
        <property name="bookRepository" ref="bookRepository"/>
        <!--name="bookRepository" 이부분에서 bookRepository는 bookService의 setter에서 가져옴-->
        <!--ref="bookRepository" 이부분에서 bookRepository는 bean id 에서 가져옴-->
    </bean>

    <bean id="bookRepository"
          class="com.springstudy.springapplicationcontext.BookRepository">
    </bean>
</beans>
```  
  
<br />

### xml 파일을 로드하여 Application 실행

#### DemoApplication.java  
  
```java
public class DemoApplication {

    public static void main(String[] args) {

       ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
       String[] beanDefinitionNames = context.getBeanDefinitionNames();
       System.out.println(Arrays.toString(beanDefinitionNames)); // bean name 출력
       BookService bookService = (BookService)context.getBean("bookService");
       System.out.println(bookService.bookRepository != null); 
       // bookService의 bookRepository의 의존성 주입 여부 확인

    }

}
```  

<br />

## 2. XML - componentScan 사용  
  
### application.xml 수정  
  
#### application.xml  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>
    <context:component-scan base-package="com.springstudy.springapplicationcontext"/>
    <!--Spring 2.5부터 가능했던 기능으로 bask-package에서부터 @Component 혹은 Component를 상속받은 -->
    <!--Repository, Service 등의 어노테이션을 찾아 빈으로 등록한다.-->
</beans>
```  
  
<br />

### Bean Class에 어노테이션 작성  
 
#### BookRepository.java
  
```java
@Repository
public class BookRepository {
}
```  
 
#### BookService.java
  
```java
@Service
public class BookService {

    @Autowired
    BookRepository bookRepository;

    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }

}
```  
  
<br />

## 3. Config파일을 통한 Bean 등록  
  
### ApplicationConfig.java 파일 생성 및 Bean 생성  
  
#### ApplicationConfig.java  
  
```java
@Configuration // bean을 등록하는 Configuration 이다.
public class ApplicationConfig {

   @Bean
   public BookRepository bookRepository() {
       return new BookRepository();
   }

   @Bean
   public BookService bookService() {
       BookService bookService = new BookService();
//        아래와 같이 직접 의존성을 주입하지 않아도 @Autowired를 통하여 주입할 수 있다.
//        하지만 BookService에 setter메서드가 있어야 가능하다 !
//        bookService.setBookRepository(bookRepository());
       return bookService;
   }

}
```  
  
<br />

### ApplicationConfig를 로드하여 Application 실행  
  
#### DemoApplication.java  
  
```java
public class DemoApplication {

    public static void main(String[] args) {

        SpringApplication.run(DemoApplication.class, args);

      // annotation을 통한 bean 등록
       ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);
       String[] beanDefinitionNames = context.getBeanDefinitionNames();
       System.out.println(Arrays.toString(beanDefinitionNames)); // bean name 출력
       BookService bookService = (BookService)context.getBean("bookService");
       System.out.println(bookService.bookRepository != null); 
       // bookService의 bookRepository의 의존성 주입 여부 확인

    }

}
```  
  
<br />

## 4. Config의 ComponentScan을 활용한 Bean 등록  
  
### ApplicationConfig.java 파일 생성 및 Bean 생성  
  
#### ApplicationConfig.java
  
```java
@Configuration // bean을 등록하는 Configuration 이다.
//@ComponentScan(basePackages = "com.springstudy.springapplicationcontext")
//위의 방법은 type safety 하지 않으므로 아래의 방법을 추천한다.
@ComponentScan(basePackageClasses = DemoApplication.class)
// DemoApplication 클래스가 위치한 곳부터 scan
public class ApplicationConfig {

}

```  

<br />
  
### BookService에 Autowired를 통한 DI 주입  
  
#### BookService.java
  
```java
@Service
public class BookService {

    @Autowired
    BookRepository bookRepository;

    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }

}
```  

<br />
  
  
## @SpringBootApplication Annotation 사용  
  
4번의 방법을 통하여 간단하게 Bean을 등록하는 방법은 @SpringBootApplication Annotation을 사용하는 것이다.  
  
해당 어노테이션의 클래스에 들어가 확인해 보면 아래와 같다.  
  
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
...
```  

<br />
  
위와 같이 ComponentScan 어노테이션이 들어가 있는 것을 확인할 수 있다.  
  
따라서 DemoApplication.java 파일을 아래와 같이 수정하면 된다.  
<br />
#### DemoApplication.java  
  
```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {

        SpringApplication.run(DemoApplication.class, args);

    }

}
```  