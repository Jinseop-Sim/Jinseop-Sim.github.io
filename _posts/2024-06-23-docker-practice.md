---
layout: post
title: "Docker를 적용해보자"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
실제 기업에서도 협업에 있어 ```Docker```는 꽤나 중요한 역할을 한다.  
OS에 구분 없이 단지 ```Container```의 실행만으로 어플리케이션 구동이 가능하다.  
즉, 가상화된 공간에 의해 이식성이 무한하다는 의미가 되겠다.  

기존에는 이론적으로만 알고 있었지만, 이번 기회에 실제로 좀 적용을 해보려한다.  

### Docker의 기본
Docker는 기본적으로 설치한 뒤, ```zsh``` 환경에서 조작이 가능하다.  
매우 편리한 점은 ```Docker Desktop```을 통해 GUI 조작도 가능하다는 점이다.  
하지만 나는 ```zsh``` 내에서 직접 명령어를 통해 조작하려 한다.  

기본적으로 Docker의 구성은 ```image```와 ```container```이다.  
누군가, 혹은 내가 구성한 ```image```를 받아와 실행시키면 ```container```가 되는 것이다.  

### Docker의 실제 적용
#### Dockerfile
실제로 프로젝트에서 ```image```를 만드는 경우, ```Dockerfile```이 사용된다.  
아래의 사진이 그 예시이다.  

<img width="421" alt="스크린샷 2024-06-23 오후 3 26 38" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/4a9157b1-a1d6-475a-a7e8-bd2ec1d6e029">  

실제로 만든 Springboot 프로젝트를 ```image```화 하기 위해 명령어들을 적어 놓은 것이다.  
```docker build``` 명령어를 실행히면, 해당 명령어들이 수행되어 ```image```가 build 된다.  
명령어들에 대해 아래로 간단하게 알아보도록 하자.  
- ```FROM``` : 베이스 이미지를 지정하도록 한다.
  - 현재 내 프로젝트는 ```openjdk17```을 베이스 이미지로 한다.
- ```ARG``` : build 시 사용되는 매개변수를 의미한다.
  - 나는 image를 실행시키면, ```jarfile```이 실행되어야 하므로, 해당 파일을 변수로 넣었다.
- ```ADD / COPY``` : 이미지 생성 시 파일을 추가/복사하는 명령어이다.
  - 예를 들어, ```jarfile```을 실행시키려하면 기본적으로 container 내에는 파일이 없다.
  - 따라서 ```ADD / COPY``` 명령어를 통해 파일을 생성해주어야 하는 것이다.
  - ```ADD``` 명령어는 ```COPY```와 기본 동작은 같지만, 더 많은 기능을 지원한다.
    - 단순히 container내에 파일을 옮기려는 것이면 ```COPY```를 사용하는게 좋다.
- ```ENTRYPOINT, CMD``` : 컨테이너를 시작할 때 수행할 일련의 명령어를 지정한다.
  - ```jarfile```의 실행 명령어는 ```java -jar ~~~.jar```이므로, 사진과 같이 지정한다.
- ```EXPOSE``` : 이미지가 실행될 때, 실행될 포트를 지정하는 명령어이다.
- ```RUN``` : 이미지를 지정하면서 실행할 명령을 지정한다.
- ```WORKDIR``` : 컨테이너 작업 디렉토리를 지정한다.
- ```MAINTAINER, LABEL``` : 이미지의 작성자와 라벨을 명시하는 명령어이다.
- ```ENV``` : 컨테이너의 환경 변수를 지정한다.
- ```VOLUME``` : container의 볼륨을 지정한다.
- ```USER``` : 컨테이너의 사용자를 지정한다.

#### Docker-compose
하지만, 실제로 서비스를 개발할 때에는 단순히 ```jarfile```만 사용되는 것이 아니다.  
```DB```나 어떤 ```Redis```와 같은 부가적인 다양한 서비스들이 추가로 붙게 된다.  
나의 경우에도 아래의 사진과 같이 ```DB```가 없어 이미지가 실행되지 못했다.  

<img width="1286" alt="스크린샷 2024-06-23 오후 3 38 50" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/aa900aca-9f48-4d6e-9afd-1b6ef4a97c5e">  

즉, 성공적으로 서버를 돌리기 위해서는 부가적인 서비스들도 모두 __컨테이너화__ 되어야 한다!  
그럼 어떻게 한 번에 필요한 컨테이너들을 모두 실행시킬 수 있을까?  
그럴 때 필요한 것이 바로 ```docker-compose.yml``` 파일이 되겠다.  

아래는 내가 직접 ```SpringBoot``` 프로젝트에 작성한 ```docker-compose``` 파일이다.  
아래의 사진에서 볼 수 있듯이, 여러개의 컨테이너를 동시에 실행시킬 수 있도록 이미지를 구성한다.  

<img width="326" alt="스크린샷 2024-06-24 오후 5 20 20" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/38c8210e-5618-401b-97c5-c51ab738a6b8">  

하지만 현재 제대로 인식을 하지 못하고 오류가 발생하고 있는데, 빠른 해결이 필요할 것 같다.  
