---
title: Spring - AOP(1) - Aspect Oriented Programming
layout: post
description: null
headline: null
modified: '2019-04-30'
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

# Aspect Oriented Programming (AOP)

<br />

## Native Approach 의 단점  
  
아래와 같은 Bank Class가 존재한다고 생각해보자.  
  
```java
Class Bank {

   private int balance;

   public void withdraw(int amout) {

      bankLogger.info("Withdraw - " + amount); // Logging

      transaction.begin(); // Transaction 

      balance = this.balance - amount;
      accountDao.saveBalance(balance);

      transaction.commit(); // Transaction 

   }
}
``` 

<br />

위의 코드에서 bankLogger는 Logging을 담당하며 transaction은 Transaction을 담당한다.   
이러한 Logging와 Transaction을 가지는 클래스가 여러개 존재한다면...

- 비지니스 로직과 로깅이 섞인다.  
   - 분리의 법칙을 위반한다.
   - 복잡하고 상호 의존적이다.
   - 코드의 결합성이 높아진다.
- 코드의 반복이 계속된다.  
  
<br /> 

### 흩어진 관심사 (Crosscutting Concerns)

{:.post-img}
![AOP_Crosscutting_Concerns](/images/post/AOP_crosscutting_concerns.png) 

<br />

## AOP 란?

- AOP는 Aspect Oriented Programming의 약자로 관점 지향 프로그래밍으로 불린다.
- Aspect Oriendted Programming (AOP)은 OOP를 보완하는 수단으로, 흩어진 Aspect를 모듈화 할 수 있는 프로그래밍 기법.
- AOP는 지원 기능(Logging, Transaction...)을 프로그램의 비즈니스 로직에서 분리하는 프로그래밍 패러다임이다.
   - 어떤 로직을 기준으로 핵심적인 관점(비지니스 로직), 부가적인 관점(Aspect)으로 나누어서 보고 그 관점을 기준으로 각각 모듈화하겠다는 것이다.
      - Aspect란 ?
         - 어플리케이션의 핵심 기능은 아니지만 어플리케이션을 구성하는 중요한 요소이고 부가적인 기능을 담당하는 요소
         - 여러 클래스에 걸쳐 있는 Enterprise Application Concern을 구현하는 클래스(Logging, Transaction, ...)

<br />

## Crosscutting Concerns에 AOP를 적용하면 ... 

{:.post-img}
![AOP_module](/images/post/AOP_modules.png) 
  
<br />

위와 같이 비지니스 로직과 Aspect를 분리하여 필요한 곳에 적용하여 사용한다.  
  

<br />

## AOP의 주요 개념  
  
- Aspect : 위에서 설명한 흩어진 관심사를 모듈화 한 것. (주로 부가기능을 모듈화한다)
- Target : Aspect가 가지고있는 Advice를 적용할 대상 (클래스, 메서드, ...)
- Advice : 실질적으로 어떤 일을 해야할 지에 대한 것, 실질적인 부가기능을 담은 구현체
- JointPoint : Advice가 적용될 위치, 끼어들 수 있는 지점. (메서드 실행 시점, 생성자 호출 직전, 필드에서 값을 꺼내올 때 등등 구체적이고 다양한 시점에 적용가능)
- PointCut : JointPoint의 상세한 스펙을 정의한 것. 'A란 메서드의 진입 시점에 호출할 것'과 같이 더욱 구체적으로 Advice가 실행될 지점을 정할 수 있음
  
<br />

## AOP 적용 방법  
  
1. 컴파일 시점 - 컴파일 시 Aspect가 포함된 바이트 코드로 컴파일
   - AspectJ로 컴파일을 한번 더 진행해야 함. 하지만 런타임에는 부하가 없음.
2. 로드 타임(Load Time Weaving) - 바이트 코드에는 Aspect가 포함되어 있지 않고 클래스 파일을 로드하는 시점에 Weaving하여 로드함 (JVM 메모리 상에서는 포함되어 로드됨)
   - Java Agent 설정, Load Time Weaver 등 별도의 설정이 필요. 클래스 로드 시점에서 약간의 부하.
3. 런타임 - 해당 타입의 클래스를 빈으로 만드는 과정에서 해당 타입의 Proxy Bean을 만든다
   - Bean을 생성할 때 약간의 부하

- Spring이 지원하는 AOP는 런타임시 적용한다.

<br />

### <a href="https://seongmun-hong.github.io/spring/Spring-Aspect-Oriented-Programming(AOP)(2)">Spring - AOP(2) - Proxy 기반 AOP 에서 계속...</a>

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core