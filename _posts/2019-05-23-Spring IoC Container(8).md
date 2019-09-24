---
title: IoC Container(8) - ResourceLoader
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

# ResourceLoader  
  
Resource를 Load하는 interface를 ApplicationContext가 상속받아 구현하여 ResourceLoader 역할을 수행할 수 있다.
  
## ResourceLoader를 사용한 간단한 예제  
  
### 1. Resource 폴더에 test.txt 생성  
  
#### test.txt  
  
```vim
hello
```  
  
<br />

### 2. Application Runner에서 ResourceLoader 사용해 Resource 가져오기
  
#### appRunner.java  
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ResourceLoader resourceLoader;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Resource resource = resourceLoader.getResource("classpath:test.txt");
        System.out.println(resource.exists());
        System.out.println(resource.getDescription());
    }

}
```  
  
<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core