---
layout: post
title: "[Java] Stream?"
categories: Java
tags: [Java]
author:
  - Jinseop Sim
toc: true
---
이번엔 Java에 존재하는 고급 기능 중 하나인 __Stream__ 에 대해 알아보자.  
Stream은 Java 8 부터 등장하게 된 편의 기능이다.  

원래는 Collection이나 배열을 다루기 위해선, Iterator 구문을 돌렸어야 했다.  
하지만 Code가 너무 길어져서, Stream이라는 편의 메서드가 등장하게 된다.  
간편하게 메서드 체인을 통해서 Loop을 돌리고 원하는 결과를 생성할 수 있다.  

그리고 또 하나의 장점은 간단하게 병렬처리가 가능하다는 점이다.  
그 말인 즉슨, 하나의 작업을 둘 이상의 작업으로 나눠 동시 진행이 가능하다는 것이다.  

### Stream의 처리 과정
Stream의 처리 과정은 아래와 같다.

1. Stream 생성하기
2. Stream 내부 요소 가공하기(Filtering)
3. 결과 만들어 내기

### Stream 생성하기
Stream은 Collection 인스턴스를 통해 생성할 수도 있고, 직접 생성할 수도 있다.  

{% highlight java %}
String[] arr = new String[]{"a", "b", "c"};
Stream<String> stream = Arrays.stream(arr);
// 배열을 Stream으로 순회하기.

List<String> list = Arrays.asList("a", "b", "c");
Stream<String> stream = list.stream();
// Collection을 Stream으로 순회하기. 물론 Set, ArrayList 등도 가능하다.

public Stream<String> streamOf(List<String> list){
	return list = null || list.isEmpty()
	? Stream.empty()
	: list.stream();
}
// 위 메서드와 같이 요소가 없을 경우에, 빈 Stream도 생성이 가능하다.

Stream<String> builderStream = Stream.<String>builder().add("a").add("b")
									.build();
// 비어있는 Stream에 요소를 추가할 수도 있다.
{% endhighlight %}