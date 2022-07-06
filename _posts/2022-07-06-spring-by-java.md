---
layout: post
title: "스프링 핵심 원리 이해 - 예제 1"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
---

스프링의 핵심 요소와 원리에 대해 공부하기 전에  
순수하게 Java로만 한 번 설계를 해보도록 하자.  

### 회원 도메인 설계
회원 도메인은 보통 아래와 같은 관계를 가진다.  
물론 실제로는 더 복잡하겠지만, 우선 이정도 관계로 설계를 해보자.  

![도메인설계](https://user-images.githubusercontent.com/71700079/177573291-dfde0e5e-a3c3-4c3c-a047-9bc5aa5965bc.png)  
![ehapdls](https://user-images.githubusercontent.com/71700079/177573314-c7de1571-178e-49c9-9c83-4f49d960b454.png)  

분명 Java 또한 객체 지향 언어이며, 다형성을 사용하는데 과연 객체 지향 5원칙에 부합한 코딩인가?  
결코 아니다!  

[사진 첨부]  

위의 부분들로 인해, OCP와 DIP 모두 지켜지지 않고 있다!  
그럼 우리는 이런 문제들을 어떻게 해결해서 최적화를 시킬 수 있을까?  

#### Bonus : Code Test
Java의 JUnit에서는 내가 짠 코드가 제대로 동작하는 지 확인할 수 있는  
Test 기능을 제공하고 있다.  
사용법은 아래와 같다.

{% highlight java %}
public class OrderServiceTest {
    MemberService memberService = new MemberServiceImpl();
    OrderService orderService = new OrderServiceImpl();
    // Service를 가져와야 이용이 가능하다.

    @Test // Annotation으로 기능 사용
    void createOrder(){
        Long memberId = 1L;
        Member member = new Member(memberId, "Agent A", Grade.VIP);
        memberService.join(member);
        // join 기능을 통해 DB에 저장.

        Order order = orderService.createOrder(memberId, "Umbrella", 10000);
        // createOrder을 통해 주문 생성.
        Assertions.assertThat(order.getDiscountPrice()).isEqualTo(1000);
        // Assertions.assertThat() 으로 바르게 동작하는 지 검증.
    }
}

{% endhighlight %}

사진 및 자료 출처 : [김영한의 스프링 핵심 원리 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)
