---
title: 리눅스 split
author: dejavuhyo
date: 2022-06-10 12:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-split, split, 리눅스-split, 리눅스]
---

## 1. split
파일을 분할 한다. 파일을 지정한 행의 길이나 사이즈로 분할한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|-----|-----|
| `-l 100` | 100 라인 단위로 분할한다. 기본값은 1000 이다. |
| `-d` | 숫자로 된 이름의 파일로 분할한다. 기본값은 영문이다. |
| `-a` | 분할된 파일 딜리미터의 크기를 설정한다. |
| `-b` | 지정한 사이즈로 파일을 분할한다. |

## 3. 사용예제

```shell
# origintxt 파일을 100줄 단위로 분할한다.
$ split -l 100 origin.txt
xaa
xab
xac

# origintxt 파일을 100줄 단위로 나누는데, 파일이름을 숫자형식으로 분할한다.
$ split -l 100 -d origin.txt
x01
x02
x03

# origintxt 파일을 100줄 단위로 나누는데, 파일이름을 숫자형식으로 하고, 파일이름의 딜리미터 크기는 4이다.
$ split -l 100 -d -a 4 origin.txt
x0001
x0002
x0003
```

### 1) tar 압축 파일 분할

```shell
# hadoop-2.10.1.tar.gz 파일을 30메가 단위로 분할하고, 파일 prefix 지정
$ split -b 30m hadoop-2.10.1.tar.gz hadoop-2.10.1.tar.gz.split.

$ ls -alh
hadoop-2.10.1.tar.gz.split.aa
hadoop-2.10.1.tar.gz.split.ab
hadoop-2.10.1.tar.gz.split.ac
hadoop-2.10.1.tar.gz.split.ad
hadoop-2.10.1.tar.gz.split.ae
hadoop-2.10.1.tar.gz.split.af
hadoop-2.10.1.tar.gz.split.ag
hadoop-2.10.1.tar.gz.split.ah
hadoop-2.10.1.tar.gz.split.ai
hadoop-2.10.1.tar.gz.split.aj
hadoop-2.10.1.tar.gz.split.ak
hadoop-2.10.1.tar.gz.split.al
hadoop-2.10.1.tar.gz.split.am

# 분할 된 압축 파일 합치기
$ cat hadoop-2.10.1.tar.gz.split.* > hadoop-2.10.1.tar.gz
```

## [출처 및 참고]
* <https://wikidocs.net/48068>
