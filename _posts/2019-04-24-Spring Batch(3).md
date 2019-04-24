---
title: Spring Batch - (3) Spring Batch Job Flow
layout: post
description: null
headline: null
modified: '2019-04-24'
category: Spring-Batch
tags:
- Spring, Spring-Batch
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-04-24 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

### Spring Batch Job Flow

Spring Batch의 Job을 구성 - Step이 존재  
Step은 실제 Batch 작업을 수행하는 역할  
  
실제로 Batch 비지니스 로직을 처리하는 기능은 Step에 구현  
이처럼 Step에서는 Batch로 실제 처리하고자 하는 기능과 설정을 모두 포함  
  
따라서 Batch는 **Job 내부의 Step들 간에 순서 혹은 처리 흐름을 제어할 필요성 존재**  

#### 1. Next

next()는 순차적으로 Step들 연결시킬때 사용  

**Example**  

```
@Bean
public Job job() {
    return jobBuilderFactory.get("job") // job 이름 설정
            .start(step1())
            .next(step2())
            .next(step3())
            .build();
}
```

step1 -> step2 -> stpe3 순으로 하나씩 실행

#### 2. 특정 Batch Job만 실행

여러개의 Batch Job이 존재할 경우 특정한 Batch Job 만 실행하기 위하여  

```
spring.batch.job.names: ${job.name:NONE}
```

코드를 application.properties 에 추가  

Spring Batch가 실행될때  
Program arguments로 job.name 값이 넘어오면 해당 값과 일치하는 Job만 실행하겠다는 뜻
 
> ${job.name:NONE}의 의미  
> 
> job.name이 **있으면 job.name** 값을 할당, **없으면 NONE** 할당  
> 따라서 spring.batch.job.names에 **NONE이 할당되면 어떠한 배치도 실행하지 않음**  
  
   
프로그램 실행 시 Program arguments에  

```
--job.name=JobName
```

과 같이 인자를 넘겨주면 됨.  

#### 3. 조건별 흐름 제어