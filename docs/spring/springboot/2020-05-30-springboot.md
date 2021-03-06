---
layout: default
title: SpringBoot 시작하기
parent: SpringBoot
grand_parent: Spring/SpringBoot
permalink: /spring/springboot/start/1
nav_order: 99
---

## 들어가기 앞서...

 이번 신입으로 회사를 다시 들어가게 되어 교육을 다시 듣게 되었다. 그 중 나에게 제일 와닿았던 교육은 스프링 부트의 활용이었다. 이전 회사에서는 스프링부트를 사용하는 것이 아닌 레거시스프링을 활용하여 부트를 접해볼 기회가 없어 개인 스터디를 할때 인터넷을 통해 부분적으로 공부를 하다 이번 계기를 통해 체계적으로 배운것들을 정리할 예정이다. 일반적인 스프링 프로젝트와 겹치는 내용은 최대한 생략하고 다른점들에 대해서만 작성할 예정이며, 아래의 링크에 스터디용 프로젝트 올리니 참고바란다.  
  
 - https://github.com/rmsdud2435/Springboot-Sample

### 시작하기

  1) https://spring.io/tools에서 STS를 다운받는다.(Tool은 tool일 뿐이니 굳이 이것까지 따라할 필요는 없고 익숙한 것으로 시작)
  
  2) New -> Spring Starter Project 클릭 후, Next 
  
  3) 본인이 만들고픈 자바버전 및 Group, Artifact 작성 후, Next
  
  4) 정말 기본적인 Dependency 추가(내 기준, Spring Boot DevTools(소스 수정시 자동으로 재시작), Spring Web(각종 유용한 어노테이션들), Mybatis framework(JPA 관련도 추후 작성 예정), MySQL Driver(본인이 사용할 DB Driver로 선택))
    
  5) 프로젝트 생생되었으면 아마 퍄키지 형식의 프로젝트로 안보일 것이다. 그렇다면, 프로젝트 우클릭 -> Configure -> Convert to Maven Project 클릭 후 로딩 대기(혼자 스터디할때는 안떠서 당황했으나 Configure -> Gradle어쩌고저쩌고 클릭하니 내가 아는 프로젝트 형식으로 바뀜)
  
  6) resource 밑에 application.properties에 아래의 내용 추가 
```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/본인DB?useSSL=false&serverTimezone=Asia/Seoul
spring.datasource.username=본인의 계정
spring.datasource.password=본인의 비밀번호

#추후 DB연동에 사용할 내용
#mybatis.mapper-locations=classpath:mapper/*.xml
#mybatis.config-location=classpath:mapper/config/mybatis-config.xml
```
  
  7) 처음에 설정했던 기본 package에 DemoApplication이라는 java 파일이 있을텐데 그 밑의 패키지로 만들어 Controller, Service, Dto 등 어노테이션이 필요한 파일들은 그 밑에다 작성(소스는 위의 Git 링크에 올라간 소스 참고)하면 된다.
  
  8) 이제 Spring Boot를 활용해서 편하게 개발해보자(굳이, 스프링부트에 국한이 아닌 이번에 배운 내용들을 작성할 예정)
  
  9) 페이지 이동 및 페이지 들어가면서 뿌려주는 데이터에 대해선 Thymeleaf를 할용할 것이다. 프로젝트 우클릭 -> Spring -> EditStarters -> Thymeleaf 추가  
  
  10) src/main/resource 밑에 templates 폴더와 html 파일생성(내 경우 main.html). 인코딩 관련은 다루지 않겠음.
  
  11) 만들어진 html페이지에 xmlns:th="https://www.thymeleaf.org" 추가
  ex) 
```html
<!DOCTYPE html>
<html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="https://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>

</body>
</html>

```
  
  12) 이러면 따로 suffix나 prefix 없이 controller에서 string으로 값을 반환하면 templates 아래있는 html 파일을 읽어온다. 
  ex) 
```java
package com.example.demo.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class BasicController {

	@GetMapping("/")
	public String mainpage() {
		return "mian";
	}
}

```

여기까지가 컨트롤러를 통한 단순한 페이지 호출이다. 다음 포스팅에는 DB와의 연동과 DTO, DAO, SERVICE을 활용한 서비스 제공까지 정리할 예정이다.


## 느낀점
  
 확실히 일반 스프링 프로젝트보다 접근성이 뛰어나고 설정할 것들이 적은 것같다. 하지만 내가 꼰대 기질이 있어서 그런지는 몰라도 내부적으로 왜 페이지가 불러오고 어떤 설정들이 자동으로 되고 있는지를 알 수 없는 부분들이 많아 개인적으로는 실력있는 개발자가 목표라면 일반 스프링을 어느정도 익힌 후에 스프링부트를 활용하는 것이 더 도움 될 것이란 생각이 든다. 암튼 정말 별다른 설정없이 이렇게 프로젝트를 진행할 수 있다는 것이 참 유용하고 개발에 대한 접근성이 이전보다 올라간 것은 확실한것 같다.
