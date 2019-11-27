---
title: Spring Boot AutoConfigure 구현해보기
layout: post
description: ""
headline: null
modified: '2019-11-27'
category: SpringBoot
tags: [SpringBoot]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-11-27 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Spring Boot AutoConfigure 구현해보기

Spring에서 자동 설정 프로젝트의 형식은 다음과 같다.

- Xxx-Spring-Boot-Autoconfigure 모듈 : 자동 설정
- Xxx-Spring-Boot-Starter 모듈 : 필요한 의존성 정의

하나로 만들고 싶은 경우 Xxx-Spring-Boot-Starter 와 같은 형태로 만들 수 있다.

<br />

직접 자동 설정 프로젝트를 만들어 보고자 한다.

### Maven Project 생성

```text
GroupId : autocinfig.example
ArtifactId : xxx-spring-boot-starter
```

위와 같은 형태로 maven project를 생성한다.
GroupId는 중요하지 않으나 ArtifactId는 위와 같은 형식으로 생성해주어야 한다.

여기서는 example-spring-boot-starter로 생성하였다.

#### 1. 의존성 추가

생성된 프로젝트에서 pom.xml에 다음과 같은 의존성을 추가한다.

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-autoconfigure</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-autoconfigure-processor</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.0.3.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

<br />

#### 2. @Configuration 파일 작성

Configuration 파일에선 보통 기존에 만들어져있는 Bean을 생성하나 여기서는 생성할 Class하나를 임의로 만들어주자.

##### Student.java

```java
public class Student {

    String name;
    int studentNumber;

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", studentNumber=" + studentNumber +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getStudentNumber() {
        return studentNumber;
    }

    public void setStudentNumber(int studentNumber) {
        this.studentNumber = studentNumber;
    }
}
```

<br />

그 후 Configuration 파일을 생성해 준다.

##### StudentConfiguration.java

```java
@Configuration
public class StudentConfiguration {

    @Bean
    Student student() {
        Student student = new Student();
        student.setName("Seongmun");
        student.setStudentNumber(12345);
        return student;
    }
}
```

<br />

#### 3. src/main/resource/META-INF에 spring.factories 파일 만들기

@EnableAutoConfiguration Annotation은 
<a href="https://seongmun-hong.github.io/springboot/Spring-boot-EnableAutoConfiguration">지난 포스트</a> 에서 공부하였듯 **resources/META-INF/spring.factories** 파일에 있는 **org.springframework.boot.autoconfigure.EnableAutoConfiguration** 라는 Key값에 명시되어 있는 Configure Class들을 Bean으로 등록한다.

따라서 resources 폴더에 META-INF 폴더를 생성한 후 spring.factories 파일을 생성해준 후 Key값을 적어준다.

##### spring.factories

```java
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  autocinfig.example.StudentConfiguration
```

<br />

해당 설정 프로젝트를 다른 프로젝트에서 활용하기 위하여 메이븐 프로젝트를 install 한다.

```shell
mvn install
```

위와 같이 콘솔에서 입력해주어도 되며 혹은 IntelliJ를 사용하는 경우 우측 사이드메뉴의 Maven 탭에서 Lifecycle > install 을 클릭해주어도 동일하다.

<br />

### 구현된 설정 프로젝트 사용하기

구현된 설정 프로젝트를 사용할 Spring boot 프로젝트를 생성한다. 그 후 pom.xml에 만든 프로젝트를 dependency에 추가한다.

##### pom.xml

```xml
    ...
    <dependencies>
        ...
        <dependency>
            <groupId>springboot.study</groupId>
            <artifactId>example-spring-boot-starter</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>
```

<br />

#### EnableAutoConfiguration을 통하여 등록된 Bean 확인하기

등록되어있는 Bean을 확인하기 위하여 Runner를 생성한다.

##### StudentRunner.java

```java
@Configuration
public class StudentRunner implements ApplicationRunner {

    final
    Student student;

    public StudentRunner(Student student) {
        this.student = student;
    }

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(student.toString());
    }
}
```

위와 같이 Runner 에서 student를 주입받은 후 toString 메서드를 실행시키면 설정 파일에서 정의한 것과 Bean이 생성된 것을 확인할 수 있다.

{:.post-img}
![student-runner-log](/images/post/EnableAutoConfiguration/student-runner-log.png) 

<br />

#### @Bean을 활용하여 Student를 하나 더 만든다면?

Main Class 에서 다음과 같이 @Bean을 활용하여 Student Bean을 하나 더 만들게 된다면 어떻게 될까 ?

##### Application.java

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public Student student() {
        Student student = new Student();
        student.setName("Hong");
        student.setStudentNumber(54321);
        return student;
    }

}
```

다시 실행하더라도 결과는 Seongmun, 12345를 출력한다.

이러한 이유는 <a href="https://seongmun-hong.github.io/springboot/Spring-boot-EnableAutoConfiguration">지난 포스트</a>에서 찾을 수 있다. Spring Boot Application은 Bean을 생성할 때 우선 ComponentScan을 통하여 Bean을 생성한 후 EnableAutoConfiguration이 정의된 Class들의 Bean을 생성한다.

따라서 이 경우 Main에 정의한 Hong, 54321 이라는 Student Bean을 생성한 후 EnableAutoConfiguration 에 정의된 Student Bean을 덮어쓰게 된다. 따라서 최종적으로 Configuration에 정의된 Bean이 생성되어 진다.