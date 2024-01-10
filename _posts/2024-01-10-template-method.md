---
layout: post
title: "Template method pattern?"
categories: Tech
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
이번 패턴은 __Template method__ 패턴이다.  

### Template method?
Template method 패턴을 간단하게 정의하면 아래와 같다.  

> 코드 중복을 피하기 위해 템플릿을 만드는 패턴.

템플릿을 만든다는 것은 한국말로 양식을 만드는 것과 같다.  
우리는 보고서같은 문서를 작성할 때 양식을 기반으로 쓰곤 한다.  
양식을 사용하는 이유는 해당 부분이 공통 부분이기 때문이다.  
공통된 부분은 굳이 각각 학생들이 직접 만들거나 칠 필요가 없다!  

따라서 편의성을 위해 우리는 양식, 즉 템플릿을 사용한다.  

### 사용 이유
위에서 말했듯이 굳이 직접 만들 필요가 없기 때문에 사용한다.  
예를 들어 아래와 같은 클래스와 메서드가 있다고 생각해보자.  

<img width="564" alt="스크린샷 2024-01-10 오후 2 41 55" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/a95bbc0c-2b87-4dad-b36b-c3d3628c2b53">  

파일을 읽고, 수를 계산해서 결과를 내는 임시 코드이다.  
임의로 코드 흐름만 보기 위해서 내용은 따로 구현하지 않았다.  
위와 같이 코드를 짰다고 했을 때, 곱셈 연산을 추가하고 싶다고 해보자.  

그럼 아래와 같이 곱셈 연산을 하는 새 클래스를 작성해야 할 것이다.  
하지만 지금 파일을 불러오고 출력하는 과정이 중복됨을 볼 수 있다.  
굳이 중복되는 코드들을 여러번 쳐서 코드 길이를 늘려야 할까?  
이는 복잡도만 증가하게 하고 가독성도 떨어트리기만 할 것이다.  

<img width="646" alt="스크린샷 2024-01-10 오후 2 43 07" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/e6359d9e-c414-42db-b37a-b63f657853dd">

그럴 때 필요한 것이 바로 Template method 패턴!  

### 구현
아래와 같이 추상 클래스로 Template method를 선언하도록 한다.  
아래의 예시에서는 ```process()``` 함수가 Template이 되는 것이다.  

<img width="621" alt="스크린샷 2024-01-10 오후 2 45 10" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/6fc27ed5-1cbb-427c-8935-6bbbbd2cb4a2">

그럼 이제 우리는 추상 클래스를 상속받아 사용하기만 하면 된다!  
아래와 같이 ```calculate()``` 함수를 오버라이딩하여 계산 방식만 바꿔준다.  

<img width="724" alt="스크린샷 2024-01-10 오후 2 46 11" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/ad7b68cc-83a9-4d94-9df6-f8afb8606eaf">  

그 결과 아래와 같이 실제로 사용할 수 있는 것이다.  
중복되는 코드를 부모 클래스로 올려버리고, 동작 방식만 교체했다.  
하지만 사용자는 ```process()```로 동일하게 Template만 사용한다.  

<img width="575" alt="스크린샷 2024-01-10 오후 2 47 07" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/54f88d8d-3777-44a5-b7ed-6d3bba609313">  

둘의 계산 방식이 다르게 성공적으로 진행되었음을 확인할 수 있다.  
사용자들은 주어진 Template method만을 호출하면 된다.  
개발자 입장에서는 ```OCP```를 지킬 수 있고, 코드 중복이 줄었다.  
즉, 유지 보수에 있어서 굉장히 유리하게 작성한 것이다.  
