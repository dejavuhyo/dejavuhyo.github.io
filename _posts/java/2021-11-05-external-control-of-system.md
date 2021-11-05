---
title: 시스템 또는 구성 설정의 외부 제어
author: dejavuhyo
date: 2021-11-05 06:00:00 +0900
categories: [Language, Java]
tags: [external-control-of-system, configuration-setting, 시스템-외부-제어, 구성-설정의-외부-제어]
---

## 1. 정의
시스템 설정이나 구성요소를 외부에서 제어할 수 있으면 예상치 못한 결과(예: 서비스 중단)를 초래하거나 악용될 가능성이 있다.

## 2. 안전한 코딩 기법

* 외부의 입력을 Connection.setCatalog() 메소드의 인자값을 생성하는데 사용하지 않도록 한다. 불가피하게 사용해야 한다면, 외부의 입력을 화이트리스트 방식으로 검사한 후 사용한다.

## 3. 예제
외부의 입력(catalog)이 JDBC의 활성화된 카탈로그를 설정하는 데 사용되고 있다. 이때 존재하지 않는 카탈로그나 권한이 없는 카탈로그 이름이 전달되면 예외상황을 발생할 수 있다.

* 안전하지 않은 코드의 예

```java
public void setCatalog() {
    try {
        InitialContext ctx = new InitialContext();
        DataSource datasource = (DataSource) ctx.lookup("jdbc:ocl:orcl");
        Connection con = datasource.getConnection();
        Properties props = new Properties();
        String fileName = "file.properties";
        FileInputStream in = new FileInputStream(fileName);
        props.load(in);
        // catalog 정보는 외부로부터 유입되는 정보
        String catalog = props.getProperty("catalog");
        // catalog 정보를 DB Connection을 위해서 해당 값을 체크하지 않고, DB 카탈로그 정보에 지정함
        con.setCatalog(catalog);
        con.close();
    } catch (SQLException ex) {
        System.err.println("SQLException Occured");
    } catch (NamingException e) {
        System.err.println("NamingException Occured");
    } catch (FileNotFoundException e) {
        System.err.println("FileNotFoundException Occured");
    } catch (IOException e) {
        System.err.println("IOException Occured");
    }
}
```

외부의 입력값에 따라 카탈로그 이름이 바뀌어야 할 경우에는 해당 문자열을 직접 사용하지 말고, 미리 정의된 적절한 카탈로그 이름 중에 선택하여 사용한다.

* 안전한 코드의 예

```java
public void setCatalog() {
    try {
        // caltalog 값으로 c1과 c2를 사용할 경우
        InitialContext ctx = new InitialContext();
        DataSource datasource = (DataSource) ctx.lookup("jdbc:ocl:orcl");
        Connection con = datasource.getConnection();
        
        Properties props = new Properties();
        String fileName= "file.properties";
        String catalog;
        
        FileInputStream in = new FileInputStream(fileName);
        if (in != null && in.available() > 0) {
            props.load(in);
            
            if (props == null || props.isEmpty()) catalog = "c1";
            else
            catalog = props.getProperty("catalog");
        } else {
            catalog = "c1";
        }

        // 외부 유입 변수(catalog)에 대하서 값을 반드시 체크하고 걸러야 한다.
        if ("c1".equals(catalog))
            con.setCatalog("c1");
        else
            con.setCatalog("c2");
            con.close();
    } catch (SQLException ex) {
        System.err.println("SQLException Occured");
    } catch (NamingException e) {
        System.err.println("NamingException Occured");
    } catch (FileNotFoundException e) {
        System.err.println("FileNotFoundException Occured");
    } catch (IOException e) {
        System.err.println("IOException Occured");
    }
}
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>

{% include adsense.html %}
