---
title: Component의 Props와 State
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
  
# Component의 Props State

## props  
  
Properties(속성)의 Props이다. 리액트에서 하위 컴포넌트에게 변동하지 않는 데이터를 다룰 때 사용한다.  
  
create-react-app을 통해 만든 기본 프로젝트에서의 사용 예는 다음과 같다.  
  
```JSX
ReactDOM.render(<App name="홍성문" age={28} />, document.getElementById('root'));  
```  
  
기존 <App /> 으로 컴포넌트를 생성했다면 props를 적용하기 위하여 name="홍성문" age={28} 와 같은 방식으로 데이터를 전달 할 수 있다.  
    
이렇게 전달받은 데이터는 App 컴포넌트에서 this.props.name, this.props.age 와 같이 사용할 수 있다.  
  
## state  
  
state는 사용자에 의해 변경되는 값을 관리할 때 주로 사용된다.  
  
```JSX
class App extends Component {
  constructor(){
    super();
    this.state = {
      name : "홍성문",
      age : 28
    };
  }
  //...생략...
}
```  
  
위와 같이 주로 컴포넌트 클래스에서 생성자로 정의하며 state 상태를 변경시킬 때는 반드시 setState()를 사용해야 한다. 
  
name을 바꾸는 메서드는 아래와 같은 방식으로 선언한다.
  
```JSX
setName = (newName) => {
	this.setState({
		name: newName
	});
}
```  
  

## Reference  
- https://jeong-pro.tistory.com/77