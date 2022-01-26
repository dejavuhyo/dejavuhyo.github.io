---
title: LDAP 삽입
author: dejavuhyo
date: 2021-10-05 09:00:00 +0900
categories: [Language, Java]
tags: [ldap-injection, ldap-삽입]
---

## 1. 정의
공격자가 외부 입력을 통해서 의도하지 않은 LDAP 명령어를 수행할 수 있다. 즉, 웹 응용프로그램이 사용자가 제공한 입력을 올바르게 처리하지 못하면, 공격자가 LDAP 명령문의 구성을 바꿀 수 있다. 이로 인해 프로세스가 명령을 실행한 컴포넌트와 동일한 권한(authentication)을 가지고 동작하게 된다.

LDAP 쿼리문이나 결과에 외부 입력이 부분적으로 적절한 처리 없이 사용되면, LDAP 쿼리문이 실행될 때 공격자는 LDAP 쿼리문의 내용을 마음대로 변경할 수 있다.

![ldap-injection](/assets/img/2021-10-05-ldap-injection/ldap-injection.png)

## 2. 안전한 코딩 기법

* 위험 문자에 대한 검사 없이 외부 입력을 LDAP 질의어 생성에 사용하면 안 된다.

* DN과 필터에 사용되는 사용자 입력값에는 특수문자가 포함되지 않도록 특수문자를 제거한다. 특수문자를 사용해야 하는 경우 특수문자(DN에 사용되는 특수문자는 `\`), 필터에 사용되는 특수문자(`=, +, <, >, #, ;, \` 등)에 대해서는 실행 명령이 아닌 일반문자로 인식되도록 처리한다.

## 3. 예제
다음의 예제는 외부의 입력(name)이 검색을 위한 필터 문자열의 생성에 사용되고 있다. 이 경우 name 변수의 값으로 `*`을 전달할 경우 필터 문자열은 `name=*`가 되어 항상 참이 되며 이는 의도하지 않은 동작을 유발할 수 있다.

* 안전하지 않은 코드의 예

```java
Properties props = new Properties();
String fileName = "ldap.properties";
FileInputStream in = new FileInputStream(fileName);
props.load(in);
String name = props.getProperty("name");
String filter = "(name =" + name + ")";
NamingEnumeration answer = ctx.search("ou=NewHires", filter, new SearchControls());
printSearchEnumeration(answer);
ctx.close();
```

검색을 위한 필터 문자열로 사용되는 외부의 입력에서 위험한 문자열을 제거하여 위험성을 부분적으로 감소시킬 수 있다.

* 안전한 코드의 예

```java
Properties props = new Properties();
String fileName = "ldap.properties";
FileInputStream in = new FileInputStream(fileName);
if (in == null || in.available() <= 0) return;
props.load(in);
if (props == null || props.isEmpty()) return;
String name = props.getProperty("name");
if (name == null || "".equals(name)) return;
String filter = "(name =" + name.replaceAll("\\*", "") + ")";
NamingEnumeration answer = ctx.search("ou=NewHires", filter, new SearchControls());
printSearchEnumeration(answer);
ctx.close();
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
