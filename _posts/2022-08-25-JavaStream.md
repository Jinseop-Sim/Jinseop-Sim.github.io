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
// 비어있는 Stream에 Builder를 통해 요소를 추가할 수도 있다.

Stream<String> stringStream = Stream.of("Hackers", "are", "always", "here");
// 이렇게 간단하게 Of Method를 통해서도 Stream 선언이 가능하다.
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

#### Parallel Stream
Stream을 병렬 작업으로 쓰레드처럼 처리 가능한 Parallel Stream이 존재한다.  
```.stream()``` 대신에 ```.parallelStream()```을 사용하면, 병렬 스트림을 사용할 수 있다.  

{% highlight java %}
IntStream intStream = IntStream.rangeClosed(1, 10);
        intStream.parallel()
                .forEach(System.out::println);
// 결과로 출력되는 1 ~ 10까지 수의 순서는 랜덤하다.
// 왜? 병렬 작업으로 처리되기 때문이다!

intStream.sequential(); // 이렇게 원래대로 돌릴 수 있다.
{% endhighlight %}

병렬 작업을 하므로, 작업의 효율에 있어서 이득이 있을 수 있지만, 반드시 좋은 것은 아니다.  

### Stream 가공하기
- Stream Concatenate

{% highlight java %}
Stream<String> st1 = Stream.of("Alabama", "Texas", "New york");
Stream<String> st2 = Stream.of("New Jersy", "Misissipi", "Manhattan");
Stream<String> concat = Stream.concat(st1, st2);
// 결과는 두 Stream이 합쳐진 하나의 Stream이 된다.
{% endhighlight %}  

아래로는 이렇게 __Product__ 라는 Class를 만들어 진행하도록 하겠습니다.  

{% highlight java %}
Product p1 = new Product("Chess board", 10L, 10000L);
Product p2 = new Product("Straws", 100L, 100L);
Product p3 = new Product("Hell-o-Kitty", 20L, 20000L);
Product p4 = new Product("Piece of Chess Knight", 30L, 5000L);
Product p5 = new Product("Chess champs' trophy", 1L, 0L);

List<Product> productList = new ArrayList<>();
productList.add(p1); productList.add(p2); productList.add(p3);
productList.add(p4); productList.add(p5);
{% endhightlight %}  

- Stream Filtering

{% highlight java %}
productList.stream()
	       .filter(product -> product.getName().contains("Chess"))
		   .forEach(p -> System.out.println(p));
// 이렇게 Chess가 들어간 이름의 상품만 출력하도록 Filtering이 가능하다.
// 다양한 조건을 걸 수 있다.

IntStream intStream = IntStream.rangeClosed(1, 20);
intStream.filter(num -> num % 3 == 0)
         .forEach(System.out::println);
// 이와 같이 수학적 연산 조건을 걸 수도 있다.
{% endhighlight %}

- Stream Mapping

{% highlight java %}
LongStream longStream = LongStream.rangeClosed(1, 20);
longStream.map(num -> num * 10)
          .forEach(System.out::println);
// 이렇게 Lambda 식을 통해 Stream 내의 값을 바꿔 줄 수 있다.

productList.stream()
           .map(Product::getName) // 이렇게 객체 내의 특정 값을 빼올 수도 있으며,
		   .map(String::toUpperCase) // 이렇게 모두 대문자로 바꿀 수도 있다.
           .forEach(System.out::println);
{% endhighlight %}

- Flat Mapping ?
```map```과 다른 ```flatMap``` 메서드는, 고차원 배열을 1차원으로 줄여준다.  
또한 ```Stream<Generic>``` 의 형태로 반환되는 ```map```과 달리, 기본형으로 반환을 해줄 수 있다.  

{% highlight java %}
List<List<Long>> list = Arrays.asList(Arrays.asList(1L, 2L, 3L, 4L, 5L),
                                      Arrays.asList(1L, 2L, 3L, 4L, 5L));
List<Long> collect = list.stream()
           .flatMap(Collection::stream)
           .collect(Collectors.toList());
// 위 두 코드를 통해서 Flattening 시키는 작업을 볼 수 있다.

productList.stream()
           .flatMapToLong(product -> LongStream.of(product.getPrice()))
           .average();
