---
layout: default
title: 실제 서비스에서 파일 업로드하기
parent: Spring
grand_parent: Spring/SpringBoot
permalink: /spring/spring/uoload/1
nav_order: 94
---

## 업로드에 대해서...

업로드에 대해서 학교에서 배우고 실습도 해보았지만 실제 서비스에서 업로드되는 로직을 보고 많이 헷갈렸던 기억이 있다.

내가 맡은 많은 앱에서 활용되는 업로드 되는 로직에 대해 정리해본다.


### 사용자 관점와 개발자 관점 업로드

사용자 관점에서 업로드 과정을 살펴보자

게시글 등록작성 -> 브라우저에 업로드될 파일 올리기 -> 등록

내가 헷갈렷던 것은 나는 처음에 업로드를 접했을때 등록버튼을 눌렀을때 업로드가 되는 것인지 알았다.

하지만 실제로 서버로 업로드 되는 것은 브라우저에 파일을 올렸을 때이고,

등록 버튼을 눌렀을때는 해당 파일을 개발자가 원하는 repository위치로 저장하는 과정인 것이다.


즉, 브라우저에 파일을 올리면 서버 통신을 통해 임시저장 공간에 파일을 저장하며 이름이 겹치면 에러가 발생할 수 있으니 안겹치게끔 이름을 변환시킨뒤

변환된 파일명을 클라이언트에게 전달한 후, 실제로 등록 버튼을 누를때는 게시글관련 데이터, 이전 파일명, 바뀐 파일명, 저장하고 싶은 위치를 다시 

서버에게 전달하여 해당 내용을 다시 불러올 수 있게끔 DB에 저장 후 임시저장 공간에 있는 파일을 저장하고 싶은 위치를 옮기는 작업을 하는 것이다.


### 업로드 소스

등록버튼 누를 시 로직은 일반적인 DB저장 로직 + 파일 위치옮기기 로직만이 존재하여 생략하고(궁금하면 나의 Spring-5-Study-Project 참조)

업로드 되는 로직만 살펴보자.

컨트롤러는 아래와 같으며

```java

package 패키지명;

import java.io.File;
import java.util.Iterator;
import java.util.UUID;
import java.util.regex.Matcher;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.context.request.WebRequest;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.multipart.MultipartHttpServletRequest;

@Controller
@RequestMapping("/fileUpload")
public class FileUploadController {

	private static final Logger logger = LoggerFactory.getLogger(FileUploadController.class);
	
    @RequestMapping(value = "/post") //ajax에서 호출하는 부분
    @PostMapping
    @ResponseBody
    public String uploadToTempDirectory(MultipartHttpServletRequest multipartRequest) throws Exception { //Multipart로 받는다.
    	
    	logger.info("Upload is starting");
    	
        Iterator<String> itr =  multipartRequest.getFileNames();
         
        //Temp 저장공간
        String filePath = "C:/transfer_test";
        String savedUid = "";
         
        while (itr.hasNext()) { //받은 파일들을 모두 돌린다.
             

            MultipartFile 	multipartFile 		= multipartRequest.getFile(itr.next());
            String 			originalFilename 	= multipartFile.getOriginalFilename();
            String 			fileExtension 		= originalFilename.substring(originalFilename.lastIndexOf(".")+1); //파일 확장자
            
            logger.debug("originalName 	>>>>>>>>>>>>>>>> {}"	,multipartFile.getOriginalFilename());
            logger.debug("fileExtension >>>>>>>>>>>>>>>> {}"	,fileExtension);
            logger.debug("size 			>>>>>>>>>>>>>>>> {}"	,multipartFile.getSize());
            logger.debug("ContentType 	>>>>>>>>>>>>>>>> {}"	,multipartFile.getContentType());
            logger.debug("originalName 	>>>>>>>>>>>>>>>> {}"	,multipartFile.getOriginalFilename());
      
            UUID uid = UUID.randomUUID();
            savedUid = uid.toString();
            
            logger.debug("savedUid	 	>>>>>>>>>>>>>>>> {}"	,savedUid);
            
            String fileFullPath = filePath + "/" + savedUid + "." + fileExtension; 	//저장될 경로
            fileFullPath.replaceAll("/", Matcher.quoteReplacement(File.separator));
      
            //경로가 없다면 생성
    		File directory = new File(fileFullPath);
    		if (directory.exists() == false) {
    			directory.mkdirs();
    		}
    		
            try {
                //파일 저장
            	multipartFile.transferTo(directory); //파일저장 실제로는 service에서 처리
            } catch (Exception e) {
                logger.error("error occured while making file to temp directory!!! caused by >>>>>>>>>>>>> {}",e.getMessage());
                throw new Exception(e);
            }
                          
       }
          
        return savedUid;
    }

}

```

client 부분은 아래와 같다.

