---
title: "Select Tag"
excerpt: "Select Tag"

categories:
- HTML
tags:
- [HTML, Tag]

permalink: /html/select-tag/

toc: true
toc_sticky: true

date: 2021-05-24
last_modified_at: 2021-05-24
---

# Select Tag

Select 태그는 콤보박스를 지원하는 태그이다.


**example 1)**
<select name="comboBox">
	<option value="1">1번</option>
	<option valye="2">2번</option>
	<option value="3">3번</option>
	<option value="4">4번</option>
</select> 


form 태그안에 select 태그를 사용시 name에 값을 설정하여 선택된 value값을 받을 수 있다. 
```html
<select name="comboBox">
	<option value="1">1번</option>
	<option valye="2">2번</option>
	<option value="3">3번</option>
	<option value="4">4번</option>
</select>
```


**exaple 2)**
<select name="comboBox">
	<option value="">번호</option>
	<option value="1">1번</option>
	<option valye="2">2번</option>
	<option value="3">3번</option>
	<option value="4">4번</option>
</select>

Select 태그는 맨 처음에 있는 option에 값을 display를 한다.
```html
<select name="comboBox">
	<option value="">번호</option>
	<option value="1">1번</option>
	<option valye="2">2번</option>
	<option value="3">3번</option>
	<option value="4">4번</option>
</select>
```
**exaple 3)**
<select name="comboBox">
	<option value="">번호</option>
	<option value="1">1번</option>
	<option valye="2" selected="">2번</option>
	<option value="3">3번</option>
	<option value="4">4번</option>
</select>

Select 태그에 default로 display하는 방법으로는 selected 속성을 사용하는 방법이 있다.

```html
<select name="comboBox">
	<option value="">번호</option>
	<option value="1">1번</option>
	<option valye="2" selected>2번</option>
	<option value="3">3번</option>
	<option value="4">4번</option>
</select>
```

**exaple 4)**
<select name="comboBox" onchange="alert('onChange')">
	<option value="">번호</option>
	<option value="1">1번</option>
	<option valye="2" >2번</option>
	<option value="3">3번</option>
	<option value="4">4번</option>
</select>

Html에는 onClick이라는 속성을 지원한다.  
이것은 클릭시 해당 속성에 값을 실행할수 있게하는 동적인 속성이라 볼 수 있다.

Select도 비슷한 기능을 지원하는 속성이 있다.
onChange 속성은 콤보박스 안에 있는 옵션을 클릭시 속성에 값을 실행하는 속성이라고 볼 수 있다.

```html
<select name="comboBox" onchange="alert('onChange')">
	<option value="">번호</option>
	<option value="1">1번</option>
	<option valye="2">2번</option>
	<option value="3">3번</option>
	<option value="4">4번</option>
</select>
```
