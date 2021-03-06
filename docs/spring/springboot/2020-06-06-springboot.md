---
layout: default
title: DB연동하기(feat. Lombok 적용)
parent: SpringBoot
grand_parent: Spring/SpringBoot
permalink: /spring/springboot/db/1
nav_order: 98
---

## 들어가기 앞서...

 앞의 "스프링부트 시작하기" 프로젝트에서 이어서 진행하며, DB 및 table 생성 등 일반적인 스프링 프로젝트와 겹치는 내용은 최대한 생략하고 다른점들에 대해서만 작성할 예정이며, 아래의 링크에 스터디용 프로젝트 올리니 참고바란다.  
  
 - https://github.com/rmsdud2435/Springboot-Sample

### 시작하기

  1) 우선 Lombok부터 프로젝트에 적용시킬 예정이다. Lombok은 중복되거나 노가다 작업인 부분들을 어노테이션을 통해 할 일을 줄여주는 라이브러리이며 대표적으로 @Getter, @Setter, @Tostring, @AllArgsConstructor 등이 있다. 아마 소스를 보면 어떻게 쓰이는지 알 수 있을 것이다. 이제 프로젝트에 적용을 시켜보자. 프로젝트 우클릭 -> Spring -> EditStarters -> Lombok 추가
  
  2) 이제 프로젝트에 롬복 적용은 끝났다. 하지만 Lombok의 사용은 기존 코딩 방식을 반하는 방식이기에 Eclispe Tool의 경우에는 lombok 라이브러리를 사용하는 class를 호출할 때 에러가 난다. 그러기에 tool에 강제 적용을 시켜줘야 한다. 프로젝트에 Maven Dependencies에서 lombok jar를 찾은 후, 우클릭 properties에서 위치를 찾아 파일 탐색기에서 해당 jar를 찾는다. 
  
  3) jar를 클릭하면 고추모양의 아이콘과 함께 뭘 install하라고 뜬다.(만약 실행이 압축프로그램으로 default로 되어있으면 클릭이 안될텐데, 그러면 관리자 권한으로 cmd켜서 해당 위치까지 찾아간후, java -jar lombok-버전.jar 실행) specify location을 클릭 후, 적용하고자 하는 eclipse tool의 실행 아이콘을 넣어준 후, install을 진행하면 된다.
   
  4) 확인하고 싶다면 install한 eclipse tool에 가서 .ini파일을 편집기로 켜보면 -javaagent:파일경로\lombok.jar가 추가된 것을 볼 수 있다.
  
  5) lombok을 활용하여 Dto 작성
  ex)
  ```java
package com.example.demo.dto;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

@Getter
@Setter
@ToString
public class UserDto {

	private String userId;
	private String name;
	private String password;
	private String phone;
	private String email;
}
  ```
  
  5) 이제 MyBatis를 인식시켜보자. application.properties에 아래의 내용 추가  
  ```properties
mybatis.mapper-locations=classpath:mapper/*.xml
mybatis.config-location=classpath:mapper/config/mybatis-config.xml
  ```
  참고로 classpath는 이클립스 기준 프로젝트 우클릭 -> properties -> Java Build Path -> Order and Export에서 확인가능하다.  
  
  6) 해당 위치에 각각 매퍼와 마이바티스 설정파일 추가(일반 스플링은 bean factory, DTD 등을 통해 매퍼와 설정파일을 인식시키게 위해 많은 과정을 거치지만 스프링부트는 설정파일은 경로변수 빼고 굳이 안넣어줘도 된다.) 예시는 아래와 같다.
  ```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.example.demo.dao.UserDao">

    <select id="listPerson" resultType="com.example.demo.dto.UserDto">
        select * from user
    </select>

    <select id="findById" parameterType="String" resultType="com.example.demo.dto.UserDto">
        select * from user where user_id=#{userId}
    </select>

    <insert id="addPerson" parameterType="com.example.demo.dto.UserDto">
        INSERT INTO
        user (user_id, name, password, phone, email)
        VALUES(#{userId},#{name},#{password},#{phone},#{email})
    </insert>

    <update id="updatePerson" parameterType="com.example.demo.dto.UserDto">
        UPDATE user
        SET
            name=#{name},
            password=#{password},
            phone=#{phone},
            email=#{email}
        WHERE
            user_id = #{userId}
    </update>

    <delete id="deletePerson" parameterType="String">
        DELETE FROM user
        WHERE user_id=#{userId}
    </delete>
    
</mapper>
  ```
