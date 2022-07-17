---
layout: post
title: "스프링 MVC - 직접 만든 Framework"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
---

이전까지는 Servlet의 Controller들을 따로따로 각각 구현해서 실행시켰다.  
이제는 본격적으로 MVC Pattern을 구현하기 위해, Controller을 하나로 묶어보자.  
__Front Controller__ 를 만들어서 Controller을 Mapping 하도록 할 것이다.  

### Front Controller Version 1
[사진 첨부]

Controller를 Front Controller에서 정하도록 한다.  
Front Controller에 Controller Map을 만들어 놓고, 요청이 들어온 URI에 맞는 객체를 가져온다.  

{% highlight java %}

// URL이 v1의 하위 모든 Controller을 호출하도록.
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

Servlet의 ```RequestURI```를 이용해 URI를 받을 수 있다.  
그 값을 ControllerMap에 Key 값으로 매칭을 시켜, Controller을 불러오고  
Controller 내부의 Method를 호출함으로써 View를 띄운다.  

### Front Controller Version 2 : MyView
[사진 첨부]

이전 Version 1에서 각 Controll들의 내부 Method를 한번 보자.  

{% highlight java %}

public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String viewPath = "/WEB-INF/views/new-form.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
}

{% endhighlight %}

3가지의 Controller 모두에 이 viewPath와 Dispatcher 코드가 중복됨을 알 수 있다.  
Version 2에서는 이 중복 코드들을 MyView라는 Class로 빼버리고 함수로 만들 것이다.  

앞서 사용했던 Front Controller은 동일하다.  
하지만 이번엔 각 Controller 내부 Method가 MyView를 Return 하도록 한다.  

{% highlight java %}

public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        return new MyView("/WEB-INF/views/new-form.jsp");
}

{% endhighlight %}

위와 같이, MyView에 URI를 직접 View path를 주입해서, Front Controller에서 Render 시킨다.  
아까 Version 1의 Dispatcher 과정은 MyView의 Render 함수로 들어가게 되며,  
우리가 각 Controller에서 넘겨준 View path를 인자로 받아, Dispatcher을 Forward 한다.

### Front Controller Version 3 : ModelView
[사진 첨부]

생각해보자. 굳이 각 Controller들 입장에서 Servelt이 필요가 할까?  
사실, Front Controller만 Servlet을 알면 되지, 각 Controller은 굳이 필요가 없다.  
그래도 Model에 값은 넘겨 주어야 렌더링 할 수 있으므로, Model을 따로 만들어서 넘겨주자.  

Model을 따로 저장하고, View를 렌더링해주는 ModelView Class를 만들어 사용하자.  

{% highlight java %}
public ModelView process(Map<String, String> paramMap) {
        List<Member> members = memberRepository.findAll();
        ModelView mv = new ModelView("members");
        mv.getModel().put("members", members);
        return mv;
}
{% endhighlight %}

Front Controller에서 Servlet을 이용해, Parameter Map을 만들어 낸다.  
이후 위와 같이 Controller의 인자로, ```paramMap(이름, 값)```으로 Parameter를 넘긴다.  
그럼 Controller은 ModelView의 Field에 미리 선언된 Model에 Entity를 주입한다.    

{% highlight java %}
public ModelView process(Map<String, String> paramMap) {
        List<Member> members = memberRepository.findAll();
        ModelView mv = new ModelView("members");
        mv.getModel().put("members", members);
        return mv;
}
{% endhighlight %}

이제 Controller에서 반환받은 ModelView에서 View name을 얻을 수 있다.  
View name은 View Resolver를 통해 View path로 변환되며, 아까와 같이 Render가 가능해진다.  

추가로, 이번에 쓰이는 Render 함수에는 Model까지 같이 넘겨주어야 한다.  
JSP 파일에서 View로 Render하기 위해서 값을 넘겨주어야 하기 때문이다.  
ModelView에서 Model을 꺼내서 넘겨주면, MyView에서 ```setAttribute()```를 진행한다.  

### Front Controller Version 4
[사진 첨부]

이번 버전에서는 ModelView를 없애고, 그냥 Model을 Parameter로 같이 넘겨보자.  
Front Controller에서 Parameter Map과 함께, Model도 생성을 해서 만들도록 한다.  
```paramMap```은 요청 Parameter에서 값을 빼오며, Model은 빈 채로 넘겨준다.  

그럼 각 Controller에서 ```paramMap```과 ```model```을 넘겨받은 뒤,  
```paramMap```의 값을 통해 Entity를 만들어 ```model.put``` 으로 넘긴다.  
그리고 반환 값은, 이전과 같이 View name을 반환한다.  
View Resolver을 통해 똑같이 View path로 변환하여 MyView에 넘겨주면,  
```render(model, req, resp)```을 통해, View를 Render해주며 마무리한다.  

### Front Controller Version 5 : Adpater
[사진 첨부]

이번 버전은 다른 버전들에 마지막으로 어댑터 패턴을 추가한다.  
지금까지 우리가 개발한 Front Controller은 한 가지 방식의 Controller만 쓸 수 있었다.  
만약 어떤 개발자는 Version 3을 쓰고 싶고 어떤 개발자는 Version 4를 쓰고 싶다면?  

그럴 때 필요한 것이 __Handler Adapter!__  
마치 110V, 220V 전기 콘센트를 모두 쓸 수 있도록 변환해주는 장치와 같이  
Handler Adapter에서 Controller을 정하도록 하고, 원래의 Controller 동작은 그대로 진행시킨다.  

1. Handler Adapter
  - Handler Adapter은 Controller가 지원 가능한지 판단하는 Supports 함수가 필요하다.

{% highlight java %}
public boolean supports(Object handler){
    return (handler instance of ControllerV3);
    // 내가 주입한 Handler(Controller)이 V3에 지원 가능한가?
}
{% endhighlight %} 
  - 그리고 Handle 함수를 통해 원래 Controller의 Process 함수를 구현해준다.
    - 반환 값은 ModelView를 반환하도록 한다.

2. Front Controller
  - ```HandlerMappingMap```에 사용할 Handler(Controller) 목록을 저장한다.
    - 들어온 요청 URI를 Key로 Controller을 찾아서 쓸 수 있도록 한다.
  - ```HandlerAdapters```에 사용할 Handler Adapter 목록을 저장한다.
    - 고른 Handler에 맞게 끼울 수 있는 Adapter이 있는지 For Loop으로 찾아야 한다.  
    - 앞서 선언한 Supports 함수와 For loop을 통해 찾을 수 있다.
    - 찾았다면, Adapter에 Handler를 넘겨 Handle 함수를 실행해 ModelView를 반환받는다.  

이후 View를 Resolve하고 Render하는 과정은 앞의 Version 들과 동일하다.
  
사실 위의 모든 과정들은, Spring MVC의 구조를 이해하기 위한 밑거름이다.  
Spring MVC에서 제공하는 모든 기능들은 모두 위의 구조와 동일하기 때문이다.  

> - 사진 및 자료 출처 : [김영한의 스프링 MVC 1편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)