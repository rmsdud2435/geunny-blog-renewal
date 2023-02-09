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


이유는 크게 2가지로, 첫번째는 Mathematical functions이다.
예전 자바 개발자들은 수학 함수들을 사용하는데 Math.abs처럼 함수 앞에 Math가 붙는게 별로 우아하다고 생각하지 않은것 같다.



## static import의 유의사항


### 참고한 블로그 및 정보
 * ##### import vs static import
 https://velog.io/@kasania/Java-Static-import%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B4%80%EC%B0%B0