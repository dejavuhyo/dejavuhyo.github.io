---
title: Java 위험한 형식 파일 업로드 코딩 기법
author: dejavuhyo
date: 2021-09-16 06:00:00 +0900
categories: [Language, Java]
tags: [upload-dangerous-type-file, 위험한-형식-파일-업로드]
---

## 1. 정의
위험한 형식 파일 업로드(Unrestricted Upload of File with Dangerous Type)는 서버 측에서 실행될 수 있는 스크립트 파일(asp, jsp, php 파일 등)이 업로드 가능하고, 이 파일을 공격자가 웹을 통해 직접 실행시킬 수 있는 경우 공격자는 스크립트 파일을 업로드하고 이 파일을 통해 시스템 내부명령어를 실행하거나 외부와 연결하여 시스템을 제어할 수 있다.

![upload-dangerous-type-file](/assets/img/2021-09-16-upload-dangerous-type-file/upload-dangerous-type-file.png)

## 2. 안전한 코딩 기법

* 업로드하는 파일 타입과 크기를 제한하고, 업로드 디렉터리를 웹서비스의 도큐먼트 외부에 설정한다.

* 화이트리스트 방식으로 허용된 확장자만 업로드되도록 하고, 확장자도 대소문자 구분 없이 처리하도록 한다.

* 공격자의 웹을 통한 직접 접근을 차단한다. 또한 파일 실행 여부를 설정 할 수 있는 경우, 실행 속성을 제거한다.

## 3. 예제
업로드할 파일에 대한 유효성을 검사하지 않으면, 위험한 유형의 파일을 공격자가 업로드하거나 전송할 수 있다.

* 안전하지 않은 코드의 예

```java
public void upload(HttpServletRequest request) throws ServletException {
    MultipartHttpServletRequest mRequest = (MultipartHttpServletRequest) request;
    String next = (String) mRequest.getFileNames().next();
    MultipartFile file = mRequest.getFile(next);
    
    // MultipartFile로부터 file을 얻음
    String fileName = file.getOriginalFilename();
    
    // upload 파일에 대한 확장자, 크기의 유효성 체크를 하지 않음
    File uploadDir = new File("/app/webapp/data/upload/notice");
    String uploadFilePath = uploadDir.getAbsolutePath()+"/" + fileName;
    
    /* 이하 file upload 루틴 */
}
```

업로드 파일의 확장자를 검사하여 허용되지 않은 확장자일 경우 업로드를 제한하고 있으며, 저장 시 외부에서 입력된 파일명을 그대로 사용하지 않고 있다.

* 안전한 코드의 예

```java
public void upload(HttpServletRequest request) throws ServletException {
    MultipartHttpServletRequest mRequest = (MultipartHttpServletRequest) request;
    String next = (String) mRequest.getFileNames().next();
    MultipartFile file = mRequest.getFile(next);
    if ( file == null )
        return ;
    
    // 업로드 파일 크기를 제한한다.
    int size = file.getSize();
    if ( size > MAX_FILE_SIZE ) {
        throw new ServletException("에러");
    }
    
    // MultipartFile로 부터 file을 얻음
    String fileName = file.getOriginalFilename().toLowerCase();
    
    // 화이트리스트 방식으로 업로드 파일의 학장자를 체크한다.
    if (fileName != null) {
        if(fileName.endsWith(".doc") || fileName.endsWith(".hwp") || fileName.endsWith(".pdf") || fileName.endsWith(".xls") ) {
        /* file 업로드 루틴 */
    } else {
        throw new ServletExeption("에러");
    }
    // 업로드 파일의 디럭터리 위치는 도큐먼트 루트의 밖에 위치시킨다.
    File uploadDir = new File("/app/webapp/data/upload/notice");
    String uploadFilePath = uploadDir.getAbsolutePath() + "/" + fileName;
    
    /* 이하 file upload 루틴 */
}
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