```jsp

<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ page session="false" %>
<html>
    <head>
        <title>Test</title>
        <script type="text/javascript" src="<c:url value="/resources/jquery/jquery-2.1.4.min.js" />"></script>
        <style>
            .dragAndDropDiv {
                border: 2px dashed #92AAB0;
                width: 650px;
                height: 200px;
                color: #92AAB0;
                text-align: center;
                vertical-align: middle;
                padding: 10px 0px 10px 10px;
                font-size:200%;
                display: table-cell;
            }
            .statusbar{
                border-top:1px solid #A9CCD1;
                min-height:25px;
                width:99%;
                padding:10px 10px 0px 10px;
                vertical-align:top;
            }
            .statusbar:nth-child(odd){
                background:#EBEFF0;
            }
            .filename{
                display:inline-block;
                vertical-align:top;
                width:250px;
            }
            .filesize{
                display:inline-block;
                vertical-align:top;
                color:#30693D;
                width:100px;
                margin-left:10px;
                margin-right:5px;
            }
            .abortFail{
                background-color:#A8352F;
                -moz-border-radius:4px;
                -webkit-border-radius:4px;
                border-radius:4px;display:inline-block;
                color:#fff;
                font-family:arial;font-size:13px;font-weight:normal;
                padding:4px 15px;
                cursor:pointer;
                vertical-align:top
            }        
            .abortSuccess{
                background-color:#819FF7;
                -moz-border-radius:4px;
                -webkit-border-radius:4px;
                border-radius:4px;display:inline-block;
                color:#fff;
                font-family:arial;font-size:13px;font-weight:normal;
                padding:4px 15px;
                cursor:pointer;
                vertical-align:top
            }
            
            .css-cancel {
			    display: inline-block;
			    position: relative;
			    margin: 0 20px 0 7px;
			    padding: 0;
			    width: 4px;
			    height: 20px;
			    background: #000;
			    transform: rotate(45deg);
			}
			.css-cancel:before {
			    display: block;
			    content: "";
			    position: absolute;
			    top: 50%;
			    left: -8px;
			    width: 20px;
			    height: 4px;
			    margin-top: -2px;
			    background: #000;
			}
        </style>
        <script type="text/javascript">
        
            $(document).ready(function(){
                 var test = "";
         		/* 
	     			웹브라우저에서 기본적으로 모든 영역에 드래그 앤 드랍 이벤트가 만들어져 있다.ex)파일 저장,해당 파일로 이동 등. 
	     			그 기본 이벤트들을 우선적으로 제거
     			*/ 			
                $(document).on('dragenter dragover drop', function (e){
                    e.stopPropagation();
                    e.preventDefault();
                });

         		/* 
     				드래그 앤 드랍 이벤트가 발생했다는 css 추가
 				*/ 	
                $(document).on("dragenter",".dragAndDropDiv",function(e){
                    $(this).css('border', '2px solid #0B85A1');
                });
                $(document).on("dragover",".dragAndDropDiv",function(e){
                	$(this).css('border', '2px dotted #0B85A1');
                });       
                $(document).on("drop",".dragAndDropDiv",function(e){           
                    $(this).css('border', '2px dotted #0B85A1');            
                 	
                    var files = e.originalEvent.dataTransfer.files;
                    handleFileUpload(files,$(this));
                });
                 

                 
                function handleFileUpload(files,obj)
                {
                   for (var i = 0; i < files.length; i++) 
                   {
                        var formData = new FormData();
                        formData.append('file', files[i]);
                  
                        var file = files[i];
                        sendFileToServer(formData, file, obj);
                   }
                }
                 
                var rowCount=0;
                function createUploadFileList(obj, flag){
                    
                    rowCount++;
                    var row="odd";
                    var cssFlag = "";
                    if(flag == "성공"){
                    	cssFlag = "abortSuccess";
                    }else{
                    	cssFlag = "abortFail"
                    }
                    
                    if(rowCount %2 ==0) row ="even";
                    this.statusbar = $("<div class='statusbar "+row+"'></div>");
                    this.filename = $("<div class='filename'></div>").appendTo(this.statusbar);
                    this.size = $("<div class='filesize'></div>").appendTo(this.statusbar);
                    this.abort = $("<div class='" + cssFlag + "'>" + flag + "</div>").appendTo(this.statusbar);
                    this.cancel = $("<div class='css-cancel'></div>").appendTo(this.statusbar);
                     
                    obj.after(this.statusbar);
                  
                    this.setFileNameSize = function(name,size){
                        var sizeStr="";
                        var sizeKB = size/1024;
                        if(parseInt(sizeKB) > 1024){
                            var sizeMB = sizeKB/1024;
                            sizeStr = sizeMB.toFixed(2)+" MB";
                        }else{
                            sizeStr = sizeKB.toFixed(2)+" KB";
                        }
                  
                        this.filename.html(name);
                        this.size.html(sizeStr);
                    }
                     
                     this.setCancel = function(){
                        this.cancel.click(function(){
                        	this.statusbar.empty();
                        });
                    } 
                }
                 
                function sendFileToServer(formData, file, obj)
                {
                    var uploadURL = "../fileUpload/post"; //Upload URL
                    var extraData ={}; //Extra Data.
                    var flag = "";
                    $.ajax({
                        url: uploadURL,
                        type: "POST",
                        contentType:false,
                        processData: false,
                        cache: false,
                        data: formData,
                        success: function(data){
                        	flag = "성공";
                        },
                        error: function(data){
                        	flag = "실패";
                        },
                        complete: function(data){
                            var status = new createUploadFileList(obj, flag); //Using this we can set progress.
                            status.setFileNameSize(file.name, file.size);
                            status.setCancel();
                        }
                    }); 
                }
                 
            });
        </script>
    </head>
     
    <body>
        <div id="fileUpload" class="dragAndDropDiv">Drag & Drop Files Here</div>
    </body>
</html>

```

### 느낀점 및 실수했던 부분

\의 값에 대해서 Windows OS에서는 돌아가더라도 Linux에서는 안돌아 갈 위험성이 있어 /를 replaceAll() 하려 했지만 특수문자여서 뜻대로 되지 않아서 

위와 같이 해결하였다.
