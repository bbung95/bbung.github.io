---
layout: post
title: "Router"
date: 2021-09-22 19:30:00 +0900
categories: React
---
# Router
---

React Project의 Page Navigation을 도와주는 react-router-dom 이라는 모듈이 있다.

    npm install react-router-dom

**react-router-dom**  

```javascript
import { HashRouter, Route } from "react-router-dom";
//...
    <HashRouter>
      <Route path="/" component={Home}></Route>
      <Route path="/about" component={About}></Route>
    </HashRouter>
//...
```

라우터는 path , component 2개의 Props을 가진다.  

- path : uri와 같으며 도메인 주소에서 네비게이션하는 주소를 입력한다.
- component : component는 navaigation 시 불러오는 component를 입력한다.  


"/about" 경로로 접근했을 경우도 "/"을 랜더링 시킨다.  
이유는 리엑트 시스템 자체가 자기 자신을 찾기 때문이다(?)

**해결방법**  

 - 속성값으로 exact={true} 사용시 자기 자신의 패스일때만 랜더링 시킨다.


**Navigation**

\<a href="/"> or \<a href="/about"> 은 작동이 안될것이다.  

리엑트에서는 \<Link>를 사용해줘야하며 html이 아니기 떄문에 href가 아닌 to를 사용해야 한다.  

Link는 라우터 안에서만 작동한다. 

```javascript
import { Link } from "react-router-dom";
//...
<div>
  <Link to="/">
  <Link to="/about">
</div>
//...
```
