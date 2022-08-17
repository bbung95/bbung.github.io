---
title: "ArrayList"
excerpt: "ArrayList"

categories:
- Java
  tags:
- [Collection, List, Java]

permalink: /java/array-list/

toc: true
toc_sticky: true

date: 2021-03-27
last_modified_at: 2021-03-27
---
# ArrayList()

Array와 똑같이 배열의 기능을 가지고 있으며 vector의 단점을 보완한 interface기반의 클래스이다.

일반적으로 List의 add() method 와 get() method를 많이 사용한다.

vector는 배열의 방을 미리 정해주고 값이 존재하지 않더라고 방은 존재하여 메모리를 낭비하는 단점을 가지고 있다.

하지만 Arrylist는 그러한 단점을 보완하여 값이 존재시 방을 만들고  
값이 존재하지 않을 시 방을 삭제한다.

```java
ArrayList arr = new ArrayList();

arr.add("1");
arr.add("2");
arr.add("3");  // 3개의 방이 만들어진다
// 순차적구조로 들어간 순서로 [3][2][1]의 배열 값을 가지게 된다.

arr.remove() // 값과 동시에 방이 삭제된다.
// 2개의 방
```

이러한 기능을 통해 메모리를 아낄수 있으며 속도가 빠르다는 장점을 가지고 있어 vector보다 arraylist를 더 자주 사용한다.

vector와 같이 List를 구현한 인터페이스 기반의 클래스임으로  
List를 알면 vector와 arraylist를 다룰 수 있게 된다.
