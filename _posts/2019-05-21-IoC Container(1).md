---
title: IoC Container(1) - Spring IoC Container와 Bean
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
lastmod : 2019-04-29 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Spring IoC Container와 Bean  

<br />
    
## IoC(Inversion of Control)란 ?  

Inversion of Control: 의존 관계 주입(Dependency Injection)이라고도 하며, 어떤 객체가 사용하는 **의존 객체를 직접 만들어 사용하는게 아니라, 주입 받아 사용하는 방법을 말 함**  
  
### Spring IoC Container

- BeanFactory  
  - Spring IoC Container 가장 최상위에 있는 Interface로 가장 핵심이 된다.  
- 애플리케이션 컴포넌트의 중앙 저장소.  
- **빈 설정 소스**로 부터 **빈 정의**를 읽어들이고, **빈을 구성하고 제공**한다  
   
### BeanFactory 표준 Bean LifeCycle Interface  
  
- 초기화 메소드 및 표준 순서는 다음과 같다.  
   
  1. BeanNameAware setBeanName  
  2. BeanClassLoaderAware의 setBeanClassLoader  
  3. BeanFactoryAware의 setBeanFactory  
  4. EnvironmentAware의 setEnvironment  
  5. EmbeddedValueResolverAware의 setEmbeddedValueResolver  
  6. ResourceLoaderAware setResourceLoader (응용 프로그램 컨텍스트에서 실행될 때만 적용 가능)  
  7. ApplicationEventPublisherAware setApplicationEventPublisher (응용 프로그램 컨텍스트에서 실행될 때만 적용 가능)  
  8. MessageSourceAware setMessageSource (응용 프로그램 컨텍스트에서 실행될 때만 적용 가능)  
  9. ApplicationContextAware setApplicationContext (응용 프로그램 컨텍스트에서 실행될 때만 적용 가능)  
  10. ServletContextAware setServletContext (웹 애플리케이션 컨텍스트에서 실행될 때만 적용 가능)  
  11. BeanPostProcessors의 postProcessBeforeInitialization 메소드  
  12. InitializingBean의 afterPropertiesSet  
  13. 커스텀 init-methid 정의  
  14. BeanPostProcessors의 postProcessAfterInitialization 메소드  
  
<br />

- Bean 팩토리를 종료 할 때 다음 라이프 사이클 메소드가 적용된다.  
  
  1. DestructionAwareBeanPostProcessors의 postProcessBeforeDestruction 메소드  
  2. DisposableBean's destroy  
  3. 커스텀 destroy-method 정의  
  
<br />

### Bean  
  
- 스프링 IoC 컨테이너가 관리 하는 객체
- Scope  
  - Singleton - Default값, 하나의 객체만 생성하여 사용  
    - 한번 Container에 만들고 사용하기 때문에 성능 최적화(Protorype에 비해)  
  - Prototype - 매번 다른 객체를 사용  
- LifeCycle Interface  
  - interface의 존재로 빈을 만들기 전, 후 커스텀 코드와 같은 행위가 가능함  
  
```java
@Service
public class bookService {
  ...
  @PostConstruct
  public void postConstruct() {
    System.out.println("Book Service Post Construct")
  }
}
```  
  
<br />

### ApplicationContext  
  
- 실제로 가장 많이 사용하게되는 BeanFactory  
- BeanFactory를 상속받음  
- BeanFactory의 기능에 더하여 다른 많은 기능들을 가지고 있는 Interface (이벤트 발행, 리소스 로딩, 메세지 소스 처리 기능(i18n) 등 ...)  
  
1. ClassPathXmlApplicationContext (XML)  
2. AnnotationConfigApplicationContext (JAVA)  
  
<br />

### Spring IoC Container의 역할  
  
- 빈 인스턴스 생성  
- 의존 관계 설정  
- 빈 제공  
  
<br />

#### Bean 등록 및 컴포넌트 스캔 방법
- <a href="https://seongmun-hong.github.io/spring/Bean-Create">Spring Bean 관련 포스팅 참조</a>  
  


