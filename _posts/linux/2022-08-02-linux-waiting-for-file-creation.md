---
title: Linux 파일 생성 대기
author: dejavuhyo
date: 2022-08-02 13:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-waiting-for-file-creation, linux-file-creation, linux-waiting, linux-file, file-creation, 리눅스-파일-생성-대기, 리눅스-파일-생성, 파일-생성]
---

## 1. 파일 생성 대기
스크립트를 이용해서 작업할 때 특정 파일이 생성되는 것을 대기했다가 작업을 처리해야 하는 경우가 있다. 이럴 때는 `while`문과 `sleep` 명령을 이용해서 파일이 생기는 것을 대기했다가 작업을 처리하면 된다.

## 2. 사용 예제

```shell
#!/bin/bash

vFileLocation="s3://bucket-name/sample.file"

# vFileLocation을 2분에 한번씩 체크해서 파일이 생기면 종료 
while :
do
    vCount=`aws s3 ls ${vFileLocation} | wc -l`
    if [[ "${vCount}" == "1" ]]; then
        echo "file check done."
        break
    fi

    echo "file waiting..."
    sleep 2m
done


# [목적 명령어]
```

## [출처 및 참고]
* [https://wikidocs.net/87085](https://wikidocs.net/87085)
