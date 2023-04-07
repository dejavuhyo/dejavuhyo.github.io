---
title: Mybatis if 문에서 Integer 공백 체크
author: dejavuhyo
date: 2021-07-16 05:00:00 +0900
categories: [Framework, MyBatis]
tags: [mybatis-if-integer-space, mybatis-if-space, mybatis-space, mybatis-integer-space, integer-space, mybatis-if문-공백, mybatis-공백, mybatis-integer-공백]
---

## 1. 이슈 사항
MyBatis의 다이나믹쿼리 if 문에서 공백 체크를 할 때 숫자 '0'이 체크가 안 되는 경우가 있다.

```xml
SELECT * FROM dual
<where>
    <if test="num != null and num != ''">
        AND NUM = #{num}
    </if>
</where>
```

num 변수의 데이터형이 Integer일 때, num 값이 0이라면 `num != ''`로 인식해서 원하는 조건 검색이 실행되지 않는다.

## 2. 해결 방법

### 1) String Type 사용
num을 String Type으로 전송 받는다.

### 2) equals 사용
`num != ''`대신 `num.equals('')`를 사용한다.

### 3) 커스텀 함수 사용

* StringUtils.java

```java
public class StringUtils {
    public static boolean isEmpty(Object obj) {
        if (obj == null) {
            return true;
        }

        if ((obj instanceof String) && (((String) obj).trim().length() == 0)) {
            return true;
        }

        if (obj instanceof Map) {
            return ((Map<?, ?>) obj).isEmpty();
        }

        if (obj instanceof List) {
            return ((List<?>) obj).isEmpty();
        }

        if (obj instanceof Object[]) {
            return (((Object[]) obj).length == 0);
        }

        return false;
    }
}
```

* Mapper.xml

```xml
SELECT * FROM dual
<where>
    <if test="@org.apache.commons.lang3.StringUtils@isNotEmpty(num)">
        AND NUM = #{num}
    </if>
</where>
```

## [출처 및 참고]
* [https://linked2ev.github.io/mybatis/2020/01/05/MyBatis-6-if-%EB%AC%B8-%EB%8D%B0%EC%9D%B4%ED%84%B0%ED%98%95-Interger-%EC%9D%BC-%EA%B2%BD%EC%9A%B0-%EA%B3%B5%EB%B0%B1%EC%B2%B4%ED%81%AC-%EC%9D%B4%EC%8A%88/](https://linked2ev.github.io/mybatis/2020/01/05/MyBatis-6-if-%EB%AC%B8-%EB%8D%B0%EC%9D%B4%ED%84%B0%ED%98%95-Interger-%EC%9D%BC-%EA%B2%BD%EC%9A%B0-%EA%B3%B5%EB%B0%B1%EC%B2%B4%ED%81%AC-%EC%9D%B4%EC%8A%88/)
* [https://ilikesunshine.tistory.com/entry/MyBatis-%EC%9D%98-%EB%8B%A4%EC%9D%B4%EB%82%98%EB%AF%B9%EC%BF%BC%EB%A6%AC-IF-%EB%AC%B8%EC%97%90%EC%84%9C-%EA%B3%B5%EB%B0%B1%EC%B2%B4%ED%81%AC%EB%A5%BC-%ED%95%A0-%EB%95%8C-%EC%88%AB%EC%9E%90-0%EC%9D%B4-%EC%B2%B4%ED%81%AC%EA%B0%80-%EC%95%88%EB%90%98%EB%8A%94-%EC%9D%B4%EC%8A%88%EA%B0%80-%EC%9E%88%EB%8B%A4](https://ilikesunshine.tistory.com/entry/MyBatis-%EC%9D%98-%EB%8B%A4%EC%9D%B4%EB%82%98%EB%AF%B9%EC%BF%BC%EB%A6%AC-IF-%EB%AC%B8%EC%97%90%EC%84%9C-%EA%B3%B5%EB%B0%B1%EC%B2%B4%ED%81%AC%EB%A5%BC-%ED%95%A0-%EB%95%8C-%EC%88%AB%EC%9E%90-0%EC%9D%B4-%EC%B2%B4%ED%81%AC%EA%B0%80-%EC%95%88%EB%90%98%EB%8A%94-%EC%9D%B4%EC%8A%88%EA%B0%80-%EC%9E%88%EB%8B%A4)
