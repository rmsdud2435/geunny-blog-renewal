---
layout: default
title: import와 static import의 차이
parent: Java
permalink: /java/import/1
nav_order: 88
---

## import와 static import

 이전에 카카오뱅크 과제를 풀면서 처음으로 소스상에서 static import를 봤다. 같은 소스 내에서도 어떤 package는 import를 하고 어떤 package는 static import를 하는데 왜 그런건지 알아보면서 정리하게 되었다.

### 개념

개념적으로 따저본다면, ObjectNode는 JsonNode의 상위 개념이다.

ObjectNode의 하위 객체로는 JsonNode 외에도 ArrayNode 등이 존재한다.

즉, JsonNode의 생김새를 보면 {}와 같이 생겼고 ArrayNode는 []와 같이 생겼으며,

ObjectNode는 두 생김새를 다 받일 수 있다.

다르게 말하자면 JsonNode를 ObjectNode로 일반적으로 캐스팅시에 에러가 날 확률이 적(없)지만,

ObjectNode를 JsonNode로 캐스팅 시엔 에러가 날 수 있다.<--무조건 나는 것은 아니고...


### 활용 예


JsonNode의 메소드를 본다면 값을 넣는 용도가 아닌 주고 받는 용도로 보인다.

메소드들이 대체적으로 get을 해오거나 is를 통해 맞는지 아닌지 boolean값을 주는 것들로 이루어져있다.

반면 ObjectNode는 그런 메소드뿐만 아니라 put이나 remove 등과 같이 값을 추가하거나 제거할 수 있는 메소드들이 존재한다.

그래서 필자는 Json형태로 데이터를 보내고 싶을 때 ObjectNode를 통해 Json형식으로 만들 후 JsonNode로 변환 후 전달하는 형식의 코드를 자주 사용한다.

## 참고한 블로그 및 정보

* >import vs static import
https://velog.io/@kasania/Java-Static-import%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B4%80%EC%B0%B0