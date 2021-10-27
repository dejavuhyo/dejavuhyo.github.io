---
title: 메시지 데이터 모델링
author: dejavuhyo
date: 2021-10-27 06:00:00 +0900
categories: [Application, Modeling]
tags: [message-data-modeling, message-modeling, message-erd, 메시지-데이터-모델링, 메시지-모델링, 메시지-erd]
---

## 1. 메시지 데이터 모델링
내ID와 상대방ID 개념을 도입하여 보내고 받는 행위를 '수발신구분코드'로 변경하면 깔끔한 쿼리를 얻을 수 있다.

* 문서함 ERD

![message-erd](/assets/img/2021-10-27-message-data-modeling/message-erd.png)

> 인덱스: 사용자ID_발신, 수발신구분코드, 발신일시

> 내가 쪽지 한번 보낼 때마다 __'나, 상대방, 발신'__, __'나, 상대방, 수신'__ 두 건의 데이터를 넣어야 한다.

* 받은 문서함 쿼리

```sql
SELECT 사용자ID_수신, 쪽지내용, 발송일시, 열람일시
FORM 쪽지
WHERE 수발신구분코드 = '수신'
AND 사용자ID_발신 = '나'
ORDER BY 발송일시 DESC
```

* 보낸 문서함 쿼리

```sql
SELECT 사용자ID_발신, 쪽지내용, 발송일시, 열람일시
FORM 쪽지
WHERE 수발신구분코드 = '발신'
AND 사용자ID_발신 = '나'
ORDER BY 발송일시 DESC
```

## [출처 및 참고]
* <https://meetup.toast.com/posts/108>
