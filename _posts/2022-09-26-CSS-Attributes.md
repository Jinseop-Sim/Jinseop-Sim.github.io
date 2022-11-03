---
layout: post
title: "[PNUCSE Web] CSS Attributes"
categories: HTML
tags: [frontend]
author:
  - Jinseop Sim
toc: true
---
> 해당 자료는 부산대학교 최성기 교수님의 웹 응용 프로그래밍 강의 자료입니다.  

이전 시간에는 CSS의 기본적인 사용법과 구조에 대해 알아보았다.  
이번엔 본격적으로 CSS의 다양한 속성에 대해 알아보자.  

### Background Attribute
{% highlight html %}
<head>
	<style type = "text/css">
		body { background-image : url;
			   background-position : bottom right;
			   background-repeat : no-repeat;
			   background-attachment : fixed;
			   background-color : lightgrey;}
	</style>
</head>
{% endhighlight %}  

- ```background-image``` : 경로로 지정한 이미지를 띄운다.
- ```background-position``` : 이미지의 위치를 지정한다.
	- ```bottom```, ```right```, ```center``` 등 다양한 위치가 있다.
- ```background-repeat``` : 이미지를 반복 출력해서 배경을 다 채울 것인가? 한 번만 출력할 것인가?
	- 한 번만 출력하도록 ```no-repeat```를 사용할 수 있는데, 이러면 ```Color```와 동시에 표현이 가능하다.
	- ```repeat-x```와 ```repeat-y``` 속성을 이용해서 반복 위치 조절도 가능하다.
- ```background-attachment``` : 그림을 출력을 하고, Scroll을 내려가도 그림이 따라다니도록 한다.
	- 기본 속성은 ```scroll``` 이다. 원래대로 그냥 페이지가 움직인다.
	- ```fixed``` 속성을 이용하면, Scroll이 내려가도 그림이 따라다니게 할 수 있다.
- ```text-indent``` : 들여쓰기를 할 수 있다.

### Element Dimension  

- ```overflow``` : 글의 width가 좁고, 글이 너무 길 때 조절이 가능한 속성이다.
	- 기본 값은 ```visible```로 글이 길어져도 영역을 벗어나 다 출력을 해준다.
	- ```scroll``` 속성은, 글이 너무 길어졌을 때 Scroll이 생긴다.
	- ```hidden``` 속성은, 글이 너무 길어지면 길어진 부분이 잘려서 보이지 않게 된다.
	- ```auto``` 속성은, Scroll의 상위 호환 버전이다.
	- ```overflow-x(y)``` : 상, 하의 Scroll을 따로 설정하고 싶을 떄 사용하는 속성이다.
- ```text-align``` : Text 정렬에 대한 설정이다.

### Box Model
```Paragraph```든 ```div```든 어떤 컴포넌트가 존재할 때, 컴포넌트 주변의 범위를 말한다.  
아래의 세 범위를 구분하는 것은 상당히 중요하다!  

- ```margin``` : 컴포넌트의 가장 바깥 범위이다.
    - 다른 컴포넌트와의 거리를 보통 의미한다.
    - ```left, right, top, bottom``` 4가지의 속성으로 제어가 가능하다.
    - ```margin : 0.5em 0.5em 0.5em 1em``` 과 같이 하나의 Margin으로도 제어가 가능하다.
- ```border``` : Margin 안쪽의 테두리같은 개념이다.
    - Border은 매우 다양한 속성이 존재한다.
- ```padding``` : 가장 안쪽의 범위이며, 내가 패딩을 입는다고 생각하면 쉽다.

### Text Flows
- ```float```
  - 원래 HTML은 기본적으로 위에서 아래로 컴포넌트들이 배치가 되도록 되어있다.  
  - 하지만 ```Float``` 속성을 이용하면 이를 수평으로 배치가 가능해진다!   
  - 이는 곧 컴포넌트 배치에 있어서 유연성이 높아짐을 의미한다.  

{% highlight html %}
<head>
	<style type = "text/css">
		.floated { float: right;
				    background-color: LightGrey;
		            font-size: 1.5em;
		            text-align: center
		            width: 50%; }
	</style>
</head>
<body>
	<h1 class = "floated"> I'm floated! </h1>
	<p> It's just floating test </p>
</body> <!-- Paragraph의 오른쪽에 해당 class가 배치된다. -->
{% endhighlight %}  

- ```text-align```
  - ```left, right, center``` 3가지가 존재한다.
  - ```justify``` 라는 속성도 존재하는데, 이는 양쪽 정렬을 해주는 속성이다.

### Media Type
사용하는 기기에 따라, 표현되어야 하는 웹도 바뀌게 되는데,   
```@media``` 라는 Annotation을 통해서 원하는 용도에 맞는 Media Type을 적용시킬 수 있다.  
- ```all``` : 속성을 모든 매체에 적용시킨다.
- ```print``` : 실제 출력물을 확인하는 웹페이지를 띄울 때 사용하는 Type이다.
- ```handheld``` : 모바일 기기들에 대해 사용하는 Type이다.
- ```braille``` : 흔히 아는 브라유 점자이다.

### Media Query
사용하는 기기에 따라 화면의 크기는 분명 다 다르다.  
그 기기의 화면 크기에 맞게 Webpage 자체에서 조절을 하도록 할 수 있는데, 그게 바로 __Media Query__ 이다.  

### Drop-Down Menu
마우스 커서를 올렸을 때, Menu list가 나오게 되는 Drop-Down Menu는 어떻게 구현할까?  
{% highlight html %}
<head>
	<style type = "text/css">
		nav ul { display: none;
		         list-style: none;
				 margin: 0;
				 padding: 0;
        }
		nav:hover ul { display: block;}
	</style>
</head>
<body>
	<nav> Menu
		<ul>
			<li>Home</li>
			<li>News</li>
			<li>Articles</li>
			<li>Blog</li>
			<li>Contact</li>
		</ul>
	</nav>
</body>
{% endhighlight %} 