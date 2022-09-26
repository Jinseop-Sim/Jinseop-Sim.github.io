---
layout: post
title: "[PNUCSE Web] CSS Attributes"
categories: HTML
tags: [frontend]
author:
  - Jinseop Sim
toc: true
---
해당 글은 부산대학교 최성기 교수님의 웹 응용프로그래밍 강의를 바탕으로 작성했습니다.  

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