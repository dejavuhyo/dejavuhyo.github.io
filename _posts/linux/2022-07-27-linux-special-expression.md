---
title: Linux 특수 표현
author: dejavuhyo
date: 2022-07-27 11:40:00 +0900
categories: [DevOps, Linux]
tags: [linux-special-expression, special-expression, linux-expression, 리눅스-특수-표현, 특수-표현, 리눅스-표현]
---

## 1. 특수 표현

| 표현 | 비고 |
|:-----:|:-----:|
| `$$` | 쉘의 PID 출력 |
| `$?` | 마지막으로 실행한 명령어의 실행 상태. 0: 정상, 0이 아니면 오류 상태 출력 |
| `$!` | 마지막으로 실행한 명령어의 PID 출력 |


## 2. 전달 파라미터

| 표현 | 비고 |
|:-----:|:-----:|
| `$0` | 쉘 파일의 이름 |
| `$1` | 첫번째 파라미터 |
| `$*` | 전체 파라미터 |
| `$#` | 파라미터의 개수 |

## [출처 및 참고]
* <https://wikidocs.net/111729>
