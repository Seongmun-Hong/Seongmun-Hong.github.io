---
title: Lamda
layout: post
description: "About Lamda and Optional"
headline: null
modified: '2019-07-02'
category: Java
tags: [Java, Lamda, Optional]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-07-02 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Lamda

<br />

- 람다 표현식(Lamda Expression)은 논리학자인 Alonzo Church가 1930년대에 제안한 람다 대수에서 유래했다.
- 람다 대수는 함수 정의, 함수 적용, 귀납적 함수를 추상화한 형식 체계이다.

> Lamda의 등장 배경 및 함수형 프로그래밍의 여러 시도들은 아래의 링크에서 좀더 자세히 확인할 수 있다.
> https://d2.naver.com/helloworld/4911107#fn:3

<br />

## 함수형 프로그래밍  
  
함수형 프로그래밍을 먼저 살펴보는 이유는 람다 대수에 근간을 두는 함수형 프로그래밍은 패러다임이며 람다 표현식은 이를 나타낸다고 볼 수 있기 때문이다.
  
함수형 프로그래밍은 함수의 입력만을 의존하여 출력을 만드는 구조로 외부에 상태를 변경하는 것을 지양하는 패러다임으로 부작용(Side-effect) 발생을 최소화 하는 방법론이다.  
  
함수형 프로그래밍에서는 다음의 조건을 만족시켜야 한다.

1. 순수한 함수 (Pure Function)
    - 함수의 실행이 외부의 상태를 변경시키지 않는 함수를 의미한다.
    -  순수한 함수는 멀티 쓰레드 환경에서도 안전하고, 병렬처리 및 계산이 가능하다. 오직 입력에 의해서만 출력이 정해지고, 환경이나 상태에 영향을 받아서는 안된다

2. 익명 함수 (Annonymous Function)
    - 이름이 없는 함수를 정의할 수 있어야 한다.
    - 익명 함수는 대부분의 프로그래밍 언어에서 ‘람다식’으로 표현하고 있으며, 이론적인 근거는 람다 대수에 있다.

3. 고계 함수 (Higher-order Function)
    - 함수를 다루는 상위의 함수로 함수형 언어에서는 함수도 하나의 값으로 취급한다.
    - 함수의 인자로 함수를 전달할 수 있는 특성이 있다. 
        - 이러한 함수를 일급 함수로 간주한다.

> 일급 함수 : 프로그래밍 언어에서 함수를 값으로 다룰 수 있는 것 (함수 스스로 객체취급)

### JAVA에서의 함수형프로그래밍의 지원

자바에는 함수의 개념이 없다.
> 자바의 메소드는 일급 함수가 아니므로 다른 메소드로 전달할 수 없다. 자바에는 모든 것이 객체다. 메소드는 객체의 행위를 정의하고 객체의 상태를 변경한다.
  
Java8 에서 함수형 인터페이스(**단 하나의 메소드만이 선언된 인터페이스**)라는 개념을 도입하게 되었고, 함수형 인터페이스의 경우, 람다식으로 표현이 가능할 수 있게 제공하였다.  
  
Java8에서 함수형 인터페이스라는 개념과 람다식 표현을 통해 입력에 의해서만 출력이 결정되도록 ‘순수한 함수’를 표현할 수 있게 되었고, 람다식으로 표현함으로써 ‘익명 함수’를 정의할 수 있게 되었고, 함수형 인터페이스의 메소드에서 또다른 함수형 인터페이스를 인자로 받을 수 있도록 하여 ‘고계 함수’를 정의할 수 있게 되었다. 즉, 함수형 프로그래밍 언어의 조건을 만족시킬 수 있게 되었다.
  
### 예제
  
TwoNums, Something 클래스를 생성한다.

#### Someting.java
```java
@FunctionalInterface
public interface Someting {
    int something(int a,int b);
}
``` 

<br />

#### TwoNums.java
```java
public class TwoNums {

    private int a;
    private int b;

    TwoNums(int a, int b) {
        this.a = a;
        this.b = b;
    }

    public void calc(Someting s) {
        int number = s.something(a, b);
        System.out.println("result = " + number);
    }
}
``` 

<br />

그 후 Main 메서드에서의 활용은 다음과 같이 할 수 있다.

#### Main.java
```java
public class Main {
    public static void main(String[] args) {
        TwoNums twoNums = new TwoNums(3, 4);
        twoNums.calc((a, b) -> { return a + b; }); 
        twoNums.calc((a, b) -> { return a - b; });
    }
}
``` 

<br />

위에서와 같이 기본적으로

```java
(int a, int b) -> {return a + b} // 매개변수 -> 함수 로직 (+@ 리턴)
```

<br />

형태이다.  
  
하지만 매개변수의 타입은 명시하지 않아도 된다. (타입추론)

### Reference

- https://www.inflearn.com/course/spring-framework_core