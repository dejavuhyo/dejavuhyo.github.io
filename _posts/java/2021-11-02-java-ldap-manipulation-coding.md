---
title: Java LDAP 처리 코딩 기법
author: dejavuhyo
date: 2021-11-02 06:00:00 +0900
categories: [Language, Java]
tags: [ldap-manipulation, ldap-처리]
---

## 1. 정의
LDAP 질의문이나 결과로 외부 입력이 부분적으로 적절한 처리 없이 사용되면 LDAP 질의문이 실행될 때 공격자는 LDAP 질의문의 내용을 마음대로 변경할 수 있다. 특히 여기서는 LDAP 질의문 자체에 외부 입력이 영향을 주는 경우를 말한다.

## 2. 안전한 코딩 기법

* 외부 입력에 대한 적절한 유효성 검증 후 사용해야 하며, LDAP 사용 시 질의문을 제한하여 허용된 레코드만을 접근하도록 하는 접근 제어 기능을 사용해야 한다.

## 3. 예제
다음의 예제는 외부의 입력(name)이 검색을 위한 base 문자열의 생성에 사용되고 있다. 이 경우 임의의 루트 디렉터리를 지정하여 정보에 접근할 수 있으며, 적절한 접근제어가 동반되지 않을 경우 노출이 발생할 수 있다.

* 안전하지 않은 코드의 예

```java
try {
    // 외부로 부터 입력을 받는다.
    String name = props.getProperty("ldap.properties");
    // 입력값에 대한 BasicAttribute를 생성한다.
    BasicAttribute attr = new BasicAttribute("name", name);
    // 외부 입력값이 LDAP search의 인자로 사용이 된다.
    NamingEnumeration answer = ctx.search("ou=NewHires", attr.getID(), new SearchControls());
    printSearchEnumeration(answer);
    ctx.close();
} catch (NamingException e) {
}
```

외부 입력에 대한 적절한 유효성 검증 후 사용해야 하며, LDAP 사용 시 질의문을 제한하여 허용된 레코드만을 접근하도록 하는 접근 제어 기능을 사용해야 한다.

* 안전한 코드의 예

```java
try {
    // 외부로 부터 입력값을 받는다.
    String name = props.getProperty("name");
    // 입력값에 대한 검사를 한다.
    if (name == null || "".equals(name)) return;
    String filter = "(name =" + name.replaceAll("\\*", "") + ")";
    
    // 검증된 입력값을 LDAP search 인자로 사용한다.
    NamingEnumeration answer = ctx.search("ou=NewHires", filter, new SearchControls());
    printSearchEnumeration(answer);
    ctx.close();
} catch (NamingException e) {
}
```

## [출처 및 참고]
* [https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=](https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=)
