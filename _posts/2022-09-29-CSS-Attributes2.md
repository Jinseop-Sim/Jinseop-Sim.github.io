---
layout: post
title: "[PNUCSE Web] CSS Attributes - 2"
categories: HTML
tags: [frontend]
author:
  - Jinseop Sim
toc: true
---
> 해당 자료는 부산대학교 최성기 교수님의 웹 응용 프로그래밍 강의 자료입니다.  

저번 시간에 이어서 특수한 효과를 가진 Attribute 들을 알아보겠다.  

### Text Shadow
글자에 그림자를 넣을 수 있는 속성이다.
{% highlight html %}
<style type = "text/css">
h1 {
	text-shadow: -4px 4px 6px dimgrey;
	<!-- 순서대로 그림자 x위치, y위치, 크기, 색상이다.-->
	<!-- 음수도 가능하며, 텍스트가 영점이다.-->
	font-size: 400%;
}
</style>
{% endhighlight %}

### Radius
네모의 모서리를 둥글게 만들어주는 속성이다.  
속성에 들어가는 ```px```는, ```px```를 반지름으로 하는 원이 모서리에 들어갔다고 생각하면 된다.  
{% highlight html %}
<style type = "text/css">
div {
	border: 3px solid navy;
	background: lightcyan;
	border-radius: 15px;
}
</style>
{% endhighlight %}

### Color
색상 변경의 여러가지 속성이다.  
- RGB : ```##FF0000```와 같이 Hex 값을 이용해서 색을 조절한다.
- RGB function : ```rgb(R, G, B)```나, ```rgba(R, G, B, alpha)```를 이용해 조절한다.
  - ```alpha``` 값은 투명도를 의미한다.
- HSL : ```HSL(hue, saturation, lightness)```로 채도 명도 등을 조절할 수도 있다.

### Box Shadows
네모 뒤에 그림자를 넣을 수 있는 속성이다.
{% highlight html %}
<style type = "text/css">
div {
	width: 200px;
	height: 200px;
	background-color: plum;
	box-shadow: 25px 25px 50px dimgrey;
	<!-- Text Shadow와 동일한 순서의 속성이다.-->
}
</style>
{% endhighlight %}

### Color Gradient
색을 하나만 쓰는 것이 아닌, 여러 색을 함께 표현할 수 있다.  

- Prefix : 브라우저마다 접두사를 따로 붙일 수 있다.
  - ```webkit``` : Google, Safari 브라우저에 맞춰서 적용되는 속성이다.
  - ```moz``` : Firefox(mozila) 브라우저에 맞춰서 적용되는 속성이다.
  - ```ms``` : IE(Internet Explorer) 브라우저에 맞춰 적용되며, 보통 생략한다.
  - ```o``` : Opera 브라우저에 맞춰 적용된다.

#### Linear Gradient
선형의 그라데이션을 적용시킨다.  

{% highlight html %}
<style type = "text/css">
div {
	width: 200px;
	height: 200px;
	border: 3px solid navy;
	background: -webkit-gradient(
		linear, center top, center bottom,
		color-stop(15%, white), color-stop(50%, lightsteelblue), color-stop(75%, navy));
	background: -moz-linear-gradient(
		top center, white 15%, lightsteelblue 50%, navy 75%);
	background: linear-gradient(
		to bottom, white 15%, lightsteelblue 50%, navy 75%);
}
</style>
{% endhighlight %}

#### Radial Gradient
원형의 그라데이션을 적용시킨다.  

{% highlight html %}
<style type = "text/css">
div {
	width: 200px;
	height: 200px;
	border: 3px solid navy;
	background: -webkit-radial-gradient(center, yellow, red);
	background: -moz-radial-gradient(center, yellow, red);
	background: radial-gradient(center, yellow, red);
}
</style>
{% endhighlight %}

### Multiple Background
여러 사진을 동시에 배경으로 쓸 수 있다.  

{% highlight html %}
<style type = "text/css">
div.background {
	background-image: url(logo.png), url(ocean.png);
	background-position: bottom right, 100% center;
	<!-- 위에서 적은 Image의 순서대로 위치를 적용시킨다. -->
	background-origin: border-box, content-box; 
	<!-- 기준이 되는 Box model을 정한다. -->
	background-repeat: no-repeat, repeat;
	<!-- 반복 여부 -->
}
</style>
{% endhighlight %}

### Reflect
사진을 반전시킬 수 있는 기능이다.  
참고사항으로, ```webkit```만 사용이 가능하다!  

