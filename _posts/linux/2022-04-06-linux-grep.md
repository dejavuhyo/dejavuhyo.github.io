---
title: Linux grep
author: dejavuhyo
date: 2022-04-06 17:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-grep, grep, 리눅스-grep, 리눅스-문자열]
---

## 1. grep
지정한 문자열을 포함하고 있는 행을 검색한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:-----:|:-----:|
| -i | 대소문자 구분없이 검색 |
| -v | 해당 문자를 제외하고 검색 |
| -n | 검색한 문자의 행 출력 |
| -l | 검색한 문자가 들어 있는 파일 이름 출력 |

## 3. 사용예제
기본 문법은 다음과 같다.

```text
grep [OPTIONS] PATTERN [FILE...]
grep [OPTIONS] [-e PATTERN | -f FILE] [FILE...]

# 파이프를 이용한 데이터 입력
cat file_name.txt | grep PATTERN
```

주요 사용방법은 다음과 같다.

```shell
# test.txt 파일 내용
$ cat test.txt
a
b
c
d
e
f
g
h
i
j
k

# 대소문자 구분없이 확인
$ grep -i A test.txt
a

# 해당 문자를 제외하고 검색
$ grep -v a test.txt
b
c
d
e
f
g
h
i
j
k

# 검색한 문자의 행 번호를 출력
$ grep -n a test.txt
1:a

# 검색한 문자가 포함된 파일 이름 출력
$ grep -l a test.txt
test.txt
```

### 1) 여러개의 파일에서 파일명과 함께 검색

```shell
# 여러개의 파일에서 파일명과 함께 검색한 문자 출력
$ grep a *
test2.txt:a
test.txt:a
```

### 2) 정규식을 이용한 검색

```shell
# 정규식을 이용한 검색
$ cat test.txt | grep "[a|b]"
a
b
```

### 3) 문자가 있는 파일 출력
jar파일에서 클래스 찾기

```shell
$ grep -l "org.codehaus.jackson.jaxrs.JacksonJaxbJsonProvider" * 
jackson-jaxrs-1.9.13.jar
```

## [출처 및 참고]
* <https://wikidocs.net/32351>
