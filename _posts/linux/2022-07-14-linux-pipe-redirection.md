---
title: 리눅스 파이프(Pipe), 리다이렉션(Redirection)
author: dejavuhyo
date: 2022-07-14 10:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-pipe, linux-redirection, pipe-redirection, linux-pipe-redirection, linux-파이프, linux-리다이렉션, linux-shell-script, 파이프, 리다이렉션, 리눅스-파이프, 리눅스-리다이렉션, 리눅스, 리눅스-쉘-스크립트]
---

## 1. 파이프(Pipe), 리다이렉션(Redirection)
표준 입력, 출력, 에러를 연결하기 위해서 사용한다.

## 2. 파이프(Pipe)
파이프는 `command1 | command2`와 같은 형태로 사용되고, command1의 표준 출력을 command2의 표준 입력으로 전달한다. `&`를 붙이면 표준 에러도 함께 전달한다.

```shell
$ command1 | command2
$ command1 |& commnad2

# file.txt의 내용을 읽어서, grep의 입력으로 전달
$ cat file.txt | grep a
```

## 3. 리다이렉션(Redirection)
리다이렉션은 `command > filename`와 같은 형태로 사용되고, 파일을 읽어서 표준 입력으로 전달하거나, 표준 출력을 파일로 저장한다. 파일 대신 명령의 결과를 입력, 출력할 수도 있다. `&`를 붙이면 표준 에러도 함께 전달한다.

| 리다이렉션 | 내용 |
|:-----:|:-----:|
| `<` | 파일 읽기 |
| `>` | 파일 쓰기(overwrite) |
| `>>` | 파일 쓰기(insert) |

```shell
# 파일 입력
$ command < infile

# 파일 출력 
$ command > outfile
$ command >> outfile
$ command >& outfile
$ command >>& outfile
```

## 4. 2>&1와 /dev/null
스크립트를 작성할 때 `2>&1` 이라는 표현이 있다. 이 명령은 표준에러(2)를 표준출력(1)으로 리다이렉션 하는 것이다.

리눅스는 표준 입력과 출력을 숫자로 표현할 수 있다. 이를 파일 디스크립터라고 한다.

| 구분 | 파일 디스크립터 |
|:-----:|:-----:|
| 표준 입력 | 0 |
| 표준 출력 | 1 |
| 표준 에러 | 2 |

`/dev/null`은 표준 출력을 버리기 위한 용도로 사용되는 디스크립터 이다. 처리 결과로 출력되는 로그를 보지 않기 위해 사용하고, `2>&1`과 함께 사용하여 표준 출력, 표준 에러를 화면에 표시하지 않는 용도로 사용한다.

```shell
# sample.sh의 표준 출력을 보이지 않도록 리다이렉션
$ sample.sh > /dev/null

# sample.sh의 표준 출력, 에러를 보이지 않도록 리다이렉션
$ sample.sh > /dev/null 2>&1
```

## [출처 및 참고]
* <https://wikidocs.net/29734>
