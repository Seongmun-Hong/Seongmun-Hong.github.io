---
title: [Spring] IoC-Container(4) - Bean의 Scope
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

# Bean의 Scope

<br />
    
## Scope  
  
<br />  
  
### Singleton  
  
- Application 전반에 걸쳐서 해당 Bean의 Instance가 오직 하나 뿐인 것.  

<br />  
  
### Prototype  
  
- Bean을 주입할 때 마다 새로운 Bean을 생성해서 주입.
  
<br />

## Prototype Bean이 Singleton Bean을 참조하면  
  
> 아무 문제가 없다.  
  
매번 새로운 ProtoType Bean이 가지고 있는 Singleton Bean은 매번 동일하다.  
  
<br />
  
## Singleton Bean이 ProtoType Bean을 참조하면  
  
#### Proto.java  
  
```java
@Component @Scope("prototype")
public class Proto {
}
```  

<br />

#### Single.java

```java
@Component
public class Single {

    @Autowired
    Proto proto;

    public Proto getProto() {
        return proto;
    }
    
}
```
  
<br />

#### AppRunner.java

```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext ctx;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        System.out.println("single.get");
        System.out.println(single.getProto());
        System.out.println(single.getProto());
        System.out.println(single.getProto());

    }

}
```  
  
<br />  
  
위와 같은 코드의 결과는 아래와 같다. 
  
```vim
proto by single
com.springstudy.beandemo.Proto@1804f60d
com.springstudy.beandemo.Proto@1804f60d
com.springstudy.beandemo.Proto@1804f60d
```  
  
<br />
  
Singleton Bean이 가지고 있는 Prototype의 bean을 호출하였는데 매번 동일한 Bean이 호출되었다.  

<br />
  
## 위와 같은 상황을 해결하기 위한 방법들  

<br />  
  
### 1. proxyMode를 설정  
  
- ScopedProxyMode.DEFAULT
    - Proxy를 사용하지 않는다는 뜻.  
- ScopedProxyMode.TARGET_CLASS
    - CGLIB의 Dynamic Proxy가 적용된다.
    - Target Class를 Proxy로 감싸달라는 뜻
        - 다른 Bean들이 해당 Bean을 사용할 때 이 Bean을 감싼 Proxy Bean을 사용하게 해라. 
        - <a href="https://en.wikipedia.org/wiki/Proxy_pattern">Proxy Patturn 참조</a>
    - 해당 Bean을 상속받은 Proxy Instance가 만들어 지고 그 Instance가 Bean으로 등록된다. 그 후 등록된 Bean을 주입해 준다.
  
> 왜 Proxy를 거쳐야 하나 ??  
    - 직접 참조하면 Bean을 바꿔줄 수 없다.  
  
{:.post-img}
![PrototypeProxy](/images/post/prototype_proxy.png)  
  
따라서 Proto 를 아래와 같이 수정하면 된다.
  
#### Proto.java
  
```java
@Component @Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS) 
public class Proto {
}
```  
  
<br /> 

### 2. ObjectProvider 사용  
  
Single에서 ObjectProvider를 사용하여 해결할 수 있다.  
  
#### Single.java  
  
```java
@Component
public class Single {

    @Autowired
    ObjectProvider<Proto> proto;

    public Proto getProto() {
        return proto.getIfAvailable();
    }

}
```  
  
<br /> 
  
Bean 선언 부분이 아닌 곳에 Spring Code가 삽입되므로 추천하고싶은 방법은 아니다.  
  
<br /> 
  
## Singleton 사용시 주의할 점
  
<br /> 
  
### 1. Property가 공유된다. (Thread safe 하지 않다)

<br /> 
  
### 2. Application Context 초기 구동시 생성된다.  
  
- Application 구동 시간이 조금 더 걸릴 수 있다.

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core