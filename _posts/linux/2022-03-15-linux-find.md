---
title: Linux find
author: dejavuhyo
date: 2022-03-15 10:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-find, find, 리눅스-find]
---

## 1. find
파일을 검색할 때 사용한다.

## 2. 주요 옵션

| 옵션 | 내용 |
|:-----:|:-----:|
| -name | 검색할 이름을 지정. *도 사용가능 |
| -exec | 찾은 파일을 이용해서 다른 명령을 실행해야 할 때 사용 |
| -mtime | 수정시간을 이용하여 검색. n*24시간 전 수정파일 |
| -type | 찾는 파일의 타입을 지정 |

### 1) type 옵션

| 옵션 | 내용 |
|:-----:|:-----:|
| b | 블록형 특수 파일 |
| c | 캐릭터형 특수 파일 |
| d | 디렉토리 |
| f | 일반 파일 |
| l | 심볼릭 링크 |
| p | 파이프 |
| s | 소켓 |

## 3. 사용예제

### 1) 검색

```shell
# txt로 끝나는 파일 검색 
$ find ./ -name "*.txt"

# logs 폴더 아래에서 txt로 끝나는 파일 검색 
$ find ./logs -name "*.txt"

# 현재 폴더에서 com으로 시작하는 파일(f)만 검색 
$ find ./ -name "com*" -type f
```

### 2) 검색후 추가 작업

```shell
# logs 폴더 아래에서 log로 끝나는 파일을 찾이서 log.tar.gz 으로 압축 
$ find ./logs -name "*.log" -exec tar -czf "./logs/log.tar.gz" {} \;
```

### 3) 삭제 관련

```shell
# logs 폴더 아래에서 20190101이 있는 파일을 찾아서 삭제(rm) 
$ find ./logs/ -name "*20190101*" -exec rm -rf {} \;

# logs 폴더 아래에서 20190101이 있는 파일을 찾아서 삭제하는데, tar.gz 으로 끝나는 파일은 제외 
$ find ./logs/ -name "*20190101*" -not -name "*.tar.gz" -exec rm -rf {} \;

# logs 폴더 아래에서 log로 끝나는 파일이면서, 일주일동안 수정이 없던 파일을 삭제 
$ find ./logs/ -name "*.log" -mtime +6 -delete
```

## [출처 및 참고]
* [https://wikidocs.net/47974](https://wikidocs.net/47974)
