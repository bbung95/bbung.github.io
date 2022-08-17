---
title: "Spring Web"
excerpt: "Spring Web"

categories:
- jave
  tags:
- [Spring, FrameWork, Java]

permalink: /java/spring-web/

toc: true
toc_sticky: true

date: 2021-05-17
last_modified_at: 2021-05-17
---

# Spring Web
---
자바의 스프링은 많은 프레임워크를 지원해 준다.  
그 중에 WEB Servlet을 지원하는 프레임워크가 있다.

Model2의 Controller(Action)부분을 보면 코드의 반복을 볼수 있다.  
이것을 정적인(Static) 부분과 동적인(Dynamic) 부분을 나눌수 있는데 

- DB에 접속하여 데이터를 가져오는 비지니스부분	
- 데이터를 객체나 ObjectScope에, Navigation 정보를 저장하는 정적인(Static) 부분
- Navigation을하는 동적인(Dynamic) 부분

이것을 컨트롤과 나눠 작성 할 수 있는데 이러한 것을 스프링에서 프레임워크로 제공해준다.

![](https://bbung95.github.io/public/img/spring%20Web.png)

각각 나눠진 코드의 내부를 보며 역활을 알아보자

### DispatcherServlet
---
```java
public class DispatcherServlet extends HttpServlet {

	public DispatcherServlet() {
	}

	public void service(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {

		System.out.println("\n[DispatcherServlet.service() start.........]");

		String actionPage = this.getURI(req.getRequestURI());
		System.out.println("::URI? => : " + req.getRequestURI());
		System.out.println(":: Client의 요구사항은 ? => : " + actionPage);

		req.setCharacterEncoding("EUC_KR");

		Controller controller = null;

		ControllerMapping cf = ControllerMapping.getInstance();
		controller = cf.getController(actionPage);

		System.out.println("====================================");
		
		ModelAndView modelAndView = controller.execute(req, res); // Static Data와 Navigation을 담은 객체
		// 컨트롤러를 통해서 비지니스 로직을 수행후 Static Data와 Navigation을 ModelAndView객체에 저장
		new ViewResolver().forward(req, res, modelAndView);
		// ModelAndView 객체에 담긴 Data와 Navigation에 해당하는 view로 보냄
	}

	private String getURI(String requestURI) {
		int start = requestURI.lastIndexOf('/') + 1;
		int end = requestURI.lastIndexOf(".do");
		String actionPage = requestURI.substring(start, end);
		return actionPage;
	}
}
```
### 1. Controller
---
  
먼저 DB에 접근하는 컨트롤파트를 보자면 클라이언트로 부터 들어온 정보를 DispatcherServlet로 받아와 Mapping을 통해  
클라이언트가 원하는 행동을 리턴하여 컨트롤을 실행시키게 된다.  

컨트롤로 들어오게 되면 비지니스와 데이터의 저장과 Navigation을 하게 되는데  
데이터와 Navigation정보를 저장할수 있는 Spring FrameWork ModelAndView를 리턴하여  
비지니스와 네비게이션을 분리 할 수 있다.

```java
public class LogonController implements Controller{

	public LogonController() {
	}

	public ModelAndView execute(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
			
		System.out.println("[ LogonController.excute() start..........]");
		
		HttpSession session = req.getSession(true);
		
		if(session.isNew() || session.getAttribute("userVO") == null) {
			session.setAttribute("userVO", new UserVO());
		}
		
		UserVO userVO = (UserVO)session.getAttribute("userVO");
		System.out.println(userVO);
		String requestPage = "/user/logon.jsp";
		
		if(userVO.isActive()) {
			requestPage = "/user/home.jsp";
		}
		
		/*
		RequestDispatcher rd = req.getRequestDispatcher(requestPage);
		rd.forward(req, res);
		*/
		
		System.out.println("[ LogonActionController.execute() end..........]");
		
		return new ModelAndView(requestPage, "info", "[LogonActionController Message] :: Welcome");
		}
	
	}

```
### 2. ModelAndView
---
ModelAndView는 Spring에서 지원하는 FrameWork중 하나로 데이터와 Navigation정보를 담을수 있다.  

```java
public class ModelAndView {

	private String viewName;    // Navigation 정보
	private String modelName;   // 데이터의 키값
	private Object modelObject; // 데이터(객체)를 받는다
	
	public ModelAndView() {
	}
	public ModelAndView(String viewName) {
		this.viewName = viewName;
	}
	public ModelAndView(String viewName, String modelName, Object modelObject) {
		this.viewName = viewName;
		this.modelName = modelName;
		this.modelObject = modelObject;
	}
	public String getViewName() {
		return viewName;
	}
	public void setViewName(String viewName) {
		this.viewName = viewName;
	}
	public String getModelName() {
		return modelName;
	}
	public void setModelName(String modelName) {
		this.modelName = modelName;
	}
	public Object getModelObject() {
		return modelObject;
	}
	public void setModelObject(Object modelObject) {
		this.modelObject = modelObject;
	}
	
}
```
### 3. ViewReSolver
---
Spring에서 지원하는 FrameWork중 하나로 Navigation을 하는 Dynamic한 부분을 맡고 있다.

만약 데이터가아닌 Navigation만 하는 경우도 지원하지만 나중에 배울 String 을 사용하면 더 간단하게 Navigation을 할 수 있다. 

ViewResolver로 보내지는 데이터는 request에 저장되며 getAttribute로 불러 올 수 있다.
```java
public class ViewResolver {

	public ViewResolver() {
	}
	
	public void forward(HttpServletRequest request, HttpServletResponse response, ModelAndView modelAndView) throws ServletException, IOException {
		
		System.out.println("[ViewResolver.foreward() start........]");
		
		if(modelAndView.getModelName() != null) {

			request.setAttribute(modelAndView.getModelName(), modelAndView.getModelObject());
		}
		
		request.getRequestDispatcher(modelAndView.getViewName()).forward(request, response);
		
		System.out.println("[ViewResolver.forward() and.........]");
		
	}
}

```

ModelAndView와 ViewResolver를 사용하려면 lib추가 또는 ClassPath를 잡아 사용한다.