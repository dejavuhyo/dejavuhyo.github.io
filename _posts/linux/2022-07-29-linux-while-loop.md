---
title: Linux while 루프
author: dejavuhyo
date: 2022-07-29 10:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-while-loop, linux-while, while-loop, while, loop, 리눅스-while-루프, 리눅스-while, 리눅스-루프, while-루프, 루프]
---

## 1. while 루프

### 1) 문법
`while`문의 기본 문법은 다음과 같다. 조건이 참일동안 명령1과 명령2가 순차적으로 반복된다. 명령을 처리 하는 중간에 `if`문과 `continue`, `break`문을 이용하여 `while`문의 처음으로 돌아가거나, 탈출하는 것이 가능하다.

```shell
while [ 조건 ]
do
   명령1
   명령2
done
```

`while`문을 한줄로 작성하는 방법은 다음과 같다.

```shell
while [ 조건 ]; do 명령1; 명령2; done
```

## 2. 사용예제

### 1) 기본 루프 처리
기본적인 루프문 처리는 다음과 같다. number가 2보다 작을 동안(le) 반복된다.

```shell
#!/bin/bash

number=0

while [ $number -le 2 ]
do
  echo "Number: ${number}"
  ((number++))
done
```

### 2) 무한 루프
무한 루프는 다음과 같이 `while :`로 표현한다. `if`문을 이용하여 2보다 커지면 `while`문을 탈출한다.

```shell
#!/bin/bash

number=0

while :
do
  if [ $number -gt 2 ]; then
    break
  fi

  echo "Number: ${number}"
  ((number++))
done
```

### 3) 날짜를 이용한 루프
시작일자(2019.01.01)부터 종료일자 전일(2019.01.31)까지 일자를 출력하는 방법은 다음과 같다.

```shell
#!/bin/bash

startDate=`date +"%Y%m%d" -d "20190101"`
endDate=`date +"%Y%m%d" -d "20190201"`

while [ "$startDate" != "$endDate" ] ; 
do 
    echo $startDate

    startDate=`date +"%Y%m%d" -d "$startDate + 1 day"`; 
done
```

종료일자(2019.02.01)까지 출력하기 위해서는 종료일자에 1을 더하여 `whilte`문 종료조건을 늘려주면 된다.

```shell
#!/bin/bash

startDate=`date +"%Y%m%d" -d "20190101"`
endDate=`date +"%Y%m%d" -d "20190201"`
endDate=`date +"%Y%m%d" -d "${endDate} + 1 day"`

while [ "$startDate" != "$endDate" ] ; 
do 

    echo $startDate

    startDate=`date +"%Y%m%d" -d "$startDate + 1 day"`; 
done
```

## [출처 및 참고]
* [https://wikidocs.net/29981](https://wikidocs.net/29981)
