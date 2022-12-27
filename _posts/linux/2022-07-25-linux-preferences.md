---
title: Linux 환경설정
author: dejavuhyo
date: 2022-07-25 09:20:00 +0900
categories: [DevOps, Linux]
tags: [linux-preferences, linux-bash, bash, linux-bash-profile, bash-profile, 리눅스-환경설정, 환경설정, 배쉬쉘]
---

## 1. 환경설정
배쉬쉘의 설정값을 설정하는 파일은 `.bashrc`와 `.bash_profile` 두 개가 있다. 사용자가 로그인할 때 `.bash_profile` 파일을 호출하고, 이 파일에서 `.bashrc` 파일을 호출한다.

사용자 로그인시 호출 순서는 `.bash_profile` → `.bashrc` → `/etc/bashrc` 이다. 보통 `.bashrc` 파일에 사용자가 필요한 alias나 환경 변수를 기록한다.

## 2. .bash_profile
`.bash_profile` 파일의 기본내용이다. 배쉬쉘 환경인지 확인하여 `.bashrc` 파일을 호출한다.

```text
if [ -n "$BASH" ] && [ -f ~/.bashrc ] && [ -r ~/.bashrc ]; then
    source ~/.bashrc
fi
```

## 3. .bashrc
`.bashrc` 파일의 기본내용이다. 운영체제의 배쉬쉘 설정 파일을 확인하고 호출한다.

```text
# Source global definitions
if [ -f /etc/bashrc ]; then
    . /etc/bashrc
fi
```

## [출처 및 참고]
* <https://wikidocs.net/73709>
