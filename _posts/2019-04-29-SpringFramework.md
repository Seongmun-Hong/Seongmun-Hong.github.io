---
title: Spring Framework란?
layout: post
description: "About Spring Framework"
headline: null
modified: '2019-04-29'
category: Spring
tags: [Spring]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-04-29 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Spring Framework  
  
## 1. Framework란 ?  
Framework는 구조 품질을 보장  
 - SW구조 그리고 기반되는 클래스를 제공  
   
Framework는 어플리케이션 구조 및 코드의 상당 부분을 제공하며, 개발자는 어플리케이션의 핵심 로직에 집중할 수 있다.

### 1-1. Library VS Framework  
 - 중요한 차이점은 "**Inversion of Control**" (제어의 역전)

#### Library
 - 클래스의 집합으로서 코드의 재사용성을 지원한다.
 - 제어의 주체는 개발자이다. (코드에서 Library 함수를 호출한다)

  사용자가 라이브러리 함수를 호출  
  
#### Framework  
 - 제어의 주체가 Framework이다.(프레임 워크에서 개발자의 코드를 호출한다 - 제어의 역전)  
 - Framework에서 기본적인 골격을 잡아놓았기 때문에 개발자는 제어의 흐름에 맞게 코드를 작성해 두면 Framework에서 호출한다.  

  프레임워크가 개발자의 코드를 호출  
  
{:.post-img}
![LibraryVSFramework](/images/post/libraryVSframework.png) 

## 2. Spring이란 ?  

### Lightweight Java Application Framework(Open source)  
  
**POJO** 기반의 엔터프라이즈 어플리케이션 개발을 쉽고 편하게 만들어 주는 라이브러리  
 - 자바 어플리케이션을 개발하는데 필요한 **하부구조**(infrastructure)를 포괄적으로 제공  
 - 스프링이 하부 구조를 처리하므로 개발자는 **어플리케이션 개발에 집중**  
   
## 3. Spring 구성 요소
  
{:.post-img}
![SpringStructure](/images/post/spring_structure.png) 

## Spring Framework의 디자인 철학
- 모든 기능은 개발자의 몫 (스프링이 특정 영속화 기술을 강요하지 않는다.)
- 다양한 관점을 지원한다. (유연성)
- 하위 호환성을 지킨다.
- API를 신중하게 설계한다.
- 높은 수준의 코드를 지향한다.
 
 ## Spring의 간단한 역사
- 2003년 등장 (개발은 이미 그 이전부터 진행)
  - 등장시 Java EE 표준과 싸우는 것처럼 보였지만 실제론 JEE 스팩 구현 모음체
  - Servlet, WebSocket, Bean Validation, JPA, Dependency Injection, ...
- 최근까지 주로 서블릿 기반 애플리케이션을 만들 때 사용해 왔다.
- 스프링 5부터는 WebFlux 지원으로 서블릿 기반이 아닌 서버 애플리케이션도 개발할 수 있게 되었다.