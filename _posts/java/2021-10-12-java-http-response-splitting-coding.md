---
title: Java HTTP 응답 분할 코딩 기법
author: dejavuhyo
date: 2021-10-12 06:00:00 +0900
categories: [Language, Java]
tags: [http-response-splitting, http-응답-분할]
---

## 1. 정의
HTTP 요청에 들어 있는 인자값이 HTTP 응답 헤더에 포함되어 사용자에게 다시 전달 될 때 입력값에 CR(Carriage Return)이나 LF(Line Feed)와 같은 개행문자가 존재하면 HTTP 응답이 2개 이상으로 분리될 수 있다. 이 경우 공격자는 개행문자를 이용하여 첫 번째 응답을 종료시키고 두 번째 응답에 악의적인 코드를 주입하여 XSS 및 캐시 훼손(cache poisoning) 공격 등을 수행할 수 있다.

![http-response-splitting](/assets/img/2021-10-12-http-response-splitting/http-response-splitting.png)

## 2. 안전한 코딩 기법

* 외부에서 입력된 인자값을 사용하여 HTTP 응답 헤더 (Set Cookie 등)에 포함할 경우 CR, LF 등이 제거하거나 적절한 인코딩 기법을 사용하여 변환한다.

* 외부에서 입력 된 인자값을 적절한 필터링 코드를 통해 검증하여, 오작동을 일으킬 소지가 있는 문자들을 제거한 후 코드에 사용한다.

## 3. 예제
다음의 예제는 외부의 입력값을 사용하여 반환되는 쿠키의 값을 설정하고 있다. 그런데, 공격자가 `Wiley Hacker\r\nHTTP/1.1 200 OK\r\n`를 authorName의 값으로 설정할 경우, 다음의 예와 같이 의도하지 않은 두 개의 페이지가 전달된다. 또한 두 번째 응답 페이지는 공격자가 마음대로 수정할 수 있다.

```text
HTTP/1.1 200 OK...Set-Cookie: author=Wiley Hacker HTTP/1.1 200 OK ...
```

* 안전하지 않은 코드의 예

```java
throws IOException, ServletException {
    response.setContentType("text/html");
    String author = request.getParameter("authorName");
    Cookie cookie = new Cookie("replidedAuthor", author);
    cookie.setMaxAge(1000);
    response.addCookie(cookie);
    RequestDispatcher frd = request.getRequestDispatcher("cookieTest.jsp");
    frd.forward(request, response);
}
```

외부에서 입력되는 값에 대하여 널 여부를 체크하고, replaceAll을 이용하여 개행문자 `\r, \n`을 제거하여 헤더 값이 나누어지는 것을 방지한다.

* 안전한 코드의 예

```java
throws IOException, ServletException {
    response.setContentType("text/html");
    String author = request.getParameter("authorName");
    if (author == null || "".equals(author)) return;
    String filtered_author = author.replaceAll("\r", "").replaceAll("\n", "");
    Cookie cookie = new Cookie("replidedAuthor", filtered_author);
    cookie.setMaxAge(1000);
    cookie.setSecure(true);
    response.addCookie(cookie);
    RequestDispatcher frd = request.getRequestDispatcher("cookieTest.jsp");
    frd.forward(request, response);
}
```

## [출처 및 참고]
* [https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=](https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=)
