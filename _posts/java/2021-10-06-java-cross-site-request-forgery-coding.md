---
title: Java 크로스사이트 요청 위조 코딩 기법
author: dejavuhyo
date: 2021-10-06 09:00:00 +0900
categories: [Language, Java]
tags: [cross-site-request-forgery, 크로스사이트-요청-위조]
---

## 1. 정의
특정 웹 사이트에 대해서 사용자가 인지하지 못한 상황에서 사용자의 의도와는 무관하게 공격자가 의도한 행위(수정, 삭제, 등록 등)를 요청하게 하는 공격을 말한다. 웹 애플리케이션이 사용자로부터 받은 요청에 대해서 사용자가 의도한 대로 작성되고 전송된 것인지 확인하지 않은 경우 발생할 수 있고 특히 해당 사용자가 관리자인 경우 사용자 권한 관리, 게시물 삭제, 사용자 등록 등 관리자 권한으로만 수행 가능한 기능을 공격자의 의도대로 실행시킬 수 있게 된다.

공격자는 사용자가 인증한 세션이 특정 동작을 수행하여도 계속 유지되어 정상적인 요청과 비정상적인 요청을 구분하지 못하는 점을 악용하여 피해가 발생한다. 웹 응용프로그램에 요청을 전달할 경우, 해당 요청의 적법성을 입증하기 위하여 전달되는 값이 고정되어 있고 이러한 자료가 GET 방식으로 전달된다면 공격자가 이를 쉽게 알아내어 원하는 요청을 보냄으로써 위험한 작성을 요청할 수 있게 된다.

![cross-site-request-forgery](/assets/img/2021-10-06-cross-site-request-forgery/cross-site-request-forgery.png)

## 2. 안전한 코딩 기법

* 입력화면 폼 작성 시 GET 방식보다는 POST 방식을 사용하고 입력화면 폼과 해당 입력을 처리하는 프로그램 사이에 토큰을 사용하여, 공격자의 직접적인 URL 사용이 동작하지 않도록 처리한다. 특히 중요한 기능에 대해서는 사용자 세션 검증과 더불어 재인증을 유도한다.

## 3. 예제
GET 방식은 단순히 폼 데이터를 URL 뒤에 덧붙여서 전송하기 때문에 GET 방식의 폼을 사용하면 전달 값이 노출되므로 CSRF 공격에 쉽게 노출될 수 있다.

* 안전하지 않은 코드의 예

```html
<form name="MyForm" method="get" action="customer.do">
<input type=text name="txt1">
<input type=submit value="보내기">
</form>
```

Post 방식을 사용하여 위협을 최소화 한다.

* 안전한 코드의 예

```html
<form name="MyForm" method="post" action="customer.do">
<input type=text name="txt1">
<input type=submit value="보내기">
</form>
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
