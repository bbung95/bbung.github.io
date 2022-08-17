---
title: "Annotation"
excerpt: "Annotation"

categories:
- java
  tags:
- [Spring, FrameWork, Annotation, Java]

permalink: /java/annotation/

toc: true
toc_sticky: true

date: 2021-05-24
last_modified_at: 2021-05-24
---

# Annotation
---
Annotation(@)은 사전적 의미로는 주석이라는 뜻이다.  
자바에서 Annotation은 코드 사이에 주석처럼 쓰이며 특별한 의미, 기능을 수행하도록 하는 기술이다.  
즉, 프로그램에게 추가적인 정보를 제공해주는 메타데이터라고 볼 수 있다.  

meta data : 데이터를 위한 데이터

Reflection이란 프로그램이 실행 중에 자신의 구조아 동작을 검사하고, 조사하고, 수정하는 것이다.
- @Annotation은 Reflection 하기 전까지는 그냥 주석의 의미를 가진다.
- 어노테이션을 사용가능하게끔 한다.

다음은 어노테이션의 용도를 나타낸 것이다.

1. 컴파일러에게 코드 작성 문법 에러를 체크하도록 정보를 제공한다.

2. 소프트웨어 개발 툴이 빌드나 배치시 코드를 자동으로 생성할 수 있도록 정보를 제공한다.

3. 실행시(런타임시)특정 기능을 실행하도록 정보를 제공한다.

4. 기본적으로 어노테이션을 사용하는 순서는 다음과 같다.
	- 어노테이션을 정의한다.  
	- 클래스에 어노테이션을 배치한다.  
    - 코드가 실행되는 중에 Reflection을 이용하여 추가 정보를 획득하여 기능을 실시한다.

[참고] https://velog.io/@gillog/Spring-Annotation-%EC%A0%95%EB%A6%AC