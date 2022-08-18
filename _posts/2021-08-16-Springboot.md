---
title: "Springboot"
excerpt: "Springboot"

categories:
- Java
tags:
- [Spring, Java]

permalink: /java/spring-boot/

toc: true
toc_sticky: true

date: 2021-08-16
last_modified_at: 2021-08-16
---
# Spring Boot
---

Java언어 개발시 필수 프레임워크로 Spring 프레임워크가 존재한다. 
하지만 Spring 프레임워크는 XML을 사용하고 초기설정이 복잡하여 새로운 프로젝트 진행시 하나하나 설정해야하는 단점을 가지고있다.

이러한 단점을 보완하여 프로젝트의 초기 설정을 손쉽게 도와주며 라이브러리 관리를 편리하게 해주는 Springboot가 있다.

**스프링 부트란?** 
---
스프링 프레임워크를 사용하는 프로젝트를 아주 간편하게 설정할 수 있는 스프링 프레임웍의 서브 프로젝트라고 할 수 있다.  
쉽게말하면 스프링프레임워크 개발 스타트팩이라고 볼 수 있으며 단독실행가능한 스프링애플리케이션을 생성한다.

**특징**

- 독립형 Spring 애플리케이션 생성

- Tomcat, Jetty 또는 Undertow를 직접 포함(WAR 파일을 배포할 필요 없음)

- 빌드 구성을 단순화하기 위해 독자적인 '스타터' 종속성을 제공합니다.

- 메트릭, 상태 확인 및 외부 구성과 같은 프로덕션 준비 기능을 제공합니다.

- 코드 생성 및 XML 구성 요구 사항 없음

- 가능할 때마다 Spring 및 타사 라이브러리를 자동으로 구성

**스프링부트 종류**

스프링부트는 2가지의 종류로 빌드하는 방식에 따라 나눌 수 있다.

1. 메이븐 (Maven)

  - Maven은 Java용 프로젝트 관리도구로 Apache의 Ant 대안으로 만들어졌다.

  - 빌드 중인 프로젝트, 빌드 순서, 다양한 외부 라이브러리 종속성 관계를 pom.xml파일에 명시한다.

   - Maven은 외부저장소에서 필요한 라이브러리와 플러그인들을 다운로드 한다음, 로컬시스템의 캐시에 모두 저장한다.

2. 그래들 (Gradle)

  - Apacahe Maven과 Apache Ant에서 볼수 있는 개념들을 사용하는 대안으로써 나온 프로젝트 빌드 관리 툴이다. (완전한 오픈소스)

  - Groovy 언어를 사용한 Domain-specific-language를 사용한다. (설정파일을 xml파일을 사용하는 Maven보다 코드가 훨씬 간결하다.)

  - Gradle은 프로젝트의 어느부분이 업데이트되었는지 알기 때문에, 빌드에 점진적으로 추가할 수 있다.

 - 빌드된 라이브러리를 재실행 시키지 않아 시간을 단축 시킬 수 있다.

위와 같은 메이븐 , 그래들 두가지의 도구로 나눌 수 있다.

메이븐에 오랜시간 빌드관리로써 많은점유율을 가지고 있었지만 그래들로 점점 바뀌고 있는 추세이다.

## 그래들 구조
---

<img src="https://bbung95.github.io/public/img/gradle%20folder.png" style="width: 300px">

그래들은 스프링프레임워크와 비슷한 폴더구조를 가지고 있으며 다른점으로  
**application.properties** , **build.gradle**을 포함하고있다.

- 프로젝트 라이브러리는 build.gradle

- 프로젝트 설정과 같은 부분은 application.properties 에 작성하여 관리를 한다.

## 라이브러리 추가
---
이클립스를 통해 스프링부트 프로젝트 생성시 많이 사용하는 라이브러리를 추가하여 생성이 가능한데  
그 외의 추가적으로 필요한 라이브러리는 [https://mvnrepository.com/](https://mvnrepository.com/)에서 받을 수 있다.






