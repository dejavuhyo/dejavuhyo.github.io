---
title: 리눅스 gzip, gunzip, zcat
author: dejavuhyo
date: 2022-03-24 12:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-gzip, linux-gunzip, linux-zcat, 리눅스-gzip, 리눅스-gunzip, 리눅스-zcat]
---

## 1. gzip, gunzip, zcat
gzip은 파일을 압축할 때, gunzip은 파일의 압축을 해제할 때 사용한다.

압축한 파일은 `.gz`이 붙고 기존 파일은 삭제된다. 파일, 디렉토리 단위로 압축되므로 여러개의 파일을 압축하기 위해서는 tar 명령을 이용해야 한다.

## 2. 주요 옵션

| 옵션 | 내용 |
|-----|-----|
| -r | 디렉토리를 압축 |
| -d | 압축을 해제. gunzip과 같은 동작 |
| -l | 압축 정보를 표시 |
| -c | gzip 파일을 읽어서 표준 출력으로 출력. zcat과 동일하게 동작 |

## 3. 사용예제

### 1) 파일 압축

```shell
# sample.txt 파일을 압축하면 sample.txt.gz 파일이 생성
$ ls
sample.txt
$ gzip sample.txt
$ ls
sample.txt.gz
```

### 2) 압축 정보 확인

```shell
$ gzip -l sample.txt.gz
         compressed        uncompressed  ratio uncompressed_name
                278                 412  38.1% work
```

### 3) 파일 압축 해제

```shell
# sample.txt.gz 파일의 압축을 해재하면 sample.txt 파일 생성
$ ls
sample.txt.gz

# 다음의 두개의 명령은 동일한 결과를 나타낸다.
$ gunzip sample.txt.gz
$ gzip -d sample.txt.gz

$ ls
sample.txt
```

### 4) 파이프를 이용한 압축 해제
압축파일을 읽어서 표준 입력으로 받아 들여서 압축을 해제할 수도 있다.

```shell
$ cat sample.txt.gz | gzip -d
```

### 5) 압축 파일 읽기
압축파일을 읽어서 표준 출력으로 출력한다. `gzip -c`, `zcat`은 동일하게 동작한다.

```shell
$ gunzip -c sample.txt.gz
a, b, c

$ zcat sample.txt.gz
a, b, c
```

## [출처 및 참고]
* <https://wikidocs.net/33341>
