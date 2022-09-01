---
title: 리눅스 셸 스크립트 문자열
author: dejavuhyo
date: 2022-07-28 16:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-shell-script-string, shell-script-string, linux-shell-script, linux-string, 리눅스-셸-스크립트-문자열, 셸-스크립트-문자열, 리눅스-셸-스크립트, 리눅스-문자열, 쉘-스크립트-문자열, 리눅스-쉘-스크립트]
---

## 1. 문자열

### 1) Uppercase, Lowercase
문자열의 대문자화와 소문자화는 다음과 같이 처리한다.

```shell
#!/bin/bash

str="abcd"
echo ${str^^}
echo ${str,,}
```

### 2) 문자열 변경(replace)

| 명령 | 비고 |
|-----|-----|
| `${변수명/문자A/문자B}` | 첫번째 문자A를 문자B로 변경 |
| `${변수명//문자A/문자B}` | 모든 문자A를 문자B로 변경 |

```shell
vParam=a.b

$ echo ${vParam/./_}
a_b

$ echo ${vParam//./_}
a_b
```

## [출처 및 참고]
* <https://wikidocs.net/29933>
