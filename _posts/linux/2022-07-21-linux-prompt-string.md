---
title: Linux 프롬프트 스트링
author: dejavuhyo
date: 2022-07-21 10:40:00 +0900
categories: [DevOps, Linux]
tags: [linux-prompt-string, linux-ps, ps, 리눅스-prompt-string, 리눅스-ps, 리눅스-프롬프트-스트링, 프롬프트-스트링]
---

## 1. 프롬프트 스트링(Prompt String)
롬프트 스트링은 쉘에서 사용자의 입력을 대기할 때 나타나는 문자이다. 4가지 종류가 있다.

### 1) PS1
기본 프롬프트 스트링으로, 기본값은 `[\u@\h \W]\$`이다.

### 2) PS2
긴 문자 입력을 위해 나타나는 문자열로, 기본 값은 `>`이다.

### 3) PS3
select 옵션을 처리할 때 나타나는 문자열이다.

### 4) PS4
실행을 디버깅할 때 출력되는 문자열로, 기본값은 `+`이다.

## 2. PS1 설정
사용자가 가장 많이 보게 되는 PS1을 설정하는 방법이다. PS1은 사용자의 입력을 대기할 때 `[scott@home var]$`와 같은 형태로 나오는 프롬프트이다.

PS1 변수를 전역 변수로 `export`하면 된다. 색상 설정을 함께 하여 명령어 입력과 구분하여 주는 것이 좋다.

PS1을 설정할 때 특수기호를 이용하여 이름을 다이나믹하게 변경할 수 있다.

| 기호 | 비고 |
|:-----:|:-----:|
| `\u` | 사용자명 |
| `\h` | 호스트명 |
| `\W` | 현재 디렉토리명 |

```text
PURPLE="\[\033[0;35m\]"
WHITE="\[\033[1;37m\]"
GREEN="\[\033[1;32m\]"
NON_COLOR="\[\033[0m\]"

export PS1="[$GREEN\u$WHITE@$PURPLE\h$WHITE \W]\$$NON_COLOR "
```

## [출처 및 참고]
* [https://wikidocs.net/73419](https://wikidocs.net/73419)
