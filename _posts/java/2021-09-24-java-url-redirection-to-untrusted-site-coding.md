---
title: Java 신뢰되지 않은 URL 주소로 자동 접속 연결 코딩 기법
author: dejavuhyo
date: 2021-09-24 06:00:00 +0900
categories: [Language, Java]
tags: [untrusted-url-redirection, open-redirect, 신뢰되지-않은-url-연결]
---

## 1. 정의
사용자로부터 입력되는 값을 외부사이트의 주소로 사용하여 자동으로 연결하는 서버 프로그램은 피싱(phishing) 공격에 노출되는 취약점을 가질 수 있다.

일반적으로 클라이언트에서 전송된 URL 주소로 연결하기 때문에 안전하다고 생각할 수 있으나, 해당 폼의 요청을 변조함으로써 공격자는 사용자가 위험한 URL로 접속할 수 있도록 공격할 수 있다.

![open-redirect](/assets/img/2021-09-24-url-redirection-to-untrusted-site/open-redirect.png)

## 2. 안전한 코딩 기법

* 자동 연결할 외부 사이트의 URL과 도메인은 화이트리스트로 관리하고, 사용자 입력값을 자동 연결할 사이트 주소로 사용하는 경우에는 입력된 값이 화이트리스트에 존재하는지 확인해야 한다.

## 3. 예제
다음과 같은 코드가 서버에 존재할 경우 공격자는 다음과 같은 링크를 희생자가 접근하도록 함으로써 희생자가 피싱 사이트 등으로 접근하도록 할 수 있다.

`<a href="http://bank.example.com/redirect?url=http://attacker.example.net">Click</a>`

* 안전하지 않은 코드의 예

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    String query = request.getQueryString();
    if (query.contains("url")) {
        String url = request.getParameter("url");
        response.sendRedirect(url);
    }
}
```

다음의 예제와 같이, 외부로 연결할 URL과 도메인들은 화이트리스트를 작성한 후, 그중에서 선택함으로써 안전하지 않은 사이트로의 접근을 차단할 수 있다.

* 안전한 코드의 예

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    // 다른 페이지 이동하는 URL 리스트를 만든다.
    String allowURL[] = {"http://url1.com", "http://url2.com", "http://url3.com"};
    // 입력받는 URL은 미리 정해진 URL의 order로 받는다.
    String nurl = request.getParameter("nurl");
    try {
        Integer n = Integer.parseInt(nurl);
        if ( n >= 0 && n < 3)
            response.sendRedirect(allowURL[n]);
    } catch (NumberFormatException nfe) {
        // 사용자 입력값이 숫자가 아닐 경우 적절히 에러를 처리한다.
    }
}
```

## [출처 및 참고]
* [https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=](https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=)
