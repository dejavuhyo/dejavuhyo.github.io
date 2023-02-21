---
title: MyBatis CDATA
author: dejavuhyo
date: 2022-07-07 16:00:00 +0900
categories: [DevOps, MyBatis]
tags: [mybatis-cdata, mybatis, cdata, mybatis-문자열, mybatis-특수문자]
---

## 1. CDATA 란
Character DATA, 즉 문자열 데이터를 말한다.

쿼리를 작성할 때, `<`, `>`, `&`를 사용해야하는 경우 xml에서 태그로 인식한다. 에러가 나기 때문에 '태그가 아니라 실제 쿼리에 필요한 코드'라고 알려줘야 한다.

죽, XML parser에 의해 해석하지말고 그대로 브라우저에 출력하라는 뜻이다.

## 2. 예시

### 1) `<![CDATA[ ]]>` 태그를 사용

```sql
SELECT * FROM DUAL WHERE A <!CDATA[ > ]]> B;
```

### 2) `<![CDATA[ ]]>` 태그를 사용하지 않음

```sql
SELECT * FROM DUAL WHERE A &gt B;
```

위의 코드처럼 `<!CDATA[ ]]>`를 사용하지 않고 표현하는 방법이 있지만 HTML 특수문자 코드를 알아야 한다는 번거로움이 있다.

## 3. 주의점
xml에서 sqlMap이나 Mapper를 사용할 경우 동적(다이나믹) 쿼리에서 `<!CDATA[ ]]>`를 사용하게 되면 해당 코드에는 동적 쿼리를 사용할 수 없다.

동적 쿼리를 사용해야 하는 경우, 쿼리문 전체가 아닌 특수문자가 있는 코드에만 `<!CDATA[ ]]>`를 사용하면 동적 쿼리도 문제없이 사용할 수 있다.

## [출처 및 참고]
* [https://sdevstudy.tistory.com/6](https://sdevstudy.tistory.com/6)