{% highlight html %}
<style type = "text/css">
img.below {
	-webkit-box-reflect: below 5px
	<!-- px값을 통해서 반전된 Image가 떨어진 정도를 조절한다. -->
		-webkit-gradient(
			linear, left top, left bottom,
			from(transparent), to(white));
	<!-- 이렇게 반전된 사진에 효과를 따로 줄 수도 있다. -->
}
</style>
{% endhighlight %}

### Border Image
Border로 테두리가 설정되어 있다면, Image를 테두리로 사용할 수 있다.  

{% highlight html %}
<style type = "text/css">
#stretch{
	border: 15px solid;
	border-image-source: url(border.png);
	border-image-repeat: stretch;
	border-image-slice: 80;
	<!-- Stretch는 이미지를 길게 늘어트려 테두리로 만든다. -->
	<!-- 해당 속성이 기본 속성이다. -->
}
#repeat {
	border: 15px solid;
	border-image-source: url(border.png);
	border-image-repeat: repeat;
	border-image-slice: 34%;

	<!-- 반복해서 테두리를 사진으로 채운다.-->
	<!-- Slice는 테두리 조각을 만들 때 Image를 Slice할 위치를 조절한다.-->
}
</style>
{% endhighlight %}

### Animation
Animation 속성을 통해서 컴포넌트를 움직이게 할 수 있다.  

#### Key Frames
Animation에 관련된 속성으로, 컴포넌트를 움직이게 할 수 있다.  

{% highlight html %}
<style type = "text/css">
img{
	position: relative; <!-- 움직일 Image의 위치를 조절한다.-->
	animation-name: movingImage 
	<!-- 아래에서 keyframe으로 생성한 animation의 이름 -->
	animation-timing-function: linear;
	<!-- 움직이는 Animation의 방향(선형, 원형..) -->
	animation-duration: 10s;
	<!-- 몇 초 동안 움직일 것인가? -->
	animation-delay: 1s;
	<!-- 다음 반복 까지의 시간 간격 -->
	animation-iteration-count: 2;
	<!-- 반복 횟수 -->
	animation-direction: alternate;
	<!-- Keyframe에서 생성한 방향의 진행 순서 -->
}
@keyframes movingImage
{
	0% {opacity: 0; left: 50px; top: 0px;}
	25% {opacity: 1; left: 0px; top: 50px;}
	50% {opacity: 0; left: 50px; top: 100px;}
	75% {opacity: 1; left: 100px; top: 50px;}
	100% {opacity: 0; left: 50px; top: 0px;}
}
</style>
{% endhighlight %}

#### Transition
특정 동작을 했을 때의 컴포넌트의 크기를 조절한다.  
{% highlight html %}
<style type = "text/css">
div{
	width: 100px;
	height: 100px;
	background: red;
	transition: width 2s, height 4s;
	<!-- 옆으로는 2초, 아래로는 4초 동안 늘린다. -->
}
div:hover{
	width: 300px;
	height: 300px
	<!-- 마우스 커서를 올리면 동작-->
}
</style>
{% endhighlight %}

#### Transform
특정 동작을 했을 때의 컴포넌트의 형태나 위치를 조절한다.  
{% highlight html %}
<style type = "text/css">
div{
	transform: translate(50px, 100px);
	<!-- 컴포넌트를 가로, 세로 만큼 옮긴다 -->
	transform: rotate(20deg);
	<!-- 컴포넌트를 각도만큼 회전시킨다. 음수는 반대로 회전 -->
	transform: scale(2, 3);
	<!-- 컴포넌트를 가로로 x배 세로로 y배 확장한다. -->
	<!-- 1보다 작은 소수는, 축소시킨다. -->
	transform: scaleX(2);
	transform: skew(20deg, 20deg);
	<!-- x축, y축 기준으로 해당 각도만큼 컴포넌트를 기울인다. -->
	transform: skewX(20deg);
	transform: matrix(1, -0.3, 0, 1, 0, 0);
	<!-- 순서대로 scaleX(), skewX(), Y(), scaleY, translateX(), translateY() 이다. -->
}
</style>
{% endhighlight %}

#### Bonus : 3D Transform
{% highlight html %}
<style type = "text/css">
div{
	transform: rotateY(150deg);
	transform: rotateX(150deg);
	transform: rotateZ(90deg);
	<!-- 해당 축을 기준으로 입체적으로 회전시킨다. -->
}
</style>
{% endhighlight %}
