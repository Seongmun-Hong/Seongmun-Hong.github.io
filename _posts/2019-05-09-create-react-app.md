---
title: create-react-app
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

<!-- # create-react-app -->
  
## create-react-app 이란 ?  
  
create-react-app은 페이스북에서 만든 react 웹 개발용 boilerplate이다. 
  
create-react-app이 나오기 전 까지는  
1. 직접 모든 환경을 설정  
2. 남이 만든 boilerplate를 사용  
  
해야했다.
  
1번의 경우 React는 ES6 버전의 Javascript로 작성하는 것이 일반화 되어있기 때문에 **webpack**이라는 모듈번들러로 컴파일 및 빌드 하는 것이 필수라 이 환경을 세팅해줘야한다. 
  
2번의 경우 개인이 만든 bolierplate이기 때문에 지속적 업데이트가 되지 않아 react 버전이 상승하면 익숙했던 bolierplate는 쓰지 못하게 되고, 새로운 bolierplate를 찾아야 한다.  
  
create-react-app은 페이스북에서 만들고 지속적으로 업데이트 되는 공식적인 boilerplate이기 때문에 위와 같은 걱정 없이 개발을 진행할 수 있다.  
  
## create-react-app 설치  
  
npm 최신버전이 설치되어있어야 한다. npx는 npm 패키지를 로컬에 글로벌로 설치하지 않고 바로 일회성으로 실행할 수 있게 해주는 도구이다. npm 5.2.0 버전 이후부터 기본으로 제공된다. 아래의 명령어를 통해 프로젝트를 생성한다.
  
```shell
npx create-react-app react-example
```  
npx가 실행이 안되는 구버전이라면 아래와 같이 해야한다.
  
```shell
npm install -g create-react-app
create-react-app react-example
```  
  
아래의 명령어를 통하여 프로젝트를 로컬에서 실행할 수 있다.  
  
```shell    
npm start  
```
  
  
## create-react-app 구조  
  
기본 구조는 다음과 같다.
  
```
react-example/
  README.md
  node_modules/
  package.json
  public/
    index.html
    favicon.ico
  src/
    App.css
    App.js
    App.test.js
    index.css
    index.js
    logo.svg
```
  
src 폴더에 우리가 직접 작성할 소스코드 파일이 들어가고, public 폴더에 static 자원이 위치한다. public/index.html과 src/index.js는 엔트리 포인트가 되는 소스로, 파일이름이 변경되면 create-react-app은 작동하지 않으므로 주의한다.  
  
package.json파일을 얼여보면 굉장히 단순하다. dependency가 3개 밖에 없고, devDependency도 없다. 하지만 node_modules/폴더를 보면 알 수 있듯이, 내부적으로는 무수히 많은 dependency가 존재한다. 이는 create-react-app의 특징인데, 모든 설정들이 캡슐화 되어있다. 사용자로 하여금 설정들을 건들지 못하도록 실제 package.json을 숨겨두고, webpack설정과 관련된 부분들도 모두 숨겨두었다. 한마디로 개발자들이 소스코드에만 집중할 수 있도록 배려해 놓았다.
  
  
## 소스코드 분석
  
엔트리 포인트인 src/index.js 파일은 다음과 같다.
  
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';

ReactDOM.render(<App />, document.getElementById('root'));
registerServiceWorker();
```
  
#### ES6 문법 정리
[링크](https://itstory.tk/entry/JavaScript-ES6-%EB%AC%B8%EB%B2%95-%EC%A0%95%EB%A6%AC)
  
```javascript  
import React from 'react';
```  
  
jsx문법을 사용하기 위해서는 react모듈을 import 해야한다. 모든 react 컴포넌트에 필수적인 코드이다.  
  
```javascript
import ReactDOM from 'react-dom';
```  
  
react-dom모듈은 react 앱을 최초 렌더링 하기위해 엔트리 포인트에서 쓰인다. 브라우저뿐만 아니라 서버사이드용 랜더링 메소드를 지원한다.  
  
```javascript
import './index.css';
```  
  
css파일을 import 구문으로 가져오고 있다. 이는 webpack의 css-loader를 활용한 것인데, create-react-app에서 기본적으로 세팅이 되어있다.  
  
```javascript
import App from './App';
```  
  
App이라는 React 컴포넌트를 가져오는 코드이다. 
  
```javascript
import registerServiceWorker from './registerServiceWorker';
```
service worker란 네트웍이 느리거나 오프라인인 상태에서도 온라인 인것 처럼 리소스들을 캐싱해서 보여주는 모듈을 뜻한다. create-react-app에서는 기본으로 구현하여 소스코드에 포함되어있다.  
  
```html
ReactDOM.render(<App />, document.getElementById('root'));
registerServiceWorker();
```  
  
root라는 id를 가진 태그를 찾아서 그 안에 App컴포넌트를 렌더링시킨다. 이곳이 우리가 만드는 react 웹의 엔트리 포인트인데, 껍데기가 되는 html 파일은 public/index.html파일이 된다. 이 파일을 열어보면 root라는 id를 가진 div태그가 존재함을 확인할 수 있다.  
  
  
  
### Reference  
- https://eunvanz.github.io/react/
