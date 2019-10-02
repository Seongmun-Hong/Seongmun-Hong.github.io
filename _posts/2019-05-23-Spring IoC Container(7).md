---
title: [Spring] IoC Container(7) - Application EventPublisher
layout: post
description: "About Spring Framework"
headline: null
modified: '2019-05-23'
category: Spring
tags: [Spring, IoC Container]
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

# ApplicationEventPublisher  
  
ApplicationContext는 이벤트 프로그래밍에 필요한 인터페이스 제공한다. (옵저버 패턴 구현체)
  
## Event를 만드는 방법  
  
Spring 4.2 미만의 버전에서는 ApplicationEvent를 상속받아야 했지만 이상의 버전에서는 상속받지 않아도 사용할 수 있다.  
  
### 4.2 버전 미만  
  
#### MyEvent.java  
  
```java
public class MyEvent extends ApplicationEvent {

    private int data;

    public MyEvent(Object source) {
        super(source);
    }

    public MyEvent(Object source, int data) {
        super(source);
        this.data = data;
    }

    public int getData() {
        return data;
    }
    
}
```  
  
<br />
  
### 4.2 버전 이상  
  
#### MyEvent.java  
  
```java
public class MyEvent {

    private int data;
    private Object source;

    public MyEvent(int data, Object source) {
        this.data = data;
        this.source = source;
    }

    public Object getSource() {
        return source;
    }

    public int getData() {
        return data;
    }

}
```   
  
<br />
  
- 위와 같은 코딩 방법이 Spring이 추구하는 철학이다. (비 침투성, non-invasive)
    - framework 코드가 나의 코드에 노출되지 않는 것.
    - POJO 기반의 프로그래밍 (테스트와 유지보수가 쉬워진다.)
- 가장 깔끔한 POJO
  
<br />
<br />

## Event 처리 방법(EventHandler)  
   
Spring 4.2 미만의 버전에서는 ApplicationListener 상속받아야 했지만 이상의 버전에서는 상속받지 않아도 사용할 수 있다.  
  
### 4.2 버전 미만  
  
#### MyEventHandler.java  
  
```java
@Component
public class MyEventHandler implements ApplicationListener<MyEvent> {

    @Override
    public void onApplicationEvent(MyEvent myEvent) {
        System.out.println("이벤트를 받았습니다. 데이터는 = " + myEvent.getData());
    }

}
```  
  
<br />
  
### 4.2 버전 이상  

- @EventListener를 사용하여 리스너를 만들 수 있다. 
  
#### MyEventHandler.java  
  
```java
@Component
public class MyEventHandler {

    @EventListener
    public void myHandler(MyEvent myEvent) {
        System.out.println("이벤트를 받았습니다. 데이터는 = " + myEvent.getData());
    }

}
```   
  
<br />

## 한 Event에 두개의 Listener가 존재한다면 ?  

<br />
  
테스트를 위하여 코드를 아래와 같이 수정한다.  

#### MyEventHandler.java  
  
```java
@Component
public class MyEventHandler {

    @EventListener
    public void myHandler(MyEvent myEvent) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("이벤트를 받았습니다. 데이터는 = " + myEvent.getData());
    }

}
```   
  
<br />

#### AnotherHandler.java  
  
```java
@Component
public class AnotherHandler {

    @EventListener
    public void handle(MyEvent myEvent) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("anotherHandler 데이터는 = " + myEvent.getData());
    }

}
```   
  
<br />  
  
실행한 결과는 아래와 같다.
  
```vim
Thread[main,5,main]
anotherHandler 데이터는 = 100
Thread[main,5,main]
이벤트를 받았습니다. 데이터는 = 100
```   
  
### 위와 같이 메인스레드에서 순차적으로 실행된다!  
  
<br />

### Listener의 순서를 정해주어 실행하는 방법(동기적)  
  
- 아래의 코드와 같이 @Order Annotation을 사용하여 순서를 정해줄 수 있다.

<br />

#### MyEventHandler.java  
  
```java
@Component
public class MyEventHandler {

    @EventListener
    @Order(Ordered.HIGHEST_PRECEDENCE + 2)
    public void myHandler(MyEvent myEvent) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("이벤트를 받았습니다. 데이터는 = " + myEvent.getData());
    }

}
```   
  
<br />

#### AnotherHandler.java  
  
```java
@Component
public class AnotherHandler {

    @EventListener
    @Order(Ordered.HIGHEST_PRECEDENCE)
    public void handle(MyEvent myEvent) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("anotherHandler 데이터는 = " + myEvent.getData());
    }

}
```   

<br />

결과는 다음과 같이 MyEventHandler가 먼저 동작한다.
  
