---
title: Servlet And JSP MVC Architecture
layout: post
description: null
headline: null
modified: '2019-04-30'
category: Spring
tags:
- Spring, MVC
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-04-30 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Integrating Servlet and JSP MVC Architecture
  
## 1. MVC Architecture
  
- <a href="https://seongmun-hong.github.io/spring/MVC1-MVC2">MVC1, MVC2 비교 포스팅 참조</a>  
  
{:.post-img}
![MVC2](/images/post/mvc2.png) 
  
### MVC FLow of Control  
  
{:.post-img}
![MVCFlowOfControl](/images/post/mvc_flow_of_control.png) 
  

### Flow  
  
1. Browser가 Servlet에게 Request를 보낸다.  
2. Servlet은  Request에 대한 결과가 들어가 있는 Bean을 Instance화 한다.  
3. 서블릿은 JSP 페이지와 커뮤니케이션을 한다.  
4. JSP 페이지는 Bean과 커뮤니케이션을 한다.  
5. JSP 페이지는 Browser에게 응답한다.  
    
  
## RequestDispatcher 란?
  
> RequestDispatcher는 클라이언트로부터 최초에 들어온 요청을 JSP/Servlet 내에서 원하는 자원으로 요청을 넘기는 역할을 수행하거나, 특정 자원에 처리를 요청하고 처리 결과를 얻어오는 기능을 수행하는 클래스
  
### RequestDispatcher의 존재 이유 
  
#### HttpServletResponse.sendRedirect() 와 차이점
  
sendRedirect() 메서드를 사용하여 지정한 경로로 페이지를 이동시킬 수 있다.  
하지만 sendRedirect()는 HTTP 리다이렉션을 이용하기 때문에 하나의 요청 범위 안에서 처리를 하는것이 아니라 브라우저에게 Response 후 브라우저측에서 지정받은 요청 경로로 다시 재요청을 하는 방식이기에 **두 번의 HTTP 트랜잭션**이 발생하며, 서버측에서는 **최초에 받은 요청중에 처리한 내용**을 리다이렉트 된 요청안에서 **공유할 수 없는 문제**가 있다.  
  
물론 sendRedirect() 에서도 쿠키나 세션등을 이용해 특정 상태를 유지하여 공유할 수는 있다.
하지만 좋은 방법이라고 할 수는 없다.
  
  
#### 정리
- HttpServletResponse : 현재 어플리케이션 이외에 다른 자원의 경로를 요청할 수 있다.
- RequestDispatcher : 현재 처리중인 서블릿이 속해 있는 웹 어플리케이션 범위 내에서만 요청을 제어할 수 있다.
  

### MVC + RequestDispatcher 시행 절차
  
1. 데이터를 포함하여 Bean을 정의한다.  
  - 적어도 하나의 getXXX 메서드를 사용하여  
2. Serlvet은 Request를 처리한다.  
  - Serlvet은 Request 매개변수를 읽고 누락되거나 잘못된 데이터를 확인하고 비지니스 로직을 실행  
3. 비니지스 로직을 실행하고 결과를 포함한 Bean을 얻는다.  
4. Request, Session or Servlet Context에 Bean을 저장  
  - Servlet은 요청의 결과를 나타내는 Bean을 저장하기 위해 요청, Session 또는 Servlet 컨텍스트 오브젝트에 대해 setAttribute를 호출  
5. JSP페이지로 Request를 Forwarding  
  - 서블릿은 상황에 맞는 JSP 페이지를 결정하고 RequestDispatcher의 forward 메소드를 사용하여 해당 페이지로 전송  
6. Bean에서 데이터를 추출한다.  
  - JSP 페이지는 Bean을 생성하거나 수정하지 않는다. Servlet이 생성 한 데이터를 단순히 추출하여 표시  
  
  
## 2. Beans  
  
### 특정 규칙을 따르는 Java 클래스  
  
- 반드시 0개의 인자를 갖는다.
  - Tomcat이 Beans를 만듬 - 어떤 인자를 넣을지 판단하지 않는다.
- Public field를 가져서는 안된다.
- getXXX, setXXX와 같은 메서드를 통하여야지만 접근할 수 있다.

## 3. Bean의 Scopes  
  
- **Scope**는 Bean이 저장되는 장소
- 이 장소에서 Bean이 **얼마나 오래 유지되는지를 컨트롤**함

#### 종류

1. Request  
  - Set, Get 하는 부분에서만 유효 (Servlet/JSP)
  
2. Session  
  - 다른 페이지에서도 얼마동안 같은 사용자에게 보여짐  
  - 다른 사용자는 볼 수 없음  
  
3. Application
  - 모든 사용자, 모든 페이지에서 볼 수 있음(ex - 방문자 수)
 