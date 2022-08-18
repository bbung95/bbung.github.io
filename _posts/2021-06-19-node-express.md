---
title: "express"
excerpt: "express"

categories:
- node.js
tags:
- [express, javascript, Node.js]

permalink: /node-js/express/

toc: true
toc_sticky: true

date: 2021-06-30
last_modified_at: 2021-06-30
---
# express
---
### express란?
---
express는 node server를 더욱더 간편하게 설정하고 실행할 수 있는 npm 중 하나이다.

node를 사용하게되면 대부분 express를 이용하여 서버를 구성할 것이다.

### express 설치
---
express의 설치는 npm을 관리하는 package.JSON을 따로 만들어 해당 명령어를 cmd에 입력하여 설치가 가능하다.

    npm install express


### express 서버
---
**일반 node server**  

```javascript
var app = require('express');
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

**express server**  

```javascript
const express = require('express')
const app = express()
 
app.get('/', (req, res) => res.send('Hello World!'))
 
app.listen(3000, () => console.log('Example app listening on port 3000!'))
```

위와 같이 express사용시 단 몇줄의 코드만으로 서버를 구성하고 실행이 가능하다.