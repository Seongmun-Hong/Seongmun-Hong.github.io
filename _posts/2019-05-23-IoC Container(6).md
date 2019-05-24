---
title: IoC Container(6) - MessageSource
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

# MessageSource

ApplicationContext가 가지고 있는 국제화 (i18n) 기능(메세지 다국화)을 제공하는 인터페이스이다.  
  
Spring boot를 사용한다면 별 다른 설정없이 messages.properties를 만들어 사용 가능하다.
  
<br />
  
## 사용 방법  
  
### 1. messages.properties 파일을 생성한다.  
  
- messages.properties에 들어가는 내용은 Default Locale에 해당하는 message이다.
  
#### messages.properties  
  
```vim
greeting=안녕, {0}
```  
  
위에서 {0} 은 인자를 뜻한다.  
  
<br />

### 2. Locale 별 메세지를 작성한다.  
  
- 위의 en_US에 해당하는 메세지 프로퍼티를 만들어 본다. 
  
#### messages_en_US.properties  
  
```vim
greeting=Hello, {0}
```  
  
<br />
  
위의 설정을 가져와 출력하기 위한 Runner를 만든다.
  
#### MyRunner.java  
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    MessageSource messageSource;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(Locale.getDefault());
        System.out.println(messageSource.getMessage("greeting", new String[]{"Seongmun"}, Locale.getDefault()));
        System.out.println(messageSource.getMessage("greeting", new String[]{"Seongmun"}, Locale.KOREA));
        System.out.println(messageSource.getMessage("greeting", new String[]{"Seongmun"}, Locale.US));
    }

}
```   
  
<br />
 
프로젝트를 실행시켜 보면 결과는 다음과 같다.
  
```vim
ko_KR
안녕, Seongmun
안녕, Seongmun
Hello, Seongmun
```  
  
<br />

## Reloading 기능이 있는 MessageSource 사용하기

<br />  

### 1. Reloading 기능이 있는 MessageSource를 Bean으로 등록 

#### MessagesourceApplication.java
  
```java
@SpringBootApplication
public class MessagesourceApplication {

    @Bean
    public MessageSource messageSource() {
        ReloadableResourceBundleMessageSource messageSource = new ReloadableResourceBundleMessageSource();
        messageSource.setBasename("classpath:/messages");
        messageSource.setDefaultEncoding("UTF-8"); // messages.properties 의 인코딩 설정
        messageSource.setCacheSeconds(5); // 메세지를 캐시하는 시간을 설정 
        // 5초마다 메세지를 다시 읽어와서 캐시함 (수정해도 다시 가져오면 수정사항이 반영됨)
        return messageSource;
    }

    public static void main(String[] args) {
        SpringApplication.run(MessagesourceApplication.class, args);
    }

}
```  

<br />
  
### 2. 수정사항을 보기 위한 Runner코드 수정  

#### MyRunner.java  
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    MessageSource messageSource;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        while (true) {
            System.out.println(messageSource.getMessage("greeting", new String[]{"Seongmun"}, Locale.getDefault()));
            System.out.println(messageSource.getMessage("greeting", new String[]{"Seongmun"}, Locale.US));
            Thread.sleep(3000L);
        }

    }

}
```   
  
### 3. 프로젝트 실행 후 messages.properties 수정  
  
프로젝트를 실행하면 3초마다 greeting message를 가져와서 출력한다.  
  
```vim
...
안녕, Seongmun
Hello, Seongmun
안녕, Seongmun
Hello, Seongmun
...
```  

<br />

도중 messages.properties들을 수정한다.  

#### messages.properties

```vim
greeting=새로운 안녕, {0}
``` 

<br />
  
#### messages_en_US.properties

```vim
greeting=new Hello, {0}
```  
  
<br />
  
위와 같이 수정한 후 프로젝트를 빌드하면 출력 메세지가 변경되는 것을 확인할 수 있다.
  
결과는 아래와 같다.
  
```vim
...
안녕, Seongmun
Hello, Seongmun
새로운 안녕, Seongmun
new Hello, Seongmun
새로운 안녕, Seongmun
new Hello, Seongmun
...
```  