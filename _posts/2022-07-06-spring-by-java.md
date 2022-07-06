---
layout: post
title: "������ �ٽ� ���� ���� - Java ����"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
---

�������� �ٽ� ��ҿ� ������ ���� �����ϱ� ����  
�����ϰ� Java�θ� �� �� ���踦 �غ����� ����.  

### ȸ�� ������ ����
ȸ�� �������� ���� �Ʒ��� ���� ���踦 ������.  
���� �����δ� �� �����ϰ�����, �켱 ������ ����� ���踦 �غ���.  

[���� ÷��]

�и� Java ���� ��ü ���� ����̸�, �������� ����ϴµ� ���� ��ü ���� 5��Ģ�� ������ �ڵ��ΰ�?  
���� �ƴϴ�!  

[���� ÷��]  

���� �κе�� ����, OCP�� DIP ��� �������� �ʰ� �ִ�!  
�׷� �츮�� �̷� �������� ��� �ذ��ؼ� ����ȭ�� ��ų �� ������?  

#### Bonus : Code Test
Java�� JUnit������ ���� § �ڵ尡 ����� �����ϴ� �� Ȯ���� �� �ִ�  
Test ����� �����ϰ� �ִ�.  
������ �Ʒ��� ����.

{% highlight java %}
public class OrderServiceTest {
    MemberService memberService = new MemberServiceImpl();
    OrderService orderService = new OrderServiceImpl();
    // Service�� �����;� �̿��� �����ϴ�.

    @Test // Annotation���� ��� ���
    void createOrder(){
        Long memberId = 1L;
        Member member = new Member(memberId, "Agent A", Grade.VIP);
        memberService.join(member);
        // join ����� ���� DB�� ����.

        Order order = orderService.createOrder(memberId, "Umbrella", 10000);
        // createOrder�� ���� �ֹ� ����.
        Assertions.assertThat(order.getDiscountPrice()).isEqualTo(1000);
        // Assertions.assertThat() ���� �ٸ��� �����ϴ� �� ����.
    }
}

{% endhighlight %}