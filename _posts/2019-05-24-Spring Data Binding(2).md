---
title: Spring - Data Binding(2) - Converter와 Formatter
layout: post
description: "About Spring Framework"
headline: null
modified: '2019-05-24'
category: Spring
tags: [Spring]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-05-24 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Data Binding 추상화 - Converter와 Formatter

<br />

- Spring 3.0에 도입되었으며 이상 버전에서 사용 가능하다.
- <a href="https://seongmun-hong.github.io/spring/Spring-Data-Binding(1)">Data Binding(1) - PropertyEditor</a> 에서 배운 PropertyEditor가 가지고 있는 단점을 보완하였다.

<br />

## Converter  
  
- Source 타입을 Target 타입으로 변환할 수 있는 매우 일반적인 변환기이다.
- 상태 정보가 없다 = Stateless하다 = Thread Safe 하다.
- ConverterRegistry에 등록해서 사용한다.

<br />

## Converter 사용해 보기  

<br />

### 1. Event, Controller 생성  
  
#### Event.java
```java
public class Event {

    Integer id;
    String title;

}
```  

Construct, Getter, Setter, toString 생략

<br />

#### EventController.java
  
```java
@RestController
public class EventController {

    @GetMapping("/event/{event}")
    public String getEvent(@PathVariable Event event) {
        System.out.println(event);
        return event.getId().toString();
    }
}
```  

<br />

### 2. Converter 생성  
  
#### EventConverter.java
  
```java
public class EventConverter {

    public static class StringToEventConverter implements Converter<String, Event> {

        @Override
        public Event convert(String s) {
            return new Event(Integer.parseInt(s));
        }
    }

    public static class EventToStringConverter implements Converter<Event, String> {

        @Override
        public String convert(Event event) {
            return event.getId().toString();
        }
    }
}
```  

<br />

### 3. Converter 등록

#### WebMvcConfig.java
  
```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addConverter(new EventConverter.StringToEventConverter());
    }

}
```  

<br />

### Integer, Long, Double... 등등 기본적인 타입들에 대해서는 기본적으로 등록되어 있는 Converter, Formatter가 변환을 해준다. 모든 Type을 위와 같이 Converter를 등록할 필요는 없다.

<br />

## Formatter  
  
- 조금 더 Web쪽에 특화되어 만들어진 Interface이다.
- PropertyEditor의 대체제이다.
- Object와 String 간의 변환을 담당한다.
- 문자열을 Locale에 따라 다국화하는 기능도 제공한다. (Optional)
- FormatterRegistry에 등록해서 사용한다.

<br />
  
## Formatter 사용해 보기  

- Converter 예제의 Event, Controller 는 작성되어 있다고 가정한다.  

<br />
  
### 1. Formatter 생성  
  
#### EventFormatter.java

```java
//@Component  Thread Safe하기 때문에 Bean으로 만들 수 있다.
public class EventFormatter implements Formatter<Event> {

    // @Autowired
    // MessageSource messageSource;

    @Override
    public Event parse(String s, Locale locale) throws ParseException {
        return new Event(Integer.parseInt(s));
    }

    @Override
    public String print(Event event, Locale locale) {
        // messageSource.getMessage("title", locale);
        // 위의 주석과 같이 Bean으로 만들면 MessageSource를 가져와서
        // locale에 따른 처리가 가능하다.
        return event.getId().toString();
    }

}
```

<br />

### 2. Formatter 등록
  
#### WebMvcConfig.java

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addFormatter(new EventFormatter());
    }

}
```

<br />  
  
## ConversionService  
  
- 실제로 데이터를 변환하는 작업은 Converter와 Formatter를 활용할 수 있도록 도와주는 ConversionService가 담당한다.  
- 위에서 만들었던 Converter와 Formatter는 실제로 ConversionService에 등록되지고 ConversionService를 통해 데이터를 변환한 것이다.  
- 실제 변환 작업은 이 interface를 통해서 Thread Safe하게 사용할 수 있다.
- **스프링 MVC**, 빈 (value) 설정, SpEL에서 사용한다.
- ConversionService 구현체 중 DefaultFormattingConversionService 클래스를 주로 사용한다.
    - FormatterRegistry
    - ConversionService
    - 여러 기본 Converter과 Formatter를 등록 해 줌
  
<br />

### DefaultFormattingConversionService  
  
{:.post-img}
![DefaultFormattingConversionService](/images/post/default_formatting_conversion_service.png) 

<br />  

### Spring Boot  

- 웹 애플리케이션인 경우에 DefaultFormattingConversionSerivce를 상속하여 만든 WebConversionService를 빈으로 등록해 준다.
    - DefaultFormattingConversionSerivce보다 많은 기능을 제공해 준다.
- Formatter와 Converter 빈을 찾아 자동으로 등록해 준다. (@Component Annotation)
    - addFormatters메서드를 사용하여 일일이 등록해 줄 필요가 없다.

<br />

### MockMvcTest  

<br />
  
MockMvcTest 사용시 기본적으로 Web 관련 Bean들만 생성하여 테스트를 진행한다.
  
```java
@WebMvcTest({EventFormatter.class, EventController.class}) 
```  

<br />

따라서 위와 같이 특정 Bean을 등록하여 테스트를 진행할 수 있다.
    - 그냥 Class를 찾아서 등록하는 것은 아니다. ComponentScan이 가능한 Bean이여야 한다. (@Component Annotation이 붙어 있어야 한다)  
      
<br />

#### 보통 Web과 관련되어 데이터 바인딩을 진행할 경우 Formatter를 사용하는 것을 추천한다.
  
- 보통 String to Object이기 때문.  
  
<br />

## Spring boot에서 등록되어 있는 Converter, Formatter들을 확인하는 방법  
  
### ConversionService를 출력해 보면 된다.  
  
#### Runner.java  
  
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    private ConversionService conversionService;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(conversionService);
    }

}
```  

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core