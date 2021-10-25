---
title: XPath 삽입
author: dejavuhyo
date: 2021-10-01 06:00:00 +0900
categories: [Language, Java]
tags: [xpath-injection, xpath-삽입]
---

## 1. 정의
외부 입력값을 적절한 검사과정 없이 XPath 쿼리문 생성을 위한 문자열로 사용하면, 공격자는 프로그래머가 의도하지 않았던 문자열을 전달하여 쿼리문의 의미를 왜곡시키거나 그 구조를 변경하고 임의의 쿼리를 샐행하여 인가되지 않은 데이터를 열람할 수 있다.

![xpath-injection](/assets/img/2021-10-01-xpath-injection/xpath-injection.png)

## 2. 안전한 코딩 기법

* XPath 쿼리에 사용되는 외부 입력 데이터에 대하여 특수문자(```", [, ], /, =, @ 등 ```) 및 쿼리 예약어 필터링을 수행하고 인자화된 쿼리문을 지원하는 XQuery를 사용한다.

## 3. 예제
다음의 예제에서 name의 값으로 user1, passwd의 값으로 ```'or ''='```을 전달하면 다음과 같은 질의문이 생성되어 인증과정을 거치지 않고 로그인할 수 있다.

```//users/user[login/text()=‘user1' or ''='' and password/text() = “ or ”=“]/home_dir/text()```

* 안전하지 않은 코드의 예

```java
// 외부로 부터 입력을 받음
String name = props.getProperty("name");
String passwd = props.getProperty("password");

XPathFactory factory = XPathFactory.newInstance();
XPath xpath = factory.newXPath();

// 외부 입력이 XPath의 인자로 사용
XPathExpression expr = xpath.compile("//users/user[login/text()='" + name + "' and password/text() = '" + passwd + "']/home_dir/text()");
Object result = expr.evaluate(doc, XPathConstants.NODESET);
NodeList nodes = (NodeList) result;
for (int i = 0; i < nodes.getLength(); i++) {
    String value = nodes.item(i).getNodeValue();
    if (value.indexOf(">") < 0) {
        System.out.println(value);
    }
}
```

다음의 예제와 같이 인자화된 질의문을 지원하는 XQuery를 사용하여 미리 질의 골격을 생성하고 이에 인자값을 설정함으로써 외부의 입력으로 인하여 질의 구조가 바뀌는 것을 막을 수 있다.

* 안전한 코드의 예

```java
// dologin.xp 파일
declare variable $loginID as xs:string external;
declare variable $password as xs:string external;
//users/user[@loginID=$loginID and @password=$password]
```

```java
// XQuery를 이용한 XPath Injection 방지
// 외부로 부터 입력을 받음
String name = props.getProperty("name");
String passwd = props.getProperty("password");
Document doc = new Builder().build("users.xml");
// XQuery를 위한 정보 로딩
XQuery xquery = new XQueryFactory().createXQuery(new File("dologin.xq"));
Map vars = new HashMap();
vars.put("loginID", name);
vars.put("password", passwd);
Nodes results = xquery.execute(doc, null, vars).toNodes();
for (int i=0; i < results.size(); i++) {
 System.out.println(results.get(i).toXML());
}
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