// 객체는 이와 같이 기본형 Stream으로 바꿔 수학적 계산을 할 수 있다.
{% endhighlight %}

- Stream sorting

{% highlight java %}
productList.stream()
           .map(Product::getPrice)
           .sorted(Comparator.reverseOrder())
           .forEach(System.out::println);
// Sorted를 통해 Stream을 오름차순 정렬할 수 있다.
// Comparator를 통해 역순 등의 조건을 걸 수도 있으며,

productList.stream()
           .map(Product::getName)
           .sorted((s1, s2) -> s2.length() - s1.length())
           .forEach(System.out::println);
// 이렇게 Lambda 식을 이용해 조건을 걸 수도 있다.
// 위 코드는 길이가 긴 순서로 정렬을 한 것이다.
{% endhighlight %}

### Stream 결과 도출
- Primitive Type 연산
```flatMap``` 에서도 확인했듯이, 기본형 타입은 연산 메서드가 이용 가능하다.  

{% highlight java %}
OptionalDouble average = IntStream.of(1, 2, 3, 4, 5).average();
// 이렇게 기본형 타입의 Stream은 count, sum, average, min, max 등의 연산이 가능하다.
// 그 중 Avg, Min, Max는 Optional로 반환 타입이 정해진다.
// 왜냐하면 count와 sum은 비어있으면 0이지만, 나머지는 표현이 불가능하기 때문이다.
{% endhighlight %}

- Stream Reduce
```reduce```를 통한 연산은 기본형 타입이 아니더라도 가능하다.  
```reduce```는 총 3가지의 Parameter로 구성되어 있다.  

1. Identity : 연산의 초기 값을 설정한다.
2. Accumulator : 연산 로직이 들어간다.
3. Combiner : Parallel 스트림에서 나눠진 계산 값을 하나로 합친다.

{% highlight java %}
Stream<Integer> nums = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
Optional<Integer> sum = nums.reduce((x, y) -> x + y);
sum.ifPresent(System.out::println);
// 이 경우에는, 합이 존재하는 경우 1 ~ 10까지의 합을 출력한다.
// Accumulator만 존재하는 경우이다. 

Stream<Integer> nums = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
Optional<Integer> sum = nums.reduce(Integer::sum);
sum.ifPresent(System.out::println);
// 이 또한 같은 결과를 출력한다.

Stream<Integer> nums = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
Optional<Integer> sum = nums.reduce(10, (x, y) -> x + y);
sum.ifPresent(System.out::println);
// 이 땐 Identity 까지 추가된 경우로, 65를 출력하게 된다.

Stream<Integer> nums = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
Integer sum = nums.parallel()
                  .reduce(10,
                        Integer::sum,
                        (a, b) -> a+b);
// 이제 Combiner까지 등장한 경우이다.
// 병렬이기 때문에 위의 동작들과는 다르게 11 + 12 + 13.. 의 과정이 진행이 된다.
// 중간 결과들의 합산이라고 생각하면 된다.
{% endhighlight %}

- Stream Collecting
연산이 아닌, Stream의 형태를 바꿀 수 있는 종료 메서드이다.  
```Collectors``` 타입의 인자를 받아서 처리를 한다.  

{% highlight java %}
List<String> collect = productList.stream()
                .map(Product::getName)
                .collect(Collectors.toList());
// toList() 메서드를 통해서 Stream가 List로 반환되는 것을 볼 수 있다.

String collect = productList.stream()
                .map(Product::getName)
                .collect(Collectors.joining());
// joining() 메서드는 String으로 반환되는 것을 볼 수 있는데,
// List가 아닌 하나의 문자열로 싹 합쳐서 반환을 해준다.
{% endhighlight %}  

- Stream Matching
Matching의 반환 타입은 Boolean 타입으로, 존재 여부를 알린다.  

- ```anyMatch``` : 하나라도 있으면 ```True```를 반환한다.
- ```allMatch``` : 모두 조건에 해당되어야 ```True```를 반환한다.
- ```noneMatch``` : 모두 조건을 만족하지 못해야 ```True```를 반환한다.

{% highlight java %}
productList.stream()
                .map(Product::getName)
                .anyMatch(name -> name.contains("Chess"));
// 이는 True를 반환하게 된다.
{% endhighlight %}  