```vim
Thread[main,5,main]
이벤트를 받았습니다. 데이터는 = 100
Thread[main,5,main]
anotherHandler 데이터는 = 100
```   
  
<br />
  
### Ordered  
  
- Ordered는 아래와 같이 선언되어 있다.  
 
```java
public interface Ordered {
    int HIGHEST_PRECEDENCE = -2147483648;
    int LOWEST_PRECEDENCE = 2147483647;

    int getOrder();
}
```  
  
<br />

작은 수 일수록 우선순위가 높은 것을 알 수 있다.
  
<br />
  
## 비 동기적 Listener 사용 방법  
  

- 아래의 코드와 같이 @Async와 @Enabled Annotation을 사용하여 순서를 정해줄 수 있다.

<br />

#### MyEventHandler.java  
  
```java
@Component
public class MyEventHandler {

    @EventListener
    @Async
    public void myHandler(MyEvent myEvent) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("이벤트를 받았습니다. 데이터는 = " + myEvent.getData());
    }

}
```   
  
<br />

#### AnotherHandler.java  
  
```java
@Component
public class AnotherHandler {

    @EventListener
    @Async
    public void handle(MyEvent myEvent) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("anotherHandler 데이터는 = " + myEvent.getData());
    }

}
```   

<br />

Listener에 @Async Annotation만을 사옹한다고 비동기적으로 동작하지 않는다.  
  
Application.java에서 @EnableAsync Annotation을 추가해야 비동기적으로 동작한다.  
  
#### EventPublisherApplication.java  
  
```java
@SpringBootApplication
@EnableAsync
public class EventpublisherApplication {

    public static void main(String[] args) {
        SpringApplication.run(EventpublisherApplication.class, args);
    }

}
```  
  
<br />

결과는 아래와 같이 비동기적으로 동작한다. 
  
```vim
Thread[task-2,5,main]
Thread[task-1,5,main]
이벤트를 받았습니다. 데이터는 = 100
anotherHandler 데이터는 = 100
```   
  
<br />
  
### 원래는 ThreadPool 관련 설정을 더 진행해야 한다. 나중에 Async 관련 포스트에 추가할 예정이다.
  
<br />
<br />
  
## ApplicationContext 관련 Event를 처리하는 Handler 추가해보기  
  
#### MyEventHandler.java  
  
```java
@Component
public class MyEventHandler {

    @EventListener
    @Async
    public void myHandler(MyEvent myEvent) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("이벤트를 받았습니다. 데이터는 = " + myEvent.getData());
    }

    @EventListener
    @Async
    public void handle(ContextRefreshedEvent event) {
        System.out.println("ContextRefreshedEvent");
    }

    @EventListener
    @Async
    public void handle(ContextClosedEvent event) {
        System.out.println("ContextClosedEvent");
    }

}
```

<br />

위와 같이 ContextRefreshedEvent와 ContextClosedEvent 이벤트 리스너를 작성하고 Application을 실행하고 종료하면 해당 리스너가 동작하는 것을 알 수 있다.  

```vim
ContextRefreshedEvent
2019-05-23 16:27:56.287  INFO 14047 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2019-05-23 16:27:56.291  INFO 14047 --- [           main] c.s.e.EventpublisherApplication          : Started EventpublisherApplication in 3.267 seconds (JVM running for 4.636)
Thread[task-3,5,main]
이벤트를 받았습니다. 데이터는 = 100
Thread[task-2,5,main]
anotherHandler 데이터는 = 100
ContextClosedEvent
2019-05-23 16:28:01.565  INFO 14047 --- [      Thread-10] o.s.s.concurrent.ThreadPoolTaskExecutor  : Shutting down ExecutorService 'applicationTaskExecutor'

Process finished with exit code 130 (interrupted by signal 2: SIGINT)
```  
  
Spring boot 가 이와 같은 Event들을 더 많이 확장하여 더 다양한 Event들을 제공해 준다.  
  
<br />

### 스프링이 제공하는 기본 이벤트  
  
- ContextRefreshedEvent
    - ApplicationContext를 초기화 했거나 refresh 했을 때 발생.
- ContextStartedEvent
    - ApplicationContext를 start()하여 라이프사이클 Bean들이 시작 신호를 받은 시점에 발생.
- ContextStoppedEvent
    - ApplicationContext를 stop()하여 라이프사이클 Bean들이 정지 신호를 받은 시점에 발생.
- ContextClosedEvent
    - ApplicationContext를 close()하여 싱글톤 Bean이 소멸되는 시점에 발생.
- RequestHandledEvent
    - HTTP 요청을 처리했을 때 발생.

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core