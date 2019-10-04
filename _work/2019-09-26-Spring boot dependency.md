---
title: Spring Boot에서 dependency를 관리하는 방법
layout: post
description: ""
headline: null
modified: '2019-09-26'
category: SpringBoot
tags: [SpringBoot]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-09-26 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Spring Boot에서 dependency를 관리하는 방법

Spring Boot 프로젝트를 생성한 후 pom.xml은 다음과 같다.


<br /> 

### pom.xml  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>springboot.com</groupId>
    <artifactId>spring-boot-getting-started</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.3.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

Dependency에는 groupId와 artifactId만 존재하며 버전에 대한 정보는 없다. 이렇듯 버전정보가 없음에도 불구하고 어떻게 버전 관리를 하고 있는 것일까 ?


dependencymanager

장점 : 수많은 디펜던시를 직접 적어놓은 후 스프링 버전을 올리게 되면 문제가 발생할 가능성이 높다. 서드파티 라이브러리들이 어떤 버전과 어떤버전이 호환되는지 알 수 없다.

pom에서 지원하지 않는 버전들은 버전을 명시해야한다. 혹은 특별히 원하는 버전이 있는경우에는 명시할 수 있다.

바꾸는 방법 2가지
1. parent에 artifactId를 수정
2. dependencymanager element를 사용하여 설정

하지만 parent 설정에서 java version, encording 방법, resource 등 여러가지 다른 설정도 포함하기 때문에
바꿀 경우 많은 설정을 따로 진행해야 한다. 

## pom.xml

[공식 다운로드 페이지](https://www.anaconda.com/download/)