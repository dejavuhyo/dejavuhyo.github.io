---
title: 리눅스 file
author: dejavuhyo
date: 2022-05-31 10:40:00 +0900
categories: [DevOps, Linux]
tags: [linux-file, file, 리눅스-file, 리눅스-파일]
---

## 1. file
파일의 타입을 확인한다. 텍스트파일, 실행파일, 아카이브 파일인지 등의 파일 타입을 확인한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|-----|-----|
| `-z` | 파일이 압축되어 있는 경우 압축되기 전의 파일 타입을 확인 |

## 3. 사용예제

```shell
# 텍스트 파일 확인
$ file ANSI.txt
ANSI.txt: ISO-8859 text, with no line terminators

# 텍스트 파일 확인. 문자 인코딩 방식 확인 가능
$ file Unicode.txt
Unicode.txt: Little-endian UTF-16 Unicode text, with no line terminators

# 디렉토리 확인
$ file oozie
oozie: directory

# 압축파일 확인
$ file oozie.tar.gz
oozie.tar.gz: data

# 현재 디렉토리의 모든 파일 확인
$ file *
cctv.jar:                 Zip archive data, at least v1.0 to extract
```

## [출처 및 참고]
* <https://wikidocs.net/48071>
