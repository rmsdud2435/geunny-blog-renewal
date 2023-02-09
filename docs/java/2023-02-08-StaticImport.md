---
layout: default
title: import와 static import의 차이
parent: Java
permalink: /java/import/1
nav_order: 88
---

## import와 static import

 이전에 카카오뱅크 과제를 풀면서 처음으로 소스상에서 static import를 봤다. 같은 소스 내에서도 어떤 package는 import를 하고 어떤 package는 static import를 하는데 왜 그런건지 알아보면서 정리하게 되었다.


## static import를 쓰면 소스가 어떻게 변할까?

 static import를 쓰면 import된 패키지를 생략하여 작성할 수 있다.
 쉽게 예시를 들어보자면 Math 패키지에 선언된 함수 및 변수를 활용해야한다고 하면 static import를 하지 않은 소스는 아래와 같을 것이다.
```java
int i = Math.abs(-20);
double d = Math.acos(Math.PI) * Math.E;
```

만약 Math 패키지를 static import 한다면 소스는 아래처럼 바뀔 것 이다.
```java
import static java.lang.Math.*;
    
...
    
int i = abs(-20);
double d = acos(PI) * E;
```


## static import를 왜 쓸까?
 
 (블로그 https://velog.io/@kasania 발췌)


 이유는 크게 2가지로, 첫번째는 Mathematical functions이다.
 예전 자바 개발자들은 수학 함수들을 사용하는데 Math.abs처럼 함수 앞에 Math가 붙는게 별로 우아하다고 생각하지 않은것 같다.

 C, 포트란, 파스칼은 단순 이름으로 사용하는데 자바는 Math가 붙어 장황하니, Math.abs(x) 대신 abs(x)로 쓰려고 기능을 요청했다는 소리다.

 사실 진짜는 두번째 이유인 Named constant이다.
 몇몇 개발자들이 유틸리티 "인터페이스"에 상수를 만들고, 이를 구현하는 식으로 상수를 사용한다는 소리인데, 이것에 대해 이해하기 위해서는 Constant interface에 대한 이해가 필요하다.
 상수를 간단하게 사용하려는 목적으로 Constant interface를 사용하는 개발자들을, Constant interface 대신 유틸리티 클래스를 사용하도록 유도하기 위해서 요청된 기능이라고 볼 수 있다.
  
 좀 더 쉽게 풀어쓰면 특정 패키지를 통해 흔히 묶어서 사용되는 변수를 아무 변수명이나 사용하는 것이 아닌 패키지에서 지정한 변수를 공통적으로 사용하면서 개발자들의 소스를 통일성을 주는 것이다.   

 물론 이 또한, 같은 JDK 버전에서 Enum이 추가된 이후로는 의미가 퇴색된 느낌이 있다


## static import 활용시 유의사항

 잘 활용만 한다면 소스를 깔끔하게 만들 수 있지만 반대로 가독성이 떨어질 수 있다. 예를 들어 두 패키지를 static import를 했지만 패키지에서 사용되는 함수가 같다면 어떤 패키지에서 부른 함수인지 헷갈릴 것이다.
 ```java
import static java.lang.Integer.*;
import static java.lang.Double.*;
...
    
int i = parseInt("1"); //parseInt라는 함수가 Integer.parseInt 인지, Double.patseInt인지 가독성이 떨어짐
double d = parseDouble("5.0");
if(i < MAX_VALUE){
    System.out.println("less than MAX_VALUE");
}
 ```



### 참고한 블로그 및 정보
 * ##### import vs static import
 https://velog.io/@kasania/Java-Static-import%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B4%80%EC%B0%B0