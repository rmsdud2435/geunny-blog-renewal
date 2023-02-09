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
쉽게 예시를 들어보자면 Math 패키지에 선언된 함수 및 변수를 활용해야한다고 하면 static import를 하지 않은 소스는 아래와 같은 것이다.
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


JsonNode의 메소드를 본다면 값을 넣는 용도가 아닌 주고 받는 용도로 보인다.

메소드들이 대체적으로 get을 해오거나 is를 통해 맞는지 아닌지 boolean값을 주는 것들로 이루어져있다.

반면 ObjectNode는 그런 메소드뿐만 아니라 put이나 remove 등과 같이 값을 추가하거나 제거할 수 있는 메소드들이 존재한다.

그래서 필자는 Json형태로 데이터를 보내고 싶을 때 ObjectNode를 통해 Json형식으로 만들 후 JsonNode로 변환 후 전달하는 형식의 코드를 자주 사용한다.

## static import의 유의사항


### 참고한 블로그 및 정보
 * ##### import vs static import
 https://velog.io/@kasania/Java-Static-import%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B4%80%EC%B0%B0