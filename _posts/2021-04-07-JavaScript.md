---
layout: post
title: "JavaScript"
date: 2021-04-07 0:0:00 +0900
categories: JavaScript
---
# JavaScript

프로그래밍의 개발을 하는데는 많은 언어가 존재한다.
각 분야별로 프론트엔드, 백엔드가 있고 java, C, C++, Phyon, JavaScript 등.... 언어가 있다.  

하지만 프론트엔드 영역 개발시 웹 브라우져에서는 JavaScript는 빼놓고 생각 할 수 없는 언어이다.  

최근 들어서 백엔드 뿐만 아니라 웹이아닌 탈웹 영역에서도 사용 할 수 있다.  

**node.js(웹 서버 자바스크립트)**  
- 자바스크립트를 서버쪽에서도 사용가능 ( 프론트와 백엔드를 자바스크립트로 통합가능) / Writer ('~') - node.js

**탈웹(Google Apps Script)**
- 앱에서도 자바스크립트를 사용가능(영역확장) / msgBox ('~') - google spred sheet


### JS과 html의 연결
---

js는 특별한 파일 설치없이 사용이 가능하다.  
html의 연결시에는 두가지 방법이 있는데 하나는 html에 작성하는 방법과,  
js파일 생성후 html과 연결하는 방법이 있다.  

**html의 작성**
```javascript
<!DOCTYPE html>
<html lang="en" dir="ltr">
    <head>
        <meta charset="utf-8">
        <title>practice</title>
    </head>
    <body>
        <button onclick="btn_alert('안녕하세요!')"> 인사하세요! </button>
        <script>
        // 경고창
            function btn_alert(str) {
                alert(str);         }
        </script>
    </body>
</html>
```
\<body>\</body>태그 안에 \<script>\</script>태그를 사용해 js코드를 작성하여 사용 할 수 있다.  

하지만 위 방법을 사용시 코드의 유지보수가 어려워진다.  
후에 짧은 코드가 아니라 1000개 10000개의 코드가 작성되 있다면 상상도 하기 싫다.

**JS파일 생성**
```javascript
//.js file
function alert_button(str) {
    alert(str);
}

//html
<!DOCTYPE html>
<html lang="en" dir="ltr">
    <head>
        <meta charset="utf-8">
        <title>practice</title>
    </head>
    <body>

        <button onclick="btn_alert('안녕하세요!')"> 인사하세요! </button>

        <script type="text/javascript" src="practice.js"></script> 

    </body>
</html>
```
.js파일을 생성 후 필요한 코드를 작성한다.  
후에 \<body>\</body>태그 안에 \<script type="text/javascript" src="practice.js"></script>태그를 사용해 .js파일을 불러와 연결시킬 수 있다.

자바스크립트는 html 파일에 <body> 태그 안의 맨 아래에 적어준다. (컴파일 처리 순서때문에)


### JavaScript 실행 기본
---  
- alert(); - 경고창 출력
- confirm() - 확인창을 출력 (ok -> true / cancel -> false)
- prompt(); - Client로 부터 입력값을 받음
- console.log(); - 콘솔 로그에 출력

<!-- 나중에 다시 배울 것 -->
<!-- location : 윈도우의 주소내용을 가지고있는 객체값(오브젝트)
​
location.href = location ​ / url값으로 이동
location.reload(); / 윈도우를 새로고침

​
navigator : 브라우저의 정보를 제공하는 객체값 -->

### 데이터타입
---

```javascript
// Number type
var number = 1;   
var number = 2; 
let number2 = 1; // 오류 발생 var와 달리 let은 변수명을 중복하여 
let number2 = 2; // 선언 할 수 없다.

// String(문자) type
let srt1 = 'Hello World';
let srt2 = "Hello World";

// boolean type
let boo1 = true;
let boo2 = false;

// null - 값이 없다
let foo = null;

// undefined - 값이 정해지지않았다
let bar;

// 객체 리터럴
{ name: 'Lee', gender: 'male' }

// 배열 리터럴
[ 1, 2, 3 ]

// 정규표현식 리터럴
/ab+c/

// 함수 리터럴
function() {}
```
원시 타입 (primitive data type)
- number
- string
- boolean
- null
- undefined
- symbol (New in ECMAScript 6)

객체 타입 (Object data type)
- object

js는 다른 java와 C 같은 언어와 달리 데이터 타입을 따로 지정해주지 않는다. 
변수에 할당된 값의 타입에 의해 동적으로 변수의 타입이 결정된다.  
이를 동적 타이핑이라 하며 자바스크립트가 다른 프로그래밍 언어와 구별되는 특징 중 하나이다.  

## 변수
---
js도 다른 언어와 같이 변수를 통해 값을 선언 할 수 있다.  


숫자
```javascript
var a = 1;
alert(a+1);  // 출력 값 2
 
var a = 2;
alert(a+1);  // 출력 값 3​

```
문자열
```javascript
var first = "coding";
alert(first+" everybody");​ // 출력 값 coding everybody

var a = 'coding', b = 'everybody';
alert(a); // 출력 값 coding 
alert(b);​ // 출력 값 everybody
```
**묵시적 형변환**
```javascript
var number = 1234;
var str = "567";
alert(number+str); // 출력 값 123456
                   // 숫자와 문자의 연산이지만 묵시적 형변환을 통해 문자로 출력
```

## 주석 / 줄 바꿈과 여백

주석을 자주 활용하여 효율적인 코드를 작성하자  

**주석**
```javascript
앞에 // 를 사용 (한 줄)
/*
여러줄 주석
여러줄 주석​
여러줄 주석​
 */
​
//스크립트를 설명하던가, 식의 결과를 주석으로 코드에 표현가능, 식의 기능 숨김
​//주석 사용을 꼭하자!!!
```
**줄바꿈**

( ; ) 세미콜론은 명령이 끝났다를 표현한다. 사용하지 않아도 명령이 적용(한줄에 한 코드)  
세미콜론을 사용시 한줄에 여러 코드 가능하다.

## 비교 ( 조건문이랑 적용시 활용성 UP)
---
​
​연산자  /  a(변수) = 3(값)  이다  

비교 연산자  
- ==  /  Equal operator has (동등 연산자)   -  사용 x  

​
동등한 연산자로 좌항과 우항을비교해서 서로 값이 같다면 true
다르다면 false가 된다.  
- === ​/ Strict equal operator (일치 연산자)   -  정확성이 더 높음 

값뿐만 아니라 데이터(number, string)에 종류 또한 같아야함.  

not 연산자
- ( != ) / ( !== ) ! 는 부정을 의미

​

  


