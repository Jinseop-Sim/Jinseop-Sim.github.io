---
layout: post
title: "[PNUCSE 2022] HTML Basic"
categories: HTML
tags: [frontend]
author:
  - Jinseop Sim
toc: true
---
이 강의는 부산대학교 최성기 교수님의 웹 응용 프로그래밍 강의 자료입니다.  
HTML의 기본 요소들에 대해서 간략하게 알아보자.  

### HTML의 기본 구조
{% highlight html %}
<!DOCTYPE HTML>
<!-- 해당 문서가 HTML TYPE임을 지정한다 -->
<html>
	<head>
		<meta chraset = "UTF-8"> <!-- Meta 정보 및 부가정보-->
		<title> Welcome! </title>
	</head>
	<body>
		<p> Welcome to HTML5! </p> <!-- 문단 Paragraph -->
		<img src = "hello.png"> <!-- image 출력문! -->
		<hr> <!--Horizontal Rule -->
	</body>
</html>
{% endhighlight %}

### Link
HTML에서는 태그를 통한 Hyperlink 기능을 제공하고 있다.  
- Text Link : 일반적으로 텍스트에 링크를 거는 동작.
{% highlight html %}
<p>
	<a href = "links.html">여기로 이동하십쇼!</a>
</p>
{% endhighlight %}  

- Image Link : 이미지에도 링크를 걸 수 있다.
{% highlight html %}
<p>
	<a href = "links.html">
		<img src = "buttons/links.jpg" alt = "Links">
		<!-- ALT 속성은 해당 이미지 출력이 불가할 때의 대체 이미지이다.-->
	</a>
</p>
{% endhighlight %}  

- Internal Link

### HTML Entity
HTML에서는 특수문자를 사용하기 위한 특수한 Code가 존재한다.  

- 일반 특수 문자
  - ```&``` : &amp;
  - ```'``` : &apos;
  - ```>``` : &gt;(&lt;)
  - ```"``` : &quot;
- 특수 특수 문자
  - 공백 : &nbsp;
	- Non-breaking space
	- 일반적으로 공백은 몇개를 집어넣어도 1개로 축약된다.
	- 하지만 이 특수문자를 넣으면 공백을 여러칸 넣을 수 있다.
  - Copyright : &copy;
  - em dash : &mdash;
  - en dash : &ndash;
  - 1/4(fraction) : &frac14;
  - 1/2(fraction): &frac12;s

### List
HTML에서는 List를 태그를 통해 표현한다.  
- UL : Unordered List, 그냥 점으로 표시되는 List이다.
- OL : Ordered List, 순서가 숫자로 표시되는 List이다.
- DL : Descriptive List, 특이하게 직접 태그를 정의할 수 있는 List이다.
{% highlight html %}
<dl>
	<dt>Coffee</dt><dd>Hot black drink</dd>
	<dt>Milk</dt><dd>White cold drink</dd>
</dl>
{% endhighlight %}  

### Table
HTML에서는 ```<Table>``` 태그를 통해 표를 정의한다.
{% highlight html %}
<table>
	<thead>
	</thead>
	<tbody>
	</tbody>
	<tfooter>
	</tfooter>
</table>
{% endhighlight %}  
### Form