---
title: SQL 삽입공격
date: 2021-10-18 06:00:00 +0900
categories: [Language, Java]
tags: [sql-injection, java-data-objects, sql-삽입공격]
---

## 1. 정의
외부의 신뢰할 수 없는 입력을 적절한 검사 과정을 거치지 않고 JDO(Java Data Objects) API의 SQL 또는 JDoQL 질의문 생성을 위한 문자열로 사용하면, 공격자가 프로그래머가 의도하지 않았던 문자열을 전달함으로써 질의문의 의미를 왜곡시키거나 그 구조를 변경하여 임의의 질의 명령어를 수행할 수 있다.

## 2. 안전한 코딩 기법

* JDO 질의문의 생성 시에는 상수 문자열만을 사용하고 Query.execute() 실행 시에는 인자 값을 전달하는 방법(Parameterize Query)을 사용한다.

## 3. 예제
공격자가 외부의 입력(name)값을 ```name'; DROP MYTABLE; -```로 주게 되면, 다음과 같은 질의문이 수행되어 테이블이 삭제된다.

```SELECT col1 FROM MYTABLE WHERE name = 'name'; DROP MYTABLE; -'```

* 안전하지 않은 코드의 예

```java
public class ContactItem implements ContactDAO {
    public List<Contact> listContacts() {
        PersistenceManager pm = getPersistenceManagerFactory().getPersistenceManager();
        String query = "select from " + Contact.class.getName();
        try {
            Properties props = new Properties();
            String fileName = "contacts.txt";
            FileInputStream in = new FileInputStream(fileName);
            if( in != null ) { props.load(in); }
            in.close();
            // 외부로 부터 입력을 받는다.
            String name = props.getProperty("name");
            if( name != null ) {
                query += " where name = '" + name + "'";
            }
        }
        catch (IOException e) {}

        // 외부 입력값이 JDO 객체의 인자로 사용된다.
        return (List<Contact>) pm.newQuery(query).execute();
    }
}
```

외부 입력 부분을 ```?```로 설정하고(Parameterize Query), 실행 시에 해당 인자값이 전달되도록 수정함으로써 외부의 입력(name)이 질문의 구조를 변경시키는 것을 방지할 수 있다.

* 안전한 코드의 예

```java
public class ContactItem implements ContactDAO {
    public List<Contact> listContacts() {
        PersistenceManager pm = getPersistenceManagerFactory().getPersistenceManager();
        String query = "select from " + Contact.class.getName();
        String name = "";
        try {
            Properties props = new Properties();
            String fileName = "contacts.txt";
            FileInputStream in = new FileInputStream(fileName);
            props.load(in);
            // 􁹦􁕮􁁊􀀁􁕮􂤞􀀁􁽳􁀓􁼲􀀁􁐉􀪂􀫒.
            name = props.getProperty("name");
            // 􁽳􁀓􀐀􁼲􀀁􁿾􀑮􂵊􀫒.
            if (name == null || "".equals(name)) return null;
            query += " where name = ?";
        }
        catch (IOException e) {}

        javax.jdo.Query q = pm.newQuery(query);
        // Query API􁽆􀀁􁽦􁽾􁁊􀀁􁢚􁺗􂵊􀫒.
        return (List<Contact>) q.execute(name);
    }
}
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
