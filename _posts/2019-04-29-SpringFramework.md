---
title: Spring Framework 란?
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
 