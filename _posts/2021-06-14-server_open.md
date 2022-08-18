---
title: "Node.js - Server"
excerpt: "Node.js - Server"

categories:
- node.js
tags:
- [javascript, Node.js]

permalink: /node-js/server/

toc: true
toc_sticky: true

date: 2021-06-14
last_modified_at: 2021-06-14
---
# 서버의 구현
---

기본적으로 아래와 같이 설정하여 포트번호 3000번으로 간단하게 서버에 접근할 수 있다.

```javascript
var http = require('http'); // require는 요청하다 (모듈)
var fs = require('fs'); // http , fs(file system)
var app = http.createServer(function(request,response){
    var url = request.url;
    if(request.url == '/'){
      url = '/index.html';
    }
    if(request.url == '/favicon.ico'){
      return response.writeHead(404); // 404 오류 시
    }
    response.writeHead(200); // 200번은 정상적으로 연결(?)
    response.end(fs.readFileSync(__dirname + url));
 
});
// 포트번호 3000은 Node.js 웹서버 고정
app.listen(3000);
```
위와 같이 node 실행 후 위의 자바스크립트 코드를 이용하면 콘솔에서 값이 출력되는 것을 볼 수 있다.