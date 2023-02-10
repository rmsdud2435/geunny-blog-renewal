---
layout: default
title: "@Transaction 이해하기"
parent: SpringBoot
grand_parent: Spring/SpringBoot
permalink: /spring/springboot/annotation/1
nav_order: 96
---

## 들어가기 앞서...  

카카오뱅크 과제를 풀면서 @transaction을 처음 접하게 되었다. 내가 업무적으로 맡은 소스들에서는 만약 트랜잭션 처리가 필요하다면 보통 XML파일에서 JDBC설정에서 transaction 허용 설정 후 Sqlsession 클래스를 활용하여 처리하였다. 과제에서 어노테이션과 어노테이션의 옵션을 활용하여 보다 편하게 처리하는 것을 보고 @transaction 활용법을 공부해보자는 생각이 들었다.

## 트랜잭션이란?

 https://kafcamus.tistory.com/30의 블로그에 DB에 대해 전혀 모르더라도 이해할 수 있게 쉽게 설명을 해놓았다. 내용을 참고하자면,

 데이터베이스 트랜잭션은 데이터베이스 관리 시스템 또는 유사한 시스템에서 상호작용의 단위이다.여기서 단위라는 말을 사용했는데, 쉽게 말하면 더 이상 쪼개질 수 없는 최소의 연산이라는 의미가 된다.

 예를 들어보자. 만약 내가 쇼핑 앱을 켜서 상품을 구매하려고 한다. 그런데 내가 결제를 하는 짧은 시간 사이에 아래와 같은 일이 벌어지면 어떨까?
 - 해당 판매자가 상품의 가격을 바꿔버려서, 잘못된 금액이 결제됨
 - 같은 상품을 다른 사람도 구매해서, 상품 재고는 1개인데 2명에게 결제됨
 - 결제가 완료되기 직전에 네트워크가 끊겨서, 돈은 나갔지만 구매완료는 되지 않음

 위의 상황이 일어나지 않기 위해선 아래의 조치가 필요할 것이다.
  - 내가 결제중일 때에는 해당 상품의 정보를 바꿀 수 없게 함
  - 내가 결제중일 때에는 해당 상품을 다른 사람이 결제하지 못하게 함
  - 내 구매가 오류로 완료되지 않았다면, 결제된 금액을 환불 처리함
 
 이를 정리하자면,
 "결제는 다른 사람과 독립적으로 이루어지며, 과정 중에 다른 연산이 끼어들 수 없다. 오류가 생긴 경우 연산을 취소하고 원래대로 되돌린다. 성공할 경우 결과를 반영한다."
 이며, 이를 DB특성인 ACID(원자성, 일관성, 고립성, 지속성)과 연관된다.



### @Transactional 어노테이션 활용예시

1)VSCode에서 단축키 F1을 누른 후 Spring Initializr: Create a Gradle Project을 입력한다.
<figure>
<img src="{{ "/media/img/Tools/Tool11.png" | absolute_url }}" />
</figure>

2)Spring Boot 버전을 선택하라고 나오는데 본인이 생각하는 버전을 선택한다.(나는 2.4.5로 진행하였다)
<figure>
<img src="{{ "/media/img/Tools/Tool12.PNG" | absolute_url }}" />
</figure>

3)프로젝트 언어를 선택하라고 나오면 Java를 선택(이미 이전 프로젝트를 진행할때 java로 설정하여 해당 단계 skip됨)

### @Transactional의 작동 원리와 흐름

4)groupId를 설정한다.
<figure>
<img src="{{ "/media/img/Tools/Tool13.PNG" | absolute_url }}" />
</figure>

5)Artifact Id를 설정한다.
<figure>
<img src="{{ "/media/img/Tools/Tool14.PNG" | absolute_url }}" />
</figure>

### @Transactional 옵션 정리

6)패키징 타입을 설정한다.(Eclipse에서 WAS로 많이 export 해본 경험이 있어 JAR를 선택했음. JAR의 경우 WAS를 가동하는데 필요한 라이브러리를 포함하여 패키징 됨)
<figure>
<img src="{{ "/media/img/Tools/Tool15.PNG" | absolute_url }}" />
</figure>

7)Java 버전을 선택한다(이미 이전 프로젝트 진행할때 1.8로 설정하여 해당 단계 skip됨)

8)필요한 라이브러리를 선택한다.(나는 Spring Boot DevTools, Spring Web, 본인이 연동할 jdbc 이렇게 3개로 시작했다.)
<figure>
<img src="{{ "/media/img/Tools/Tool16.PNG" | absolute_url }}" />
</figure>

9)프로젝트를 생성할 위치를 지정한다.
<figure>
<img src="{{ "/media/img/Tools/Tool17.PNG" | absolute_url }}" />
</figure>

10)이제 프로젝트를 시작할텐데 이전에 터미널엣 gradle을 통해 빌드한다.
<figure>
<img src="{{ "/media/img/Tools/Tool18.PNG" | absolute_url }}" />
</figure>

<figure>
<img src="{{ "/media/img/Tools/Tool19.PNG" | absolute_url }}" />
</figure>

11)터미널에서 ./gradlew bootRun을 통해 서버를 실행한다.
<figure>
<img src="{{ "/media/img/Tools/Tool20.PNG" | absolute_url }}" />
</figure>

12)서버를 안전하게 중지시키고 싶으면 새 터미널을 열어 ./gradlew --stop으로 중지한다.
<figure>
<img src="{{ "/media/img/Tools/Tool21.PNG" | absolute_url }}" />
</figure>


## 참고 자료 및 사이트
- transaction 정의 정리: https://kafcamus.tistory.com/30
- transaction anotation 옵션 정리: 
