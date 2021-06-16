---
title: MariaDB 날짜 및 시간 차이 계산
date: 2021-06-17 06:00:00 +0900
categories: [Database, MySQL]
tags: [datediff, timestampdiff, datediff-timestampdiff, 날짜-차이, 시간-차이, 날짜-시간-차이]
---

## 1. 날짜 차이 계산
DATEDIFF(expr1, expr2)

```sql
SELECT DATEDIFF('2021-06-18', '2021-06-17') AS DIFF_DATE
```

## 2. 시간 차이 계산
TIMESTAMPDIFF(시간 표현단위, 시작 시간, 종료 시간)

* 시간 표현단위
  - SECOND: 초
  - MINUTE: 분
  - HOUR: 시
  - DAY: 일
  - WEEK: 주
  - MONTH: 월
  - QUARTER: 분기
  - YEAR: 연

```sql
SELECT TIMESTAMPDIFF(minute, '2021-06-18 06:00:00', '2021-06-17 06:00:00') AS DIFF_TIME
```

## 3. 시간 차이 계산 주의사항
문자열을 바로 계산하면 정확한 결과를 얻지 못 할 수도 있다. DATE 형식으로 변경하면 정확한 결과를 얻을 수 있다.

```sql
SELECT TIMESTAMPDIFF(minute, date_format('2021-06-17 06:00:00', '%Y-%m-%d %H:%i:%s'), date_format('2021-06-18 06:00:00', '%Y-%m-%d %H:%i:%s')) AS DIFF_TIME
```

## [출처 및 참고]
* <https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=cacung82&logNo=220688790307>
