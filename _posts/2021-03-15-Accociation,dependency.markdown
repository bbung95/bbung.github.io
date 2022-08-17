---
title: "Accociation, dependency"
excerpt: "Accociation, dependency"

categories:
- Java
tags:
- [Accociation, Dependency, Java]

permalink: /java/accociation-dependency/

toc: true
toc_sticky: true

date: 2021-03-15
last_modified_at: 2021-03-15
---

# Accociation, dependency

**dependency** 는 또 다른 말로는 의존성이라도고 한다.  
다른 클래스에서 어떤 기능이 필요시에만, 클래스를 선언하여 사용하는 것이다.  
해당 클래스는 한번 사용 후 사라진다.

method를 통해 행동을 하는데 method를 통해 class를 인스턴스화 하여 사용한다면 사용후  
만들어진 인스턴스는 사라질 것이다.
보드마카로 예를 들자면 발표를 할 때 사용을 한다.  
하지만 사용후 그것을 더이상 사용하지 않기에 이것을 depndency로 볼 수 있다.

**Accociation** 은 객체 또는 클래스가 다른 객체 또는 클래스와  
'어떤 의미의' 관계를 가질 수 있다로 정리 할 수 있다.  
has a ~ 관계로 볼 수 있다.

Accociation은 dependency와는 달리 관계를 가진다.  
위에 보드마카로 예를 들었다면 휴대폰으로 예를 들어보자  
휴대폰은 전화를 받거나 서핑을 한 후 버리는 것이 아니라 나에게 종속(?)적이기 때문에  
나와의 관계가 있다.  
이것을 Accociation이라고 할 수 있다.
