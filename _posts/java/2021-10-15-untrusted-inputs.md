---
title: 보호 메커니즘을 우회할 수 있는 입력값 변조
author: dejavuhyo
date: 2021-10-15 06:00:00 +0900
categories: [Language, Java]
tags: [untrusted-inputs, 입력값 변조]
---

## 1. 정의
응용프로그램이 외부 입력값에 대한 신뢰를 전제로 보호 메커니즘을 사용하는 경우 공격자가 입력값을 조작할 수 있다면 보호 메커니즘을 우회할 수 있게 된다. 개발자들이 흔히 쿠키, 환경변수 또는 숨은 필드와 같은 입력값이 조작될 수 없다고 가정하지만, 공격자는 다양한 방법을 통해 입력값들을 변경할 수 있고 조작된 내용은 탐지되지 않을 수 있다.

인증이나 인가와 같은 보안 결정이 이런 입력값(쿠키, 환경변수, 숨은 필드 등)에 기반으로 수행되는 경우 공격자는 이런 입력값을 조작하여 응용프로그램의 보안을 우회할 수 있음으로 충분한 암호화, 무결성 체크 또는 다른 메커니즘이 없는 경우 외부사용자에 의한 입력값을 신뢰해서는 안 된다.

## 2. 안전한 코딩 기법

* 상태정보나 민감한 데이터 특히 사용자 세션 정보와 같은 중요한 정보는 서버에 저장하고 보안 확인 절차도 서버에서 실행한다.

* 보안 설계관점에서 신뢰할 수 없는 입력값이 애플리케이션 내부로 들어올 수 있는 지점과 보안 결정에 사용되는 입력값을 식별하고 제공되는 입력값에 의존할 필요가 없는 구조로 변경할 수 있는지 검토한다.

## 3. 예제
사용자의 role을 설정할 때 사용자 웹 브라우저의 쿠키의 role에 할당된 값을 사용하고 있어 이 값이 사용자에 의해 변경되는 경우 사용자 role 값이 의도하지 않은 값으로 할당될 수 있다.

* 안전하지 않은 코드의 예

```java
Cookie[] cookies = request.getCookies();
for (int i =0; i< cookies.length; i++) {
    Cookie c = cookies[i];
    if (c.getName().equals("role")) {
        userRole = c.getValue();
    }
}
```

사용자 권한, 인증 여부 등 보안 결정에 사용하는 값은 사용자 입력값을 사용하지 않고 내부 세션 값을 활용한다.

* 안전한 코드의 예

```java
HttpSession session = context.getSession(id);
String userRole = (Stirng)session.getValue("role");
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
