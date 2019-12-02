---
title: Spring Boot 구동 원리
layout: post
description: ""
headline: null
modified: '2019-11-29'
category: SpringBoot
tags: [SpringBoot]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-11-29 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Spring Boot 구동 원리

Spring boot 는 빌드를 진행하면 Jar File이 나오게 된다. 이 Jar File 하나로 독립적으로 실행이 가능하다.

어떻게 이러한 방법이 가능한가 ?

과거에는 모든 클래스 (의존성 및 애플리케이션)를 하나로 압축하는 방법을 사용했다.
하지만 이러한 방법의 경우 어디서온 라이브러리인지, 어떠한 역할을 하는지 알 방법이 없었다.
또한 같은 파일이름이 존재하는 경우도 문제였다.

때문에 Spring Boot에서는 내장 Jar 방법으로 의존성을 관리한다.

예제로 사용했던 Spring Boot 프로젝트를 빌드한 후 Jar 파일의 압축을 풀어보면 아래와 같다.

{:.post-img}
![APP](/images/post/jar/app.png) 

BOOT-INFO, META-INFO, org라는 세 폴더로 이루어져있다.

우선 BOOT-INFO에는 직접 작성한 class file들과 의존성을 가지고 있는 jar file들로 이루어져 있다.

{:.post-img}
![Boot Info](/images/post/jar/boot-info.png) 

<br />

META-INFO에는 프로젝트 매니패스트 파일과 pom.xml로 구성되어 있다.

{:.post-img}
![Meta Info](/images/post/jar/meta-info.png) 

<br />

마지막으로 org 폴더의 하위 목록을 보면 아래와 같다.

{:.post-img}
![Loader](/images/post/jar/loader.png) 

<br />

여기서 조금 더 들여다 볼 곳은 META-INFO의 MANIFEST 파일이다.

#### MANIFEST.MF

```text
Manifest-Version: 1.0
Implementation-Title: http
Implementation-Version: 0.0.1-SNAPSHOT
Start-Class: spring.study.http.HttpApplication
Spring-Boot-Classes: BOOT-INF/classes/
Spring-Boot-Lib: BOOT-INF/lib/
Build-Jdk-Spec: 1.8
Spring-Boot-Version: 2.2.1.RELEASE
Created-By: Maven Archiver 3.4.0
Main-Class: org.springframework.boot.loader.JarLauncher
```

Java에서 Jar File의 MANIFEST에서 Main-Class는 Main 메서드가 존재하는 클래스로 설정된다.
하지만 Spring Boot 어느 프로젝트의 MANIFEST파일을 확인해도 똑같은 org.springframework.boot.loader.JarLauncher라는 클래스로 설정되어 있다.

위에서 확인하였든 Spring boot에서는 의존성 라이브러리들을 BOOT-INFO 폴더에서 Jar 형태로 가지고 있다. 하지만 Java에는 내장되어 있는 Jar 파일을 로딩하는 표준적인 방법이 존재하지 않는다.

따라서 Spring Boot 내부에서 이러한 Jar File을 로드하고 실행할 수 있도록 만들었다.

- org.springframework.boot.loader.jar.JarFile을 사용해서 내장 JAR를 읽는다.
- org.springframework.boot.loader.Launcher를 사용해서 실행한다.

이러한 이유로 MANIFEST의 Main-Class가 Main Method가 존재하는 클래스가 아니라 JarLauncher 클래스로 설정되어 있다.