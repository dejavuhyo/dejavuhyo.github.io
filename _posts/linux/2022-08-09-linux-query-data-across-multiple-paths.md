---
title: Linux 여러 경로의 데이터 조회
author: dejavuhyo
date: 2022-08-09 09:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-multiple-paths, multiple-paths, linux-query-data, 리눅스-여러-경로, 리눅스-데이터-조회, 데이터-조회]
---

## 1. 여러 경로의 데이터 조회
스크립트를 이용하여 여러 경로의 데이터를 한번에 조회하거나, 다른 처리를 해야 하는 경우가 있다. 이럴 때는 아래와 같이 사용한다.

## 2. 사용 예제
다음은 `vTargetDate`, `vTargetHour`를 입력 받거나, 자동으로 설정되어 `vLocations`의 여러 경로의 데이터를 aws 커맨드로 조회할 때 사용하는 예제이다.

```shell
#!/bin/bash

# 사용자가 TargetDate, TargetHour를 입력하지 않으면 자동으로 설정 
if [ $# == 3 ]; then
    vTargetDate=$1
    vTargetHour=$2
else 
    vTargetDate=`date -d -1hour -u +\%Y\%m\%d` 
    vTargetHour=`date -d -1hour -u +\%H`
fi

echo "TargetDate: "${vTargetDate}
echo "TargetHour: "${vTargetHour}

# 여러 경로를 한번에 확인할 때 
vLocations=(/A/B/C/D/${vTargetDate}/${vTargetHour}/
/A/B/C/D/${vTargetDate}/${vTargetHour}/
/A/B/C/D/${vTargetDate}/${vTargetHour}/)

for vLocation in "${vLocations[@]}"
do
    aws s3 ls s3://bucket-name/${vLocation}
done
```

## [출처 및 참고]
* <https://wikidocs.net/87084>
