---
layout: post
title: "Spring Project�� ���� ���� ����"
categories: Springboot
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
Spring���� Project�� �����ϰ� ������ ���� ������ �뷫������ �����غ���.  

### �䱸���� �м�
��� �䱸�� ������ �� �� �䱸������ ����� �м����� �ſ� �߿��ϴ�.  
���� �����ϴ� ȭ���̳�, ��� ����� ���ؼ� �츮�� �����ؾ� �� ���� �����Ѵ�.  
[���� ����]

### Entity �м� �� ���̺� ����
���� �ǹ������� __DDD(������ �ֵ� ����)___ �� ������� SW ���踦 �����Ѵٰ� �Ѵ�.  
[��ũ ����]

���� �츮�� ������ ���� ��Ȯ�� �����ϰ� �׿� ���� DB ���̺��� �����ؾ� �Ѵ�.  
��ƼƼ�� � ���� ���� �ϴ���, Class���� �������踦 �δ��� ����� ������ �ʿ䰡 �ִ�.  
[���� ����]

### Entity Class ����
DB ���踦 ��������, �ٷ� Entity Class ���߿� ������ �� �ִ�.  

- Entity ���� �� ������!
  1. ��� �ε��� ������ ��ư�, � SQL�� ������� �����ϱⰡ ��ƴ�.
    - Ư�� JPQL�� ������ �� N+1 ������ �߻�Ȱ Ȯ���� ��������.
    - ���� �ݵ�� ��� ��������� __LAZY__ �� �����ϵ��� ����!
  2. Java Collection�� �ݵ�� �ʵ忡�� �ٷ� �ʱ�ȭ ������.
    - ���� �����ڿ��� �����ܰ迡 �ʱ�ȭ�� �� ���� �ִ�.
    - ������ Best Practice�� �ʵ忡�� ��ٷ� �ʱ�ȭ ���ִ� ��!
    - �̴� NPE�κ��� �츮�� �����ϰ� �����ش�.
  3. Table, Column�� ������ �� �̸��� ���� ��Ģ�� ������ ���ؾ� �Ѵ�.
    - ���� Java���� Camel Case, DB���� �ҹ��� + Underscore�� ������ ��찡 ����.
    - �������� DB ���� �� Camel Case�� �ڵ����� Underscore�� �ٲ��ش�.
    - �빮�ڵ� �ҹ��ڷ� �ڵ����� �ٲ��ش�.
    - ���� �ǹ��� ���ԵǾ��� ��, ȸ�翡�� ���س��� ���Ը� ������ ���� �´�.
  4. Cascade ������ �� �س��� ��!
    - Cascade ������ �� �س����� �ڵ尡 �ſ� �����ϰ� ���������.

### Application ���� : Architecture
���� ���� ���ø����̼��� ������ �� �ܰ��̴�.  
�츮�� ���ø����̼��� ������ �� ������ ������ ����� ���̴�.  

- ������ ����?
  - Controller(Web) : ���� �� API ����
  - Service : ����Ͻ� ����, Ʈ����� �۾��� �ϴ� ����
  - Repository : JPA�� ����ϴ�, DB�� ������ �ϴ� ����
  - Domain : Entity�� ���� ����
- ���� ����
  - __Repository -> Service -> Test Code �ۼ� -> Controller �ۼ�__
  - __�� �κ��� ���Ŀ� TDD�� ���� �������ϸ� �����ϰڴ�.__

#### DTO�� �̿�
DTO(Data Transfer Object)��?  
- Client �ܰ��� ���뿡 �־ �ʿ��� ���� �̾Ƽ� ����� ��ü�̴�.  
- DTO�� �� ����ϳ���?
  - Entity�� ��� �Ӽ��� �ܺο� ����Ǹ� ���� ������ �߻��� �� �ִ�.
  - ������ ���ߴٽ���, �ʿ��� ���� �̾Ƽ� ��ü�� ����� ������ ȿ�����̴�.
  - ������ ������ �־, ���� ���� �и��� Ȯ���ϰ� �� �� �ִ�.

### Application ���� : Repository
DB�� ���������� ������ �ϴ� ������ Repository �����̴�.  
Spring JPA�� �̿��ϱ� ���ؼ�, �ݵ�� ```EntityManager``` �� ����ؾ� �Ѵ�.  

{% highlight java %}
@PersistContext // ǥ�� Annotation, Springboot�� @Autowired ����.
private EntityManger em;
{% endhighlight %}

### Application ���� : Service
���� Repository�� ���� �޾Ƽ� ����� ����ϰ�, ������ ���� �κ��̴�.  
Repository�� ����� �̿��� DB�� ������ �߻���Ű�� ������ Transaction ������ �����Ѵ�.  

���� ���� ���� __Validation__ ���� �� Service �ܿ��� �����Ѵ�.  
Validation ���� ����Ͻ� �����̹Ƿ�, Service �ܿ� ���踦 �ϴ� ���� �Ǵ�.  

�⺻������ �ڵ忡 ```@Service``` Annotation�� �־� Spring Bean���� ����ϵ��� �Ѵ�.  
������ ���� ������� Repository�� ���Թ޾Ƽ� ����Ѵ�.  

### Application ���� : Controller

### Application ���� : View
������ Client �ܿ� �ѷ����� View�� �����ϴ� ��Ŀ��� �� ������ �����Ѵ�.  

- SSR(Server Side Rendering)
  - �������� ���� HTML�� ���, Data�� HTML�� �ѷ��־� Rendering �ϴ� ���.
  - JSP, Thymeleaf �� ���.
- CSR(Client Side Rendering)
  - �������� HTML�� ������� �ʴ´�.
  - �������� Client(Frontend) ������ Data�� Ư�� ���·� �����ش�.
  - �׷� Client �ܿ��� �ش� Data�� �����ؼ� Client���� Rendering ���ִ� ���.
  - React, Vue ���� ���� Javascript Coding.
