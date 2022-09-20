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
	<a href = "#EPL"></a> <!-- 클릭 시 EPL Header로 이동!-->
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
  - 이외에는 CSS Style 태그를 통해 여러가지로 꾸밀 수 있다.
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

- Form의 속성
  - ```method``` : 해당 태그의 HTTP Method를 정할 수 있다.
  - ```action``` : HTTP Method에 따라 요청을 보낼 주소를 정할 수 있다.
- Input : 입력을 받을 수 있는 Form으로 다양한 Type이 존재한다.
  - 기본적으로 아래와 같이 Form 태그에 Nest하게 사용한다.
{% highlight cpp %}
<form method = "post" action = "http://www.deitei.com">
	<input type = "text" name = "name" maxlength = "20">
	<input type = "submit" value = "전송"
	<p>
		<label>Name : <!-- 앞에 이름을 달 수 있다.-->
			<input name = "name" type = "text" mexlength = "20">
		</label>
	</p>
</form>
{% endhighlight %}

  - Text : 기본적으로 글을 입력받는 타입이다.
    - Value : 기본 값을 명시해준다.
	- Placeholder : 기본 값이 아닌, 회색 글씨로 배경에 글자가 적힌다.
	  - 주로 안내용으로 많이 사용한다.
  - Submit : 해당 Form에 입력된 데이터를 ```action```의 주소로 전송한다. (Button)
  - Reset : Form에 입력되어있는 글들을 모두 지운다. (Button)
  - Button : Submit, Reset 외에도 버튼의 기능을 할 수 있는 타입이다.
  - Hidden : 웹 페이지를 렌더링 할 때에 사용자에게 보이지 않는 입력 필드이다.
    - 보통 사용자가 변경해서는 안되는 Data를 함께 보낼 때 유용하게 사용한다.
	- DB의 Record를 저장하거나, 고유한 보안 토큰 등을 보낼 때 주로 사용한다.

  - 체크할 수 있는 형태
    - Checkbox : 선택 가능한 Checkbox가 생긴다. 중복 선택이 가능하다.
	- Radio : 선택 가능한 원형의 Checkbox가 생긴다. 중복 선택 불가능!
	- Select : Select는 특별하게 태그로 사용한다.
	  - Option 태그를 Nest하게 넣어서 선택 가능한 값을 리스트화 한다.  

  - 특수한 형태
    - Color : 색상을 선택할 수 있는 색상표를 띄운다.
    - Date : 달력 UI가 나타나며, 날짜를 형태에 맞게 선택이 가능하다.
	  - Datetime-Local : 시간까지 선택이 가능한 Type이다.
	- Email : 이메일의 형태를 체크하며, ```@```가 없을 시 경고를 띄운다.
	- Image : ```img``` 태그와 같이 이미지를 출력할 수 있다.
	- Number : 수를 Select Box 형태로 고를 수 있다.
	  - Min, Max로 최소 최대값을 정한다.
	  - Range : 게이지의 형태로 수를 고를 수 있다.
	- Tel : 전화번호의 형태를 검증하며, 정규식을 이용해 검증한다.
	  - Pattern 속성을 통해 정규식을 걸어줄 수 있다.