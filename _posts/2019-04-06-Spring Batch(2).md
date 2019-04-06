---
title: Spring Batch - (2)
layout: post
description: null
headline: null
modified: '2019-04-06'
category: Spring Batch
tags:
- Spring, Spring Batch
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
---

### 5. JOB, JOB_INSTANCE, JOB_EXCUTION

 - ERD  

![ERD](/images/post/batch_table_erd.png) 


#### 5-1. BATCH_JOB_INSTANCE

 * JOB_INSTANCE_ID  
   * BATCH_JOB_INSTANCE 테이블의 PK
 * JOB_NAME
   * 수행한 Batch Job Name

BATCH_JOB_INSTANCE 테이블은 Job Parameter에 따라 생성되는 테이블
 > Job Parameter = Spring Batch가 실행될때 외부에서 받을 수 있는 파라미터 = Program Arguments
 
같은 Batch Job 이라도 Job Parameter가 **다르면** **Batch_JOB_INSTANCE에는 기록**되며,  
Job Parameter가 **같다면 기록되지 않는다.**  


#### 5-2. BATCH_JOB_EXECUTION

JOB_EXECUTION와 JOB_INSTANCE는 **부모-자식 관계**이다.  
**JOB_EXECUTION**은 자신의 부모 **JOB_INSTACNE**가 **성공/실패했던 모든 내역**을 갖고 있다.  

동일한 Job Parameter로 **2번 실행하여도** 같은 파라미터로 실행되었다는 **에러가 발생하지 않는다!**

#### ! Spring Batch는 동일한 Job Parameter로 성공한 기록이 있을때만 재수행이 안된다.  


#### 5-3. 정리


|      -     |                          내용                          |
|:------------:|:------------------------------------------------------------:|
|      JOB     |                          하나의 Spring Batch JOB              |
| JOB_INSTANCE |           JOB Parameter 단위로 실행된 JOB INSTANCE           |
| JOB_EXCUTION | JOB Parameter 단위로 실행된 JOB의 실행 내역들(1번 혹은 여러번) |


추가로 **BATCH_JOB_EXECUTION_PARAM**은 BATCH_JOB_EXECUTION 테이블이 생성될 당시에 입력 받은 **Job Parameter를 담고 있다.**


