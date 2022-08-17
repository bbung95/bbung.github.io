---
title: "Cookie가 생성이 안될 때"
excerpt: "Cookie가 생성이 안될 때"

categories:
- HTML
  tags:
- [Cookie, Spring, Java]

permalink: /java/cookie-error/

toc: true
toc_sticky: true

date: 2021-05-25
last_modified_at: 2021-05-25
---

# Cookie가 생성이 안될 때
---

미니프로젝트 리펙토랑을 진행하면서 어느 부분에서부터 갑자기 Cookie가 생성이 되지않아  
최근목록이 뜨지 않는 현상이 있었다.

```java
    String history = null;
	Cookie[] cookies = request.getCookies();
	
    if (cookies != null && cookies.length > 0) {
		for (int i = 0; i < cookies.length; i++) {
			Cookie cookie = cookies[i];
		    if (cookie.getName().equals("history")) {				
             history = cookies[i].getValue();
		    }
		}
	}

	Cookie cookie = new Cookie("history", history + "," + product.getProdNo() + "/" + product.getProdName());
	response.addCookie(cookie);
```
위와 같이 Controller에 작성하여 history 이름으로 쿠키를 생성을 했는데 확인해보니   
history는 생성이 되지않고 고유식별인 JSESSION 만 생성이 되었다.  

찾아보니 CookieGenerator라는 SpringFrameWork를 사용하니 해결됫다는 글을 보고  
다시 작성하니 정상적으로 생성되고 작동하는것을 확인했다.  

```java
public String getProduct(@CookieValue(value="history", required=false) String history, ....){

// 		Cookie History (Spring frameWork 사용)
		CookieGenerator cookie = new CookieGenerator();
		
		history = history + "," + product.getProdNo() + "/" + product.getProdName();
		
		cookie.setCookieName("history");
		cookie.addCookie(response, history);
}
```

[참조]https://marobiana.tistory.com/16