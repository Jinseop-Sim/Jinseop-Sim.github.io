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

- Internal Link : 글 내부에서 원하는 위치로 이동하는 Link이다.
{% highlight html %}
<p>
	<a href = "#EPL"></a>
	<a href = "#Bundesliga"></a>
</p>
<hr/>
<h2 id = "EPL"> EPL </h2>
<h2 id = "Bundesliga"> Bundesliga </h2>
{% endhighlight %}  

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
<!--
Coffee
	 Hot black drink
Milk
	 White cold drink
-->
{% endhighlight %}  

### Table
HTML에서는 ```<Table>``` 태그를 통해 표를 정의한다.  

- Table의 기본 태그
  - ```thead``` : 항상 Table의 상단에 위치한다.
  - ```tbody``` : 항상 Table의 중앙에 위치한다.
  - ```tfooter``` : 항상 Table의 바닥에 위치한다.
    - 위의 3가지 태그는 굳이 삽입하지 않아도 표를 만들 수 있다.  
{% highlight html %}
<table>
	<thead>
		<tr>
			<th>Fruit</th>
			<th>Price</th>
		</tr>
	</thead>
	<tfooter>
		<tr>
			<th>Total</th>
			<th>$3.75</th>
		</tr>
	</tfooter>
	<tbody>
		<tr>
			<td>Apple</td>
			<td>$3.75</td>
		</tr>
	</tbody>
</table>
{% endhighlight %}  

- Table의 속성
  - ```border``` : Table의 전체 테두리를 만들 수 있다.
  - ```rowspan(colspan)``` : Table의 행(열)을 병합한다.
{% highlight html %}
<table>
	<tr>
		<th rowspan = "4">
			<img src = "camel.png" alt = "Picture of Blue Camel">
		</th> <!-- Merge 4 Row -->
		<th colspan = "4">
			<strong>Camelid Comparison</strong><br>
			Approximate as of 6/2011
		</th> <!-- Merge 4 Column -->
	</tr>
	<tr>
		<th># of humps</th>
		<th>Indigenous region</th>
		<th>Spits?</th>
		<th>Produces wool?</th>
	</tr>
</table>
{% endhighlight %}  

### Form
HTML은 다양한 Type의 입력을 받을 수 있는 Form 태그가 존재한다.  

- Input