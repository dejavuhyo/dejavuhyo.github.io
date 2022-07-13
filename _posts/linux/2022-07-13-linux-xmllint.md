---
title: 리눅스 xmllint
author: dejavuhyo
date: 2022-07-13 16:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-xmllint, xmllint, 리눅스-xmllint, 리눅스]
---

## 1. xmllint
xml파일의 문법 확인, xpath를 이용한 데이터 추출을 할 수 있다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:---:|:---:|
| `--format` | xml 문서의 출력을 포맷에 맞춰 처리 |
| `--valid` | xml 문서가 DTD에 맞는지 확인 |
| `--xpath` | xpath 문법에 맞게 데이터를 출력 |

## 3. 사용예제

```shell
# 포맷에 맞게 출력
$ echo "<xml><key>A</key><value>B</value></xml>" | xmllint --format -
<?xml version="1.0"?>
<xml>
  <key>A</key>
  <value>B</value>
</xml>

# sample.xml 파일을 포맷에 맞게 처리
$ xmllint --format sample.xml
<?xml version="1.0"?>
<xml>
    <key>A</key>
    <value>B</value>
</xml>

# sample.xml 이 DTD 에 맞는지 확인
$ xmllint --valid sample.xml
<?xml version="1.0"?>
<xml>
    <key>A</key>
    <value>B</value>
</xml>

$ xmllint --xpath "xml/key" sample.xml
<key>A</key>
```

## [출처 및 참고]
* <https://wikidocs.net/72709>
