---
title: XQuery 삽입
author: dejavuhyo
date: 2021-09-30 06:00:00 +0900
categories: [Language, Java]
tags: [xquery-injection, xquery-삽입]
---

## 1. 정의
XQuery를 사용하여 XML 데이터에 대한 동적 쿼리문을 생성할 때 사용하는 외부 입력값에 대해 적절한 검증 절차가 존재하지 않으면 공격자가 쿼리문의 구조를 임의로 변경할 수 있게 된다. 이로 인해 허가되지 않은 데이터를 조회하거나 인증 절차를 우회할 수 있다.

## 2. 안전한 코딩 기법

* XQuery에 사용되는 외부 입력데이터에 대하여 특수문자 및 쿼리 예약어를 필터링하고, XQuery를 사용한 쿼리문은 스트링을 연결하는 형태로 구성하지 않고 인자(파라메터)화된 쿼리문을 사용한다.

## 3. 예제
다음의 예제는 외부의 입력(name)값을 executeQuery를 사용한 질의생성의 문자열 인자 생성에 사용하고 있다. 만일 다음과 `something' or '='1`을 name의 값으로 전달하면 다음과 같은 질의문을 수행할 수 있으며, 이를 통해 파일 내의 모든 값을 출력할 수 있게 된다.

```text
doc('users.xml')/userlist/user[uname='something' or '='
```

* 안전하지 않은 코드의 예

```java
// 외부로 부터 입력을 받음
String name = props.getProperty("name");
Hashtable env = new Hashtable();
env.put(Context.INITIAL_CONTEXT_FACTORY, "com.sun.jndi.ldap.LdapCtxFactory");
env.put(Context.PROVIDER_URL, "ldap://localhost:389/o=rootDir");
javax.naming.directory.DirContext ctx = new InitialDirContext(env);
javax.xml.xquery.XQDataSource xqds = (javax.xml.xquery.XQDataSource) ctx.lookup("xqj/personnel");
javax.xml.xquery.XQConnection conn = xqds.getConnection();

String es = "doc('users.xml')/userlist/user[uname='" + name + "']";
// 입력값이 XQuery의 인자로 사용
XQPreparedExpression expr = conn.prepareExpression(es);
XQResultSequence result = expr.executeQuery();
while (result.next()) {
    String str = result.getAtomicValue();
    if (str.indexOf('>') < 0) {
        System.out.println(str);
    }
}
```

다음의 예제에서는 외부 입력값을 받을 때 해당 값 기반의 XQuery상의 쿼리 구조를 변경시키지 않는 bindXXX 함수를 이용함으로써 외부의 입력으로 인하여 쿼리 구조가 바뀌는 것을 막을 수 있다.

* 안전한 코드의 예

```java
// 외부로 부터 입력을 받음
String name = props.getProperty("name");
Hashtable env = new Hashtable();
env.put(Context.INITIAL_CONTEXT_FACTORY, "com.sun.jndi.ldap.LdapCtxFactory");
env.put(Context.PROVIDER_URL, "ldap://localhost:389/o=rootDir");
javax.naming.directory.DirContext ctx = new InitialDirContext(env);
javax.xml.xquery.XQDataSource xqds = (javax.xml.xquery.XQDataSource) ctx.lookup("xqj/personnel");
javax.xml.xquery.XQConnection conn = xqds.getConnection();

String es = "doc('users.xml')/userlist/user[uname='$xpathname']";
// 입력값이 XQuery의 인자로 사용
XQPreparedExpression expr = conn.prepareExpression(es);
expr.bindString(new QName("xpathname"), name, null);
XQResultSequence result = expr.executeQuery();
while (result.next()) {
    String str = result.getAtomicValue();
    if (str.indexOf('>') < 0) {
        System.out.println(str);
    }
}
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
