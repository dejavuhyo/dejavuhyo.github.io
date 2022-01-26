---
title: MyBatis snake_case와 CamelCase 매핑 자동화
author: dejavuhyo
date: 2021-03-23 06:00:00 +0900
categories: [DevOps, MyBatis]
tags: [mybatis-snakecase, mybatis-camelcase, snakecase-camelcase, camelcase-from-snakecase, snakecase-camelcase-매핑]
---

## 1. 데이터 테이블

* test_table

| test_id | test_value |
|:---:|:---:|
| 1 | hoge |
| 2 | fuga |
| 3 | piyo |

## 2. 소스 코드

### 1) TestTable.java

```java
package sample.mybatis;

public class TestTable {
    private int id;
    private String testValue;

    @Override
    public String toString() {
        return "TestTable [id=" + id + ", testValue=" + testValue + "]";
    }
}
```

### 2) mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
  </settings>

  ...
</configuration>
```

### 3) sample_mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="sample.mybatis">

    <select id="selectTest" resultType="sample.mybatis.TestTable">
        select * from test_table
    </select>
</mapper>
```

## 3. 결과
전통적인 데이터베이스 칼럼명 형태인 A_COLUMN을 CamelCase형태의 자바 프로퍼티명 형태인 aColumn으로 자동으로 매핑하도록 함

```text
[testId=1, testValue=hoge]
[testId=2, testValue=fuga]
[testId=3, testValue=piyo]
```

## [출처 및 참고]
* <https://mybatis.org/mybatis-3/ko/configuration.html#settings>
* <http://www.devkuma.com/books/pages/744>
