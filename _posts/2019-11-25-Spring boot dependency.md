---
title: Spring Boot에서 dependency를 관리하는 방법
layout: post
description: ""
headline: null
modified: '2019-11-25'
category: SpringBoot
tags: [SpringBoot]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-11-25 12:00:00
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

Dependency에는 groupId와 artifactId만 존재하며 버전에 대한 정보는 없다. 이렇듯 버전정보가 없음에도 불구하고 어떻게 버전 관리를 하고 있는 것일까?

> 의존성 관리 기능이 이를 가능하게 한다.

### pom.xml  
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.3.RELEASE</version>
</parent>
```

위의 pom.xml에서 위의 부분을 확인한다. artifactId를 타고 들어가면 spring-boot-starter-parent-버전명.RELEASE.pom 파일을 확인할 수 있다.

### spring-boot-starter-parent-버전명.RELEASE.pom 

```xml
<?xml version="1.0" encoding="utf-8"?><project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>2.0.3.RELEASE</version>
        <relativePath>../../spring-boot-dependencies</relativePath>
    </parent>
    ...
```

위와 같이 구성되어 있는데 다시 한번 parent의 artifactId인 **spring-boot-dependencies**를 타고 들어가면 버전 정보들이 들어있다.

### spring-boot-dependencies-버전명.RELEASE.pom

```xml
<?xml version="1.0" encoding="utf-8"?><project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.0.3.RELEASE</version>
    <packaging>pom</packaging>
    <name>Spring Boot Dependencies</name>
    <description>Spring Boot Dependencies</description>
    <url>https://projects.spring.io/spring-boot/#</url>
    <licenses>
        <license>
            <name>Apache License, Version 2.0</name>
            <url>http://www.apache.org/licenses/LICENSE-2.0</url>
        </license>
    </licenses>
    <developers>
        <developer>
            <name>Pivotal</name>
            <email>info@pivotal.io</email>
            <organization>Pivotal Software, Inc.</organization>
            <organizationUrl>http://www.spring.io</organizationUrl>
        </developer>
    </developers>
    <scm>
        <url>https://github.com/spring-projects/spring-boot</url>
    </scm>
    <properties>
        ...
    </properties>
    <dependencyManagement>
        <dependencies>
            <dependency>
            ...

```

dependencyManager 영역에 모든 버전정보들이 명시되어 있다. dependencyManager 영역에 존재하는 기능을 사용하게 된다면 직접 버전을 명시하지 않아도 이곳에서 버전정보를 가져와 사용하게 된다.

#### Dependency Management 기능

- 사용자가 직접 관리해야 할 의존성이 줄어든다.
    - 수많은 디펜던시를 직접 적어놓은 후 스프링 버전을 올리게 되면 문제가 발생할 가능성이 높다. (서드파티 라이브러리들이 어떤 버전과 어떤버전이 호환되는지 알 수 없다.)

<br />

pom에서 지원하지 않는 버전들은 버전을 명시해야한다. 
 
혹은 특별히 원하는 버전이 있는경우에는 명시해도 된다. (오버라이딩 된다.)

### dependency를 관리하는 방법

1. parent를 사용하여 관리 (parent를 만들고 parent에 spring-boot-starter-parent 사용)
2. parent를 사용하지 않고 dependencyManager element를 사용하여 설정
    - 단점 : parent에서는 의존성만 가져오는 것이 아니라 java version, encording 방법, resource, plugin 등 여러가지 다른 설정도 포함하기 때문에 바꿀 경우 많은 설정을 따로 진행해야 한다. 
  
> parent를 사용하여 관리하는 것이 편리하다.

<br />

## pom.xml

[공식 다운로드 페이지](https://www.anaconda.com/download/)