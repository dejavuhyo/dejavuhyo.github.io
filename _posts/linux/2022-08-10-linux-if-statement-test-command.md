---
title: 리눅스 if문 테스트 명령
author: dejavuhyo
date: 2022-08-10 11:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-if, if-statement-test-command, if-test-command, 리눅스-if, 리눅스-if문-테스트, if문-테스트]
---

## 1. if문 테스트 명령
다음의 표현은 if문에서 사용할 수 있다.

| 표현 | 비고 |
|-----|-----|
| `-e` | 파일이 존재하면 true |
| `-f` | 일반 파일 이면 true |
| `-s` | 파일이 0 사이즈가 아니면 true |
| `-d` | 디렉토리면 true |
| `-b` | 블록 디바이스면 true |
| `-c` | 캐릭터 디바이스면 true |
| `-L` | 심볼링 링크면 true |
| `-r` | 읽기 권한이 있으면 true |
| `-w` | 쓰기 권한이 있으면 true |
| `-x` | 실행 권한이 있으면 true |
| `-z` | 문자열 길이가 0이면 true |

## 2. 사용예제

```shell
# 파일이 심볼릭 링크이면 A 출력 
$ [ -L /path/file ] && echo "A"

# 파일이 없으면 B 출력 
$ [ ! -f /path/file ] && echo "B"

# 문자열 길이가 0 이면 출력 
$ [ -z "" ] && echo "A"
A
```

## [출처 및 참고]
* <https://wikidocs.net/112181>
