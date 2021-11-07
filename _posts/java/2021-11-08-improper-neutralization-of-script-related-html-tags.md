---
title: 크로스 사이트 스크립트 공격 취약점
author: dejavuhyo
date: 2021-11-08 06:00:00 +0900
categories: [Language, Java]
tags: [improper-neutralization, script-related-html-tags, 스크립트-공격-취약점, 크로스-사이트-취약점]
---

## 1. 정의
외부에서 입력되는 스크립트 문자열이 웹 페이지 생성에 사용되면 생성된 웹 페이지를 열람하는 사용자에게 피해를 입힐 수 있다.

## 2. 안전한 코딩 기법

* JSP의 document.write() 메소드와 같이 JSP의 DOM 객체 출력을 수행하는 메소드의 인자 값으로 외부의 입력을 사용할 경우 위험한 문자를 제거하는 과정이 수행되어야 한다.

* 보안성이 검증되어 있는 API를 사용하여 위험한 문자열을 제거하여야 한다.

## 3. 예제
request.getParameter()에서 전달된 외부의 입력(name)이 document.write()의 인자값 생성에 그대로 사용되었다.

* 안전하지 않은 코드의 예

```jsp
<%
// 외부로 부터 입력을 받는다.
String name = request.getParameter("name");
%>
<SCRIPT language="javascript">
// 외부의 입력을 그대로 출력한다.
document.write("name:" + <%=name%> );
```

외부의 입력(name)으로부터 ```<```와 ```>``` 같이 HTML에서 스크립트 생성에 사용되는 모든 문자열을 ```&lt; &gt; &amp; &quot;```와 같은 형태로 변경한다.

* 안전한 코드의 예

```jsp
<%
// 외부의 입력을 받는다.
String name = request.getParameter("name");
// 입력값에 대한 유효성 검사를 한다.
if ( name != null ) {
    name = name.replaceAll("<","&lt;");
    name = name.replaceAll(">","&gt");
    name = name.replaceAll("&","&amp;");
    name = name.replaceAll("\"","&quot;");
    name = name.replaceAll("\'","&#x27;");
    name = name.replaceAll("/","&#x2F;");
} else { return; }
%>
<SCRIPT language="javascript">
// 입력값이 출력된다.
document.write("name:" + <%=name%> );
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
