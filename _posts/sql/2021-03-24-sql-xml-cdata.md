---
title: SQL XML CDATA
author: dejavuhyo
date: 2021-03-24 06:00:00 +0900
categories: [Database, SQL]
tags: [xml-cdata, sql-data, sql-xml-cdata, cdata]
---

## 1. XML에서 CDATA
XML에서 CDATA 섹션은 태그 문자로 인식될 문자가 포함된 텍스트 블록을 이스케이프 하는 데 사용된다.

XML 파서에서 태그 문자로 처리되는 문자가 포함될 수 있다. 예를 들어 꺾쇠괄호 (< 및 >), 작거나 같음 기호 (<=) 및 앰퍼샌드 (&)는 태그 문자로 처리된다. 하지만 이러한 유형의 특수 문자를 CDATA 섹션에 래핑하여 태그 문자로 처리되지 않도록 할 수 있다. CDATA 섹션 내의 텍스트는 XML 파서에서 일반 텍스트로 처리된다.

## 2. 문법

```text
<![CDATA[
    쿼리
]]>
```

## 3. 쿼리 예시

### 1) SELECT 문

```xml
<select id="select">
    <![CDATA[
    SELECT
        id, name, email, address
    FROM member
    WHERE
        id < 5
    ]]>
</select>
```

### 2) 조건문과 같이 사용

```xml
<select id="select">
    SELECT
        id, name, email, address
    FROM member
    <where>
        <if test="name != null and name != ''">
            AND name = #{name}
            <![CDATA[
            AND id < 5
            ]]>
        </if>
</select>
```

## [출처 및 참고]
* <https://docs.oracle.com/cd/B19306_01/server.102/b14200/functions216.htm>
* <https://docs.microsoft.com/ko-kr/sql/relational-databases/sqlxml-annotated-xsd-schemas-using/creating-cdata-sections-using-sql-use-cdata-sqlxml-4-0?view=sql-server-ver15>
