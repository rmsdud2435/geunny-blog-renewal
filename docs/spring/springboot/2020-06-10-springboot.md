---
layout: default
title: Thymleaf를 활용한 화면처리
parent: SpringBoot
grand_parent: Spring/SpringBoot
permalink: /spring/springboot/thymlef/1
nav_order: 97
---

## 들어가기 앞서...

 앞의 "DB 연동하기" 프로젝트에서 이어서 진행하며, 일반적인 JSP의 내용과 겹치는 내용은 최대한 생략하고 다른점들에 대해서만 작성할 예정이며, 아래의 링크에 스터디용 프로젝트 올리니 참고바란다.
 타임리프는 흔히 View Template(뷰 템플릿)이라고 부르며, 뷰 템플릿은 컨트롤러가 전달하는 데이터를 이용하여 동적으로 화면을 구성할 수 있게 해준다.  
  
 - https://github.com/rmsdud2435/Springboot-Sample

### thymleaf 특징

 - html태그를 기반으로하여 th:속성을 이용하여 동적인 View를 제공
 - Thymeleaf에는 총 4가지의 활용 방법이 있다. 변수식으로 사용하는 ${}와 메세지방식 #{}, 객체변수식인 *{}, 링크방식 @{}이 있다.(해당 프로젝트는 그 중 변수식으로 사용하는 ${}를 활용하였다.)


### thymleaf 시작하기

  1) 우선 Thymeleaf를 프로젝트에 추가하는 방법은 메이븐 또는 그래들에 라이브러리를 추가해야한다.
  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
  </dependency>
  ```

  ```gradle
  implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
  ```
  
  2) 기본적인 MVC 형태의 컨트롤러에 화면에 보내고 싶은 데이터를 Model.addAttribute()를 활용하여 보낸다
  ```java
  (이하 생략)

  @RequestMapping("/list")
  public String listPerson(Model model) {

	  model.addAttribute("person", userService.listPerson());

	  return "list";
	}

  (이하 생략)
  ```
  
  3) html 페이지에 아래의 내용을 추가한다.(thymeleaf를 활용하겠단 의미)
  ```html
  <html xmlns:th="http://www.thymeleaf.org">
   ```

  4) ${}를 활용하여 데이터를 받는다.
  ```html
  (이하 생략)

        <tr th:each="list : ${person}">
            <td th:text="${list.userId}"></td>
            <td th:text="${list.name}"></td>
            <td th:text="${list.phone}"></td>
            <td th:text="${list.email}"></td>
            <td>
                <div class="btn btn-outline-primary">
                    <a href="#" th:href="@{/update/{id} (id=${list.userId})}" role="button">수정</a>
                </div>
            </td>
            <td>
                <div class="btn btn-outline-danger">
                    <a href="#" th:href="@{/delete/{id} (id=${list.userId})}" role="button">삭제</a>
                </div>
            </td>
        </tr>

  (이하 생략)
  ```

## 느낀점
  
간단하게 화면에 랜더링할때는 javascript, jqeury를 활용하는 것보다 육안으로 보기 더 편하고 소스도 간단하게 표현할 수 있는 장점이 있는 것 같다.

## 참고 사이트

 - https://blog.naver.com/PostView.nhn?isHttpsRedirect=true&blogId=bgpoilkj&logNo=221982228705&parentCategoryNo=20&categoryNo=&viewDate=&isShowPopularPosts=false&from=postView