여기서 mapper의 namespace는 Dao 인터페이스의 클래스를 넣어주면 자동으로 해당 위치의 인터페이스의 메소드와 네임스페이스 메소드 이름이 같다면 알아서 매핑을 시켜준다.  
  
  ```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <setting name="callSettersOnNulls" value="true"/>
        <setting name="jdbcTypeForNull" value="NULL"/>
    </settings>
</configuration>
  ```
  해당 설정 부분들은 스프링부트가 아닌 설정이라서 대충 설명하자면 mapUnderscoreToCamelCase는 DB의 user_id 등의 컬럼 형식을 userId 등의 형식 변수로 매핑시켜주며, callSettersOnNulls는 쿼리 결과 필드가 null인 경우 누락이 되서 나오는데 누락이 안되게 하는 설정이고, jdbcTypeForNull는 쿼리에 보내는 파라메터가 null인 경우, 오류 발생하는 것 방지해주는 설정이다.
  
  7) 만든 mybatis mapper에 맞게끔 Dao 인터페이스를 만들준다.  
  ex)  
  ```java  
package com.example.demo.dao;

import java.util.List;

import org.apache.ibatis.annotations.Mapper;

import com.example.demo.dto.UserDto;

@Mapper
public interface UserDao {
    public List<UserDto> listPerson();

    public UserDto findById(String userId);

    public void addPerson(UserDto userDto) throws Exception;

    public void updatePerson(UserDto userDto);

    void deletePerson(String userId);

}  
  ```
  
  
  8) Controller와 Service는 일반 스프링과 다를 바 없이 만들어 준다. 특이점이 있다면 @AllArgsConstructor을 통해 빈등록이 알아서 된다는 점이다. 아래는 나의 예시이다.  
  ```java  
package com.example.demo.service;

import lombok.AllArgsConstructor;
import org.springframework.stereotype.Service;

import com.example.demo.dao.UserDao;
import com.example.demo.dto.UserDto;

import java.util.List;

@Service
@AllArgsConstructor
public class UserService {

    private UserDao userDao;

    public List<UserDto> listPerson() {
        List<UserDto> userDtos = userDao.listPerson();

        return userDtos;
    }

    public void addPerson(UserDto userDto) throws Exception {
    	userDao.addPerson(userDto);
    }

    public UserDto findById(String userId) {
        return userDao.findById(userId);
    }

    public void updatePerson(UserDto userDto) {
    	userDao.updatePerson(userDto);
    }

    public void deletePerson(String userId) {
    	userDao.deletePerson(userId);
    }

}
  ```
    
  ```java  
package com.example.demo.controller;

import lombok.AllArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

import com.example.demo.dto.UserDto;
import com.example.demo.service.UserService;

import javax.servlet.http.HttpServletRequest;

@Controller
@AllArgsConstructor
public class UserController {

	    private UserService userService;

	    @RequestMapping("/list")
	    public String listPerson(Model model) {

	        model.addAttribute("person", userService.listPerson());

	        return "list";
	    }

	    @GetMapping("/add")
	    public String addPage(Model model) {
	        return "addpage";
	    }

	    @PostMapping("/add")
	    public String addPerson(Model model, HttpServletRequest request) throws Exception {

	        String name 	= request.getParameter("name");
	        String email 	= request.getParameter("email");
	        String password = request.getParameter("password");
	        String phone 	= request.getParameter("phone");
	        String userId 	= request.getParameter("user_id");

	        UserDto userDto = new UserDto();
	        userDto.setName(name);
	        userDto.setEmail(email);
	        userDto.setPassword(password);
	        userDto.setPhone(phone);
	        userDto.setUserId(userId);
	        
	        userService.addPerson(userDto);
	        return "redirect:/list";
	    }

	    @GetMapping("/update/{id}")
	    public String updatePage(Model model, @PathVariable("id") String userId) {
	        model.addAttribute("person", userService.findById(userId));
	        return "updatepage";
	    }

	    @PostMapping("/update/{id}")
	    public String updatePerson(Model model, @PathVariable("id") String userId, HttpServletRequest request) {
	        String name 	= request.getParameter("name");
	        String email 	= request.getParameter("email");
	        String password = request.getParameter("password");
	        String phone 	= request.getParameter("phone");

	        UserDto userDto = new UserDto();
	        userDto.setName(name);
	        userDto.setPassword(password);
	        userDto.setUserId(userId);
	        userDto.setEmail(email);
	        userDto.setPhone(phone);
	        
	        userService.updatePerson(userDto);

	        return "redirect:/list";
	    }

	    @GetMapping("/delete/{id}")
	    public String deletePerson(Model model, @PathVariable("id") String userId) {
	    	userService.deletePerson(userId);
	        return "redirect:/list";
	    }

}
  ```
 
  9) 프로젝트 생성시 자동으로 만들어 지는 DemoApplicationTests 클라스를 활용하면 굳이 복잡한 junit 설정없이 편하게 테스트 할 수 있다.(역시, 스프링 부트!) 아래는 나의 예시이다.  
  ```java  
package com.example.demo;

import javax.annotation.Resource;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

import com.example.demo.dto.UserDto;
import com.example.demo.service.UserService;

import lombok.AllArgsConstructor;

@SpringBootTest
class DemoApplicationTests {
	
	@Resource
	UserService userService;

	@Test
	void contextLoads() throws Exception {
		UserDto userDto = new UserDto();
		userDto.setUserId("gykim");
		userDto.setName("geunyoung2");
		userDto.setPassword("12345");
		userDto.setPhone("1111222233");
		userDto.setEmail("test2@naver.com");
		
		//userService.addPerson(userDto);
		//userService.updatePerson(userDto);
		System.out.println(userService.findById("gykim"));
	}

}
  ```

## 느낀점
  
 역시나 설정하는데 많은 시간을 줄여주는 것을 느꼈다. 특히 Junit 부분에 대해서 많을 시간을 줄일 수 있었다.

