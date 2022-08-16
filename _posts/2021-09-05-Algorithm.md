---
layout: post
title: "Algorithm"
date: 2021-09-05 15:40:00 +0900
categories: Algorithm
---
# Algorithm
---

### for

끝이 정해져 있을 경우

### while

사전 판단 반복문

### do-while

사후 판단 반복문

```java
// b - a 를 출력하는 프로그램

int a, b;
		
		do {
		System.out.println("b을 입력해주세요");
		b = sc.nextInt();
		
		System.out.println("a을 입력해주세요");
		a = sc.nextInt();
		
		}while(b <= a);
		
		System.out.println("b - a 는 "+(b-a));
```

do-while은 루프문을 반드시 한번 실행시킨다.




