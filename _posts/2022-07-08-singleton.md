---
layout: post
title: "스프링 핵심 원리 이해 - Singleton"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
---
만약 여러명의 Client가 동시에 요청을 보낸다고 생각해보자.  
그럼 DI Container에서 모든 요청에 대해 객체를 다 따로 생성해야 할까?  
그럼 수 만명의 고객이 동시에 요청을 하면, 수 만개의 객체가 생성되어 버린다.  

이는 심한 메모리의 낭비를 초래한다!  

### Singleton Pattern
위의 문제점을 해결하기 위해서 등장한 디자인 패턴이 바로 __Singleton__ 이다.  

객체의 생성을 막고, 이미 생성된 객체를 조회하고 공유하게 한다.  
당연히 그로 인해 메모리의 낭비가 없어지고, 성능이 개선 된다.  

##### Singleton의 구현
순수 Java 코드로는 아래와 같이 구현하도록 한다.  

{% highlight java %}
public class SingletonService {

    public static final SingletonService instance = new SingletonService();
    // 제일 처음 Java에서 객체를 하나 생성해준다.

    public static SingletonService getInstance(){
        return instance;
    }
    // 접근 방법은 조회 밖에 없다!

    private SingletonService(){
    }
    // private으로 막아 객체 생성을 막는다.
}
{% endhighlight %}

#### Singleton의 문제점
- 패턴 구현 코드가 굉장히 많이 들어간다.
- 의존관계 상 클라이언트가 구체 클래스에 의존한다. --> DIP 위반.
  - 그에 따라 OCP 원칙을 위반할 가능성이 높다.
- 내부 속성 변경 및 초기화가 매우 어렵다.
- private 생성자로 자식 클래스를 만드는 것은 어렵다.
- 결론적으로 유연성이 굉장히 떨어져, Anti-Pattern이라고 불리기도 한다.

### Singleton Container
> 위의 문제점들을 해결하기 위해 등장한 것이 바로 Spring Container!  

Spring Container는 알아서 객체를 Singleton으로 관리해주는 동시에  
Singleton Pattern의 모든 단점을 해결해주기까지 한다.
지저분한 코드가 들어가지도 않으며, DIP, OCP를 위반하지 않아도 된다.

### Singleton 사용 시 주의점
Singleton Pattern에서는 동일한 객체를 여러 클라이언트가 공유한다.  
이 때, Stateful하게 구현해버려서 상태를 유지하는 필드를 공유해버린다면? 

예를 들어, A사용자가 만원치의 주문을 넣었다고 가정해보자.  
넣자마자 B사용자가 2만원치의 주문을 넣어버리면 A사용자에게도 2만원이 뜰 것이다.  
이는 더 복잡한 코드로 가게되면, 서비스에서 큰 문제를 초래하게 된다.  

따라서 항상 무상태(Stateless)로 설계를 해야만 한다!  

> - 사진 및 자료 출처 : [김영한의 스프링 핵심 원리 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)