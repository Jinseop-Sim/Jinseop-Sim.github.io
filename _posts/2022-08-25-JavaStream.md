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

그리고 Stream과 함께 람다(Lambda) 함수를 사용할 수 있음이 특징이다.  

### Stream의 처리 과정
Stream의 처리 과정은 아래와 같다.

1. Stream 생성하기
2. Stream 내부 요소 가공하기(Filtering)
3. 결과 만들어 내기

### Stream 생성하기
Stream은 Collection 인스턴스를 통해 생성할 수도 있고, 직접 생성할 수도 있다.  

- 기본적인 Stream 생성  

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

- 메서드를 통한 Stream의 생성

{% highlight java %}
Stream<String> generatedStream = Stream.generate(() -> "a").limit(5);
// generate 메서드로도 Stream을 만들 수 있다.
// 하지만 Limit을 지정해주지 않으면 무한개의 문자열을 만들어 버리니 주의!

Stream<Integer> iteratedStream = Stream.iterate(30, n->n+2).limit(5);
// 이렇게 Python의 Range 함수와 비슷하게 사용할 수도 있다.
// 30, 32, 34, 36, 38의 형태가 저장되게 된다.
{% endhighlight %}

- 타입 별 Stream의 생성

{% highlight java %}
IntStream intStream = IntStream.range(1, 5); // (1,2,3,4)
LongStream longStream = LongStream.rangeClosed(1, 5); // (1,2,3,4,5)
// 위와 같이 기본 타입형의 Stream을 만들 수도 있다.
// 이 경우엔 Auto Boxing이 일어나지 않는다. Generic이 아니라는 것!

Stream<Integer> boxedIntStream = IntStream.range(1, 5).boxed();
// 필요하다면 Boxing을 함으로써 감쌀 수 있다.

DoubleStream doubles = new Random().doubles(3);
// 3개의 난수. 자바 8의 Random Class는
// 난수를 가지고 Int, Long, Double Stream을 만들어 낼 수 있다.

IntStream charStream = "Stream".chars();
// [83, 116, 114, 101, 97, 109]가 저장된다. ASCII Code이다.

Stream<String> stringStream = Pattern.compile(",").splitAsStream("Eric, Elena, Java");
// Delimeter인 ,를 기준으로 Split한다.
// 이렇게 Regex를 이용해 문자열을 자를 수 있다.

Stream<String> lineStream = Files.lines(Paths.get("file.txt"), Charset.forName("UTF-8));
// File을 읽어서 Stream으로 저장할 수 있다.
{% endhighlight %}

### Stream 가공하기