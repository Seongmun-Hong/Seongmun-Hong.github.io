---
title: React Binding
layout: post
description: ""
headline: null
modified: '2019-05-09'
category: React
tags: [Front, React]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-05-09 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# React Binding(이벤트/메서드 연결)
  
React에서의 binding을 이해하기 위해서는 Javascript에서의 this의 이해가 중요하다.
  
## Javascript에서 Binding  
  
Javascript에서는 객체 안에서의 this는 해당 메서드가 포함된 Object를 가르킨다.  
  
```javascript
var obj = {  
    prop: 'Hello',
    sayHello: function() {
        console.log( this.prop );
    }
};
 
obj.sayHello(); // "Hello"
```  
  
위의 결과는 Console에 Hello를 출력하게 된다.  
  
하지만 아래 코드의 결과는 undefined 이다.  
  
```javascript
var obj = {  
    prop: 'Hello',
    sayHello: function() {
        console.log( this.prop );
    }
};
 
var reference = obj.sayHello;
reference(); // undefined
```  
  
이 이유는 변수 reference에 담길 때 obj와의 관계가 상실되기 때문이다.  
  
이와 같은 상황에서 필요한 것이 **바인딩(Binding)**이다.  
  
```javascript
var obj = {  
    prop: 'Hello',
    sayHello: function() {
        console.log( this.prop );
    }
};
 
var reference = obj.sayHello.bind(obj);
reference(); // "Hello"
```  
  
obj.sayHello()를 전달할 때 obj까지 바인딩시켜서 보내면 된다.  
  
리액트에서도 마찬가지로 자바스크립트의 this가 사용되기때문에 바인딩이 필요하다.  
  
## React에서 Binding  
  
React에서 컴포넌트에 이벤트메서드를 연결하는 방법이 바인딩(Binding)이다.  
  
React에서 바인딩을 하는 방법은 여러가지가 있다.  
  
### 생성자에서 바인딩
  
```JSX
class App extends React.Component {
    constructor() {
        super();
        this.state = {
              hidden: false,
        };
        this.update = this.update.bind(this);
    }
    update() {
        this.setState({
            hidden: true
        });
    }
 
    render() {
        return <div
            onClick={ this.update }
        />;
    }
 
}
```
  
constructor() 에서 this.update = this.update.bind(this); 문장을 집어넣어서 바인딩시키면 render()에서 onClick={this.update}를 할 때 this가 App컴포넌트의 this라는 것을 알게 되는 것이다.  
  
  
### autobind-decorator를 사용한 바인딩  
  
```JSX
import autobind from 'autobind-decorator'
 
class App extends React.Component {  
    @autobind
    update() {
        ...
    }
    //...생략...
}
```
  
  
### arrow function를 사용한 바인딩(auto-binding)  
  
```JSX
import React, { Component, PropTypes } from 'react';
 
export default class Basic extends Component {
    state = {
           hidden: false,
      };
 
      onClickButton = () => {
        this.setState(() => ({ hidden: true }));
      }
    
    render() {
        return (
              <div>
                <button onClick={this.onClickButton}>숨기기</button>
              </div>
        );
    }
}
```
  
  
## Reference  
- https://jeong-pro.tistory.com/79?category=791879