--- 
title: Linux에서 tar, gz, zip 압축 및 압축 해제
author: Hyosik
date: 2020-11-16 09:30:00 +0900
categories: [OS, Linux]
tags: [tar, gz, zip, tar-gz, 압축, 압축-해제]
---

## 1. 압축

### 1) tar 압축
tar -cvf [파일명.tar] [폴더명]

```shell
# abc 폴더를 abc.tar로 압축
[root@localhost ~]# tar -cvf abc.tar abc
```

### 2) tar.gz 압축
tar -zcvf [파일명.tar.gz] [폴더명]

```shell
# abc 폴더를 abc.tar.gz로 압축
[root@localhost ~]# tar -zcvf abc.tar abc
```

### 3) zip 압축
zip [파일명.zip] [폴더명]

```shell
# 해당 폴더로 이동한 후 압축 권장

# 현재 폴더 전체를 abc.zip으로 압축
[root@localhost ~]# zip abc.zip ./*

# 현재 폴더 전체와 하위 폴더 전체를 abc.zip으로 압축
[root@localhost ~]# zip abc.zip -r ./*
```

## 2. 압축 해제

### 1) tar 압축 해제
tar -xcf [파일명.tar]

```shell
# abc.tar 파일 압축 해제
[root@localhost ~]# tar -xvf abc.tar
```

### 2) tar.gz 압축 해제
tar -zxvf [파일명.tar.gz]

```shell
# abc.tar.gz 파일 압축 해제
[root@localhost ~]# tar -zxvf abc.tar.gz
```

### 3) zip 압축 해제
unzip [파일명.zip]

```shell
# abc.zip 파일 압축 해제
[root@localhost ~]# unzip abc.zip

# 특정 폴더에 압축 해제
[root@localhost ~]# unzip abc.zip -d ./target
```

## 3. tar 명령의 주요 옵션
*tar --help* 명령을 통해 자세한 내용 확인

| 옵션 | 설명 |
|:---:|:---:|
| -f | 파일 이름 지정 |
| -c | 파일을 tar로 압축 |
| -x | tar 파일 압축 해제 |
| -v | 압축 및 압축 해제시 과정 출력 |
| -z | gzip으로 압축 하거나 압축 해제 |
| -t | tar 파일에 포함된 내용 확인 |
| -k | 압축 해지시 기존 파일 유지 |
| -U | 압축 해시전 기존 파일 삭제 |
| -C | 경로 지정 |
| -A | tar 파일에 선택한 파일 추가 |
| -p | 파일 권한 저장 |

## [출처 및 참고]
* <https://brownbears.tistory.com/161>
* <https://recipes4dev.tistory.com/146>
