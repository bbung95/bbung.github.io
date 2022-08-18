---
title: "fileUpload"
excerpt: "fileUpload"

categories:
- Java
tags:
- [Spring, Java]

permalink: /java/file-upload/

toc: true
toc_sticky: true

date: 2021-06-01
last_modified_at: 2021-06-01
---
# fileUpload
---

Html의 form 태그는 기본적으로 application/x-www-form-urlencoded 방식의 타입을 사용하며  
name=value의 구조로 데이터를 보낸다.

하지만 파일은 업로드하기에는 맞지않는 타입을 가지고 있어 파일을 업로드하기 위해선  
form태그에 enctype="multipart/form-data" 의 타입을 사용해야한다.

### MultiPart

스프링 프레임워크를 이용하면 편하게 파일 업로드를 할 수 있다.

필요한 lib
- com.springsource.org.apache.commons.fileupload.jar  
- com.springsource.org.apache.commons.io.jar

먼저 스프링 common-servlet.xml의 MultipartResolver를 설정해줘야 한다.

CommonsMultipartResolver 을 빈 클래스로 등록한다.

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:context="http://www.springframework.org/schema/context"
		xmlns:mvc="http://www.springframework.org/schema/mvc"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation=
				"http://www.springframework.org/schema/beans 
				http://www.springframework.org/schema/beans/spring-beans.xsd
				
				http://www.springframework.org/schema/context 
				http://www.springframework.org/schema/context/spring-context.xsd"
				>

    <!-- 아래 코드를 추가 -->
	<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"></bean>

</beans>
```

**VO**  

도메인 객체의 MultipartFile 타입의 필드를 추가한다.

```java
public class Product {
	
	private String fileName;
	private String manuDate;
	private int price;
	private String prodDetail;
	private String prodName;
	private int prodNo;
	private Date regDate;
	private String proTranCode;
	
	private MultipartFile file;
```

**@Controller**  

@ModelAttribute로 form 태그 안에 있는 데이터들을 도메인객체에 바인딩한다.
file 타입으로 넘어오는 데이터는 MutipartFile file에 바인딩된다.

- @ModelAttribute를 사용 하지않고 MutiPartFile file을 선언하여 파일을 받아 올 수 있다.

```java
@RequestMapping(value = "addProduct", method = RequestMethod.POST)
	public String addProduct(@ModelAttribute("product") Product product) throws Exception {

		System.out.println("/product/addProduct :: POST");
		// Business Logic
		//////////
		MultipartFile file = product.getFile();
		if(file != null) {
			String fileName = file.getOriginalFilename();
			product.setFileName(fileName);
			try {
				File file2 = new File("/images/uploadFiles/"+fileName);
				file.transferTo(file2);
			}catch (Exception e) {
				e.printStackTrace();
			}
		}
		//////////
		productService.addProduct(product);

		return "forward:/product/addProduct.jsp";
	}
```

### 다중파일 업로드
---  

input 태그에 multiple="multiple"를 추가하면 여러개의 파일을 선택할 수 있게 된다.

여러개의 파일을 선택후 MultipartFile을 어레이로 설정하여 다중파일을 업로드 할 수 있게된다.

```html
<td class="ct_write01">
			<input	type="file" name="file" class="ct_input_g" 
					style="width: 200px; height: 19px" maxLength="13" multiple="multiple"/>
</td>
```

향상된 for문을 이용하여 file을 하나씩 불러와 파일을 저장하고 CSV를 통해 파싱하여 데이터베이스에 저장을 한다.

```java
@RequestMapping(value = "addProduct", method = RequestMethod.POST)
	public String addProduct(@ModelAttribute("product") Product product) throws Exception {

		System.out.println("/product/addProduct :: POST");
		// Business Logic
		//////////
		String fileName = "";
		MultipartFile[] files = product.getFile();
		for(MultipartFile file : files) {
				
			if(file != null || !file.isEmpty()) {
				fileName += file.getOriginalFilename()+"/";
				try {
					File file2 = new File("/images/uploadFiles/"+fileName);
					file.transferTo(file2);
				}catch (Exception e) {
					e.printStackTrace();
				}
			}
		}
		//////////
		
		product.setFileName(fileName);
		productService.addProduct(product);

		return "forward:/product/addProduct.jsp";
	}
```

데이터베이스에서 가져온 file을 JSTL split을 통해서 분리하여 각각의 이미지파일로 출력을 할 수 있다.

- <%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
- ${fn:split(String, 'csv')}

```html
<%@ page language="java" contentType="text/html; charset=EUC-KR"
    pageEncoding="EUC-KR"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>

<tr>
		<td width="104" class="ct_write">
			상품이미지 <img 	src="/images/ct_icon_red.gif" width="3" height="3" align="absmiddle"/>
		</td>
		<td bgcolor="D6D6D6" width="1"></td>
		<td class="ct_write01">
			<c:set var="file" value="${product.fileName }"/>
			<c:forEach var="fileName" items="${fn:split(file, '/')}">  
				<img src="/images/uploadFiles/${fileName}" width="400" height="400"/>
			</c:forEach>
		</td>
	</tr>
```
