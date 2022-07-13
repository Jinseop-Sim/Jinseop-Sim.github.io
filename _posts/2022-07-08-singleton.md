---
layout: post
title: "������ �ٽ� ���� ���� - Singleton"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
---
���� �������� Client�� ���ÿ� ��û�� �����ٰ� �����غ���.  
�׷� DI Container���� ��� ��û�� ���� ��ü�� �� ���� �����ؾ� �ұ�?  
�׷� �� ������ ���� ���ÿ� ��û�� �ϸ�, �� ������ ��ü�� �����Ǿ� ������.  

�̴� ���� �޸��� ���� �ʷ��Ѵ�!  

### Singleton Pattern
���� �������� �ذ��ϱ� ���ؼ� ������ ������ ������ �ٷ� __Singleton__ �̴�.  

��ü�� ������ ����, �̹� ������ ��ü�� ��ȸ�ϰ� �����ϰ� �Ѵ�.  
�翬�� �׷� ���� �޸��� ���� ��������, ������ ���� �ȴ�.  

##### Singleton�� ����
���� Java �ڵ�δ� �Ʒ��� ���� �����ϵ��� �Ѵ�.  

{% highlight java %}
public class SingletonService {

    public static final SingletonService instance = new SingletonService();
    // ���� ó�� Java���� ��ü�� �ϳ� �������ش�.

    public static SingletonService getInstance(){
        return instance;
    }
    // ���� ����� ��ȸ �ۿ� ����!

    private SingletonService(){
    }
    // private���� ���� ��ü ������ ���´�.
}
{% endhighlight %}

#### Singleton�� ������
- ���� ���� �ڵ尡 ������ ���� ����.
- �������� �� Ŭ���̾�Ʈ�� ��ü Ŭ������ �����Ѵ�. --> DIP ����.
  - �׿� ���� OCP ��Ģ�� ������ ���ɼ��� ����.
- ���� �Ӽ� ���� �� �ʱ�ȭ�� �ſ� ��ƴ�.
- private �����ڷ� �ڽ� Ŭ������ ����� ���� ��ƴ�.
- ��������� �������� ������ ������, Anti-Pattern�̶�� �Ҹ��⵵ �Ѵ�.

### Singleton Container
> ���� ���������� �ذ��ϱ� ���� ������ ���� �ٷ� Spring Container!  

Spring Container�� �˾Ƽ� ��ü�� Singleton���� �������ִ� ���ÿ�  
Singleton Pattern�� ��� ������ �ذ����ֱ���� �Ѵ�.
�������� �ڵ尡 ������ ������, DIP, OCP�� �������� �ʾƵ� �ȴ�.

### Singleton ��� �� ������
Singleton Pattern������ ������ ��ü�� ���� Ŭ���̾�Ʈ�� �����Ѵ�.  
�� ��, Stateful�ϰ� �����ع����� ���¸� �����ϴ� �ʵ带 �����ع����ٸ�? 

���� ���, A����ڰ� ����ġ�� �ֹ��� �־��ٰ� �����غ���.  
���ڸ��� B����ڰ� 2����ġ�� �ֹ��� �־������ A����ڿ��Ե� 2������ �� ���̴�.  
�̴� �� ������ �ڵ�� ���ԵǸ�, ���񽺿��� ū ������ �ʷ��ϰ� �ȴ�.  

���� �׻� ������(Stateless)�� ���踦 �ؾ߸� �Ѵ�!  

> - ���� �� �ڷ� ��ó : [�迵���� ������ �ٽ� ���� �⺻��](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)