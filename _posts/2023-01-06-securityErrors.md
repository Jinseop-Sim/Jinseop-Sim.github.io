---
layout: post
title: "[Debug] 로그인 과정에서 발생한 오류들"
categories: ToyProject
tags: [devops]
author:
  - Jinseop Sim
---
토이 프로젝트를 진행하며, 홈페이지를 구현하는 프로젝트 였기에  
로그인 기능을 집어넣으려 시도했지만, 각종 오류를 맞딱드렸다.

그 과정에서 발생한 오류들을 정리해보려 한다.  

### 자격 증명에 실패하였습니다.
<img width="1040" alt="KakaoTalk_20230106_232449456" src="https://user-images.githubusercontent.com/71700079/211176320-32daf1a6-feb2-46ce-a9d8-7124436d7839.png">  

해당 오류는 Spring Security에서 옳지 않은 비밀번호를 입력했을 때 띄워준다.  
나의 경우에는, ```PasswordEncoder``` 때문에 문제가 발생했다.  

Spring Security의 검증 과정에는 ```UserDetails``` 객체를 생성하는 과정이 있다.  
해당 객체를 만들어 ```AuthenticationProvider```에게 다시 보내 인증을 진행하도록 한다.  

위 Code를 보면, ```createUserDetails``` 함수가 객체를 return 하고 있음을 볼 수 있다.  
```Builder``` 패턴을 이용해서 ```passowrd```를 설정해줌을 확인할 수 있는데,  
이 과정에서 Front 단에서 입력한 Password를 한번 더 ```Encode``` 해버려서 오류가 발생했다.  

애초에 내가 DB에 저장할 때 암호를 한 번 Encode 해서 저장을 하기 때문에,  
한번 더 Encode를 해서 객체를 생성하면, 두 번 Encoding 한 셈이 되어 버린다.  
해당 코드를 ```member.getPassword()``` 로 그냥 바꿔주면 정상적으로 작동하게 된다.  

### Cannot Pass a null GrantedAuthority collection
<img width="1261" alt="KakaoTalk_20230106_231227924" src="https://user-images.githubusercontent.com/71700079/211176364-b199173f-6eb6-46a5-bbaa-ee2a959350bf.png">  

해당 오류는, User 객체에 아무 권한이 설정이 되지 않아서 발생하는 오류이다.  

위 사진을 보면 ```User``` 객체 내부 코드를 확인해볼 수 있는데,  
마지막 줄에 ```this.authorities```를 설정해주고 있음을 확인할 수 있다.  
해당 ```Collection```에 아무것도 들어가지 않아서 오류가 발생하고 있음을 알 수 있다.  

<img width="875" alt="KakaoTalk_20230106_232304956" src="https://user-images.githubusercontent.com/71700079/211176757-fd43a8f0-02c6-45e6-a4ad-f9eb8b5725db.png">

이 오류 또한 자격 증명 오류와 마찬가지로 ```UserDetails``` 객체 생성 시점에 해결해야 한다.  
위의 코드에서 확인할 수 있듯이, ```GrantedAuthority``` 객체를 통해 권한을 주입하고 있다.  

내가 따로 ```Member```에서 Authority를 ```Enum``` 객체로 먼저 구현을 해놓았다.  
하지만 인증에 필요한 권한은 ```GrantedAuthority``` 타입이어야 하기 때문에,  
생성자를 통해서 ```GrantedAuthority```의 형태로 바꾸어 준다음 권한을 주입했다.  
