---
title: "Realization(실체화)"
excerpt: "Realization(실체화)"

categories:
- Java
  tags:
- [Realization, Java]

permalink: /java/realization/

toc: true
toc_sticky: true

date: 2021-03-15
last_modified_at: 2021-03-15
---

# Realization(실체화)

**Interface**는 Realization(구체적)과 관련이 있는 키워드이다.  
자바는 extends(상속)을 지원하며 상속으로 각 클래스를 공유 할 수 있다.  
공유를 통해 추상적인 beans 클래스를 구체적으로 구현 할 수 있는데  
그러한 추상클래스를 abstract Class라고 한다.

abstract는 상속을 통해 overriding을 하여 상위 Class에서 더이상 필요가 없다고 생각하지만  
추상적으로는 존재하여야 하기 때문에 abstract를 사용하여 추상클래스로 만드는것이다.  
abstract는 method와 Class에 사용하며 method에만 사용시 오류가 발생한다.  
추상클래스는 추상적으로만 존재하기에 속성값을 변경하는 method로 사용이 불가능하다.  
잘못하여 추상 method를 부를시 오류가 나기에 그것을 사전방지하고자 Class와 method에 abstract를 붙인다.

하지만 자바의 상속은 단일 상속만 지원하기 때문에 RealWord의 객체를 표현하기에는 어려움이 있다.  
그래서 나온것이 Interface이다.

Interface를 는 다중 implements(구현)을 지원을 한다.  
사용법으로는

```java
interface Fee{
    public void charge();
}
interface Class를 작성이 가능하고
```

```java
pulbic class BusCharge extends Students implements Fee{
}
적용이 가능하다.
```

Interface의 field는 default값으로 public final static을 가진다.
그렇기에 Class로 field를 불러올 수 있기에 인스턴스를 만들지 않아도 된다.
method는 default로 publid abstract를 가진다.
