---
title: "Cookie & Session"
excerpt: "Cookie & Session"

categories:
- Java
tags:
- [Cookie, Session, Java]

permalink: /java/cookie-session/

toc: true
toc_sticky: true

date: 2021-04-02
last_modified_at: 2021-04-02
---

# 상태유지 (Cookie & Session)

---
Web의 기술은 클라이언트를 통해 들어온 정보를 접속후 프로세서 종료시 접속을 종료하는 특성을 가지고있다.    
이러한 특성은 메모리를 차지하지 않는다는 장점을 가지고 있지만 Web기술의 발달함으로써 Web의 이러한 특성이 단점으로 작용하는데  
상태유지를 위한 기술로 Cookie & Session이가 등장했다.

Cookie
* 클라이언트 컴퓨터에 저장하는 기술
* 저장된 정보를 다른 사람 또한 쉽게 접근가능한 단점을 가진다.
* 유효시간이 지나면 사라진다.
* 서버 정보에 접근시 식별성을 가지지 않아 원하는 정보를 가지기 어려움

Session
* 서버에 저장하는 기술
* 서버가 종료되거나 유효시이 지나면 사라진다.
* 특별한 식별ID를 가짐으로써 서버에서 원하는 정보만을 가질수 있다.

## Cookie

---
동작의 이해
img

1. 클라이언트가 정보를 입력하고 request시 입력 정보를 가지는 쿠키를 생성하는데 Key와 Value인 Map구조를 가진다.
2. respone시 만들어진 쿠키와 함께 클라이언트에게 보내지며 쿠키는 클라이언트 컴퓨터에 저장된다.  
3. 다시 request시 만들어진 쿠키를 포함하여 같이 서버로 보내진다.
4. 만약 동일한 쿠키가 존재한다면 생성하지 않고 그 쿠키에 대한 정보를 볼 수 있게된다.

자바에서 Cookie 생성
```java
//Cookie 생성(name=value) :: 한글 인코딩 후 저장
		Cookie cookie = new Cookie("name",URLEncoder.encode("홍길동"));

        cookie.setMaxAge(60*60);	//cookie 유효기간(초)  지나면 쿠키 삭제
		//cookie.setMaxAge(-1);		//cookie memory 저장        :: ??	 ==> API확인 
		//cookie.setMaxAge(0);	 	//cookie 0초동안 유효		:: ??	 ==> API확인 
		res.addCookie(cookie);		//Client 로 response 인스턴스를 사용 cookie 전송
				
        out.println("<html><body>");
		out.println("Cookie 저장 완료");
		out.println("</body></html>");
```
Cookie의 정보
```java
// Client 로 부터 전송된 Cookie 처리
		Cookie[] cookies = req.getCookies();
		// Cookie 의 name = value 처리 변수 (Map구조)
		String userName =null;

		//Cookie 의 존재유무 및 name=value 처리
		if(cookies != null) {
            out.println("Client에서 전송된 Cookie 있습니다.<br/>");
			//Array 로 return  :: Array 갯수만큼 처리
			for(int i=0;i<cookies.length;i++){
				//name = value 형식의 저장값 중 name 추출
				String name = cookies[i].getName();
				String value = URLDecoder.decode(cookies[i].getValue());
				System.out.println("client로 부터 전송된 cookie : "+name+"="+value);
				
				if(name.equals("name")){  // Key값중 "name"시 value로 userName 설정
					userName = value;
				}
			}
		}else{
            out.println("Client에서 전송된 cookie가 없습니다.<br/>");
        }

		out.println("<html><body>");
		//userName이 null 의미 :: cookie 에 id 가 저장 되지 않았음 ==> 처음방문
		if(userName == null){
			out.println("처음입니다.");
		}else{
			out.println(userName+"님 환영");
		}
		out.println("</body></html>");
	}
```

## Session
동작의 이해

Cookie를 이용하여 서버에 저장하는 방식이 Session이다.
img

1. 쿠키가 서버에서 만들어질 때 특별한 식별ID가 생성되어 쿠키에 포함된다.
2. 쿠키와 같은 방식으로 respone시 쿠키와 함께 클라이언트로 보내진다.
3. request시 쿠키와 함께 서버로 보내지는데 쿠키에는 특별한 식별ID가 있기에  
서버에 저장되어있는 정보를 볼 수 있다.

img
위와 같은 로직을 가지고 있다.

Session의 생성
```java
public void doGet(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
		
		HttpSession session = req.getSession(true);
		// true시 세션을 생성한다.
		if(session.isNew()) {   // 세션이 새로 만들어졌다면 true
			session.setAttribute("name", new String("홍길동")); // Key , Value 로 셋팅
		}
		req.setCharacterEncoding("EUC_KR");
		res.setContentType("text/html;charset=EUC_KR");
		PrintWriter out = res.getWriter();

		out.println("<html><head></head><body>");
		out.println("<center><h2>SessionUseCookieOne</h2></center>");
		
		System.out.println("\n UNIQUE한 JSESSIONID는 : "+session.getId());
		// 식별ID를 불러온다
		if (session.isNew()) {
			out.println("세션이 새로 생성됨<br>");
		} else {
			out.println("\n UNIQUE한 JSESSIONID는 : "+session.getId()+"사용중");
		}
		out.println("<hr>");
		out.println("<a href='/edu/SessionUseCookieTwo'>링크</a>");
		out.println("</body></html>");
	}
```
Session의 정보
```java
public void doGet(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {

		req.setCharacterEncoding("EUC_KR");
		res.setContentType("text/html;charset=EUC_KR");
		PrintWriter out = res.getWriter();

		Cookie[] cookies = req.getCookies();
		if(cookies != null) {
			for(int i = 0; i<cookies.length; i++) {
				System.out.print("\nCookie에 저장된 정보 : ");
				System.out.print(cookies[i].getName()+" : "+cookies[i].getValue());
				System.out.print("정보 확인");
				System.out.print("\n");
			}
		}
		
		HttpSession session = req.getSession(false);
		
		out.println("<html><head></head><body>");
		out.println("<center><h2>SessionUseCookieTwo</h2></center>");

		if (session != null) {
			out.println("<hr> JSESSIONID = "+session.getId()+"<hr>");
			String name = (String)session.getAttribute("name");
			out.println("이름 : "+name);
		} else {
			out.println("처음이십니다.");
		}
		out.println("</body></html>");
	}
```



