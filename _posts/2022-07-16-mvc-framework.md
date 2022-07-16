---
layout: post
title: "������ MVC - ���� ���� Framework"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
---

���������� Servlet�� Controller���� ���ε��� ���� �����ؼ� ������״�.  
������ ���������� MVC Pattern�� �����ϱ� ����, Controller�� �ϳ��� �����.  
__Front Controller__ �� ���� Controller�� Mapping �ϵ��� �� ���̴�.  

### Front Controller Version 1
[���� ÷��]

Controller�� Front Controller���� ���ϵ��� �Ѵ�.  
Front Controller�� Controller Map�� ����� ����, ��û�� ���� URI�� �´� ��ü�� �����´�.  

{% highlight java %}
// URL�� v1�� ���� ��� Controller�� ȣ���ϵ���.
@WebServlet(name = "frontControllerServletV1", urlPatterns = "/front-controller/v1/*"
public class FrontControllerServletV1 extends HttpServlet{
	private Map<String, ControllerV1> controllerMap = new HashMap<>();

    public FrontControllerServletV1() {
        controllerMap.put("/front-controller/v1/members/new-form", new MemberFormControllerV1());
        controllerMap.put("/front-controller/v1/members/save", new MemberSaveControllerV1());
        controllerMap.put("/front-controller/v1/members", new MemberListControllerV1());
    }
}
{% endhighlight %}

Servlet�� ```RequestURI``�� �̿��� URI�� ���� �� �ִ�.  
�� ���� ControllerMap�� Key ������ ��Ī�� ����, Controller�� �ҷ�����  
Controller ������ Method�� ȣ�������ν� View�� ����.  

### Front Controller Version 2 : MyView
[���� ÷��]

���� Version 1���� �� Controll���� ���� Method�� �ѹ� ����.  

{% highlight java %}
public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String viewPath = "/WEB-INF/views/new-form.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
}
{% endhighlight %}

3������ Controller ��ο� �� viewPath�� Dispatcher �ڵ尡 �ߺ����� �� �� �ִ�.  
Version 2������ �� �ߺ� �ڵ���� MyView��� Class�� �������� �Լ��� ���� ���̴�.  

�ռ� ����ߴ� Front Controller�� �����ϴ�.  
������ �̹��� �� Controller ���� Method�� MyView�� Return �ϵ��� �Ѵ�.  

{% highlight java %}
public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        return new MyView("/WEB-INF/views/new-form.jsp");
}
{% endhighlight %}

���� ����, MyView�� URI�� ���� View path�� �����ؼ�, Front Controller���� Render ��Ų��.  
�Ʊ� Version 1�� Dispatcher ������ MyView�� Render �Լ��� ���� �Ǹ�,  
�츮�� �� Controller���� �Ѱ��� View path�� ���ڷ� �޾�, Dispatcher�� Forward �Ѵ�.

### Front Controller Version 3 : ModelView
[���� ÷��]

�����غ���. ���� �� Controller�� ���忡�� Servelt�� �ʿ䰡 �ұ�?  
���, Front Controller�� Servlet�� �˸� ����, �� Controller�� ���� �ʿ䰡 ����.  
�׷��� Model�� ���� �Ѱ� �־�� ������ �� �� �����Ƿ�, Model�� ���� ���� �Ѱ�����.  

Model�� ���� �����ϰ�, View�� ���������ִ� ModelView Class�� ����� �������.  

{% hightlight java %}
public ModelView process(Map<String, String> paramMap) {
        List<Member> members = memberRepository.findAll();
        ModelView mv = new ModelView("members");
        mv.getModel().put("members", members);
        return mv;
}
{% endhighlight %}

Front Controller���� Servlet�� �̿���, Parameter Map�� ����� ����.  
���� ���� ���� Controller�� ���ڷ�, ```paramMap(�̸�, ��)```���� Parameter�� �ѱ��.  
�׷� Controller�� ModelView�� Field�� �̸� ����� Model�� Entity�� �����Ѵ�.    

{% hightlight java %}
public ModelView process(Map<String, String> paramMap) {
        List<Member> members = memberRepository.findAll();
        ModelView mv = new ModelView("members");
        mv.getModel().put("members", members);
        return mv;
}
{% endhighlight %}

���� Controller���� ��ȯ���� ModelView���� View name�� ���� �� �ִ�.  
View name�� View Resolver�� ���� View path�� ��ȯ�Ǹ�, �Ʊ�� ���� Render�� ����������.  

�߰���, �̹��� ���̴� Render �Լ����� Model���� ���� �Ѱ��־�� �Ѵ�.  
JSP ���Ͽ��� View�� Render�ϱ� ���ؼ� ���� �Ѱ��־�� �ϱ� �����̴�.  
ModelView���� Model�� ������ �Ѱ��ָ�, MyView���� ```setAttribute()```�� �����Ѵ�.  

### Front Controller Version 4
[���� ÷��]

�̹� ���������� ModelView�� ���ְ�, �׳� Model�� Parameter�� ���� �Ѱܺ���.  
Front Controller���� Parameter Map�� �Բ�, Model�� ������ �ؼ� ���鵵�� �Ѵ�.  
```paramMap```�� ��û Parameter���� ���� ������, Model�� �� ä�� �Ѱ��ش�.  

�׷� �� Controller���� ```paramMap```�� ```model```�� �Ѱܹ��� ��,  
```paramMap```�� ���� ���� Entity�� ����� ```model.put``` ���� �ѱ��.  
�׸��� ��ȯ ����, ������ ���� View name�� ��ȯ�Ѵ�.  
View Resolver�� ���� �Ȱ��� View path�� ��ȯ�Ͽ� MyView�� �Ѱ��ָ�,  
```render(model, req, resp)```�� ����, View�� Render���ָ� �������Ѵ�.  

### Front Controller Version 5 : Adpater
[���� ÷��]

�̹� ������ �ٸ� �����鿡 ���������� ����͸� �߰��Ѵ�.  
���� � �����ڴ� Version 3�� ���� �Ͱ� � �����ڴ� Version 4�� ���� �ʹٸ�?  

�׷� �� �ʿ��� ���� __Handler Adapter!__
Handler Adapter���� Controller�� ���ϵ��� �ϰ�, ������ Controller ������ �״�� �����Ų��.  

��� ���� ��� ��������, Spring MVC�� ������ �����ϱ� ���� �ذŸ��̴�.  
Spring MVC���� �����ϴ� ��� ��ɵ��� ��� ���� ������ �����ϱ� �����̴�.  

> - ���� �� �ڷ� ��ó : [�迵���� ������ MVC 1��](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)