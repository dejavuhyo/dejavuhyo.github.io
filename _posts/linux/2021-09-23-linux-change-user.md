---
title: Linux 사용자 변경
author: dejavuhyo
date: 2021-09-23 06:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-change-user, change-user, 리눅스-사용자-변경, 사용자-변경]
---

## 1. 사용자 변경
su(substitute user)는 유닉스 명령을 로그아웃하지 않고 다른 사용자의 권한으로 shell을 실행하는데 사용된다.

일반적으로 관리 작업을 위해 다시 로그 오프하지 않고 사용자의 권한을 root로 변경하는데 사용된다. 같은 방법으로 다른 사용자로 전환하는데 사용된다.

[KDE](https://ko.wikipedia.org/wiki/KDE)와 [GNOME](https://ko.wikipedia.org/wiki/%EA%B7%B8%EB%86%88)과 같은 데스크톱 환경은 사용자가 이러한 액세스를 해야 하는 명령을 실행할 수 있게 하려고 비밀번호를 입력할 수 있는 상자를 보여주는 프로그램들이 있다.

어떤 사용자의 권한으로 실행할지 정하지 않고 실행한 경우에는, root 사용자로 간주한다. (su root와 동일)

## 2. 사용법
명령 줄에서 실행하였을 때, su는 일반적으로 목적 사용자의 비밀번호를 묻고, 제대로 입력되었다면, 해당 계정과 연관된 모든 파일에 대한 사용자 접근 권한을 부여한다.

```shell
su [option] [사용자명]
```

* 예제

```shell
[john@localhost:~]$ su
Password:
[root@localhost:/home/john#] exit
logout
[john@localhost:~]$
```

또한, super 사용자가 아닌 다른 사용자로 전환할 수 있다. 예를 들어 su jane.

```shell
[john@localhost:~$] su jane
Password:
[jane@localhost:/home/john$] exit
logout
[john@localhost:~]$
```

일반적으로 관리자들은 하이픈을 붙여서 사용하는데, 이는 로그인 shell을 시작하는데 사용할 수 있다. 이렇게 하여 사용자가 목적 사용자의 사용자 환경을 얻을 수 있다.

```shell
[john@localhost:~$] su - jane
Password:
[jane@localhost:~]$
```

관련 명령인 sudo는 다른 사용자로 명령을 실행하지만, 어떤 사용자가 어떤 명령을 어떤 다른 사용자로 수행할 수 있는지에 대하여 제한 할 수 있다. (일반적으로 `/etc/sudoers`라는 설정 파일에 저장되어 있고 `vi sudo`등을 이용하면 편집할 수 있다.)

su와는 달리, sudo는 인증할 때 다른 사용자의 비밀번호가 아닌, 자신의 비밀번호로 인증한다. (특정 호스트에 있는 특정 사용자에게 명령 수행을 위임할 때, 암호를 공유하지 않고 할 수 있게 해 주고, 모든 무인 단말기의 위험을 방지함)

일부 유닉스 계열 운영 체제에서는 wheel 그룹에 속해 있는 사용자들에게만 super 사용자로 su 할 수 있다. 침입자가 그 그룹에 속해 있는 계정 중에 하나를 침입할 수도 있어서, 이러한 보안 문제를 완화하지 않을 수도 있다. 그러나 GNU su는 철학적인 이유 때문에 wheel 그룹을 지원하지 않는다.

## [출처 및 참고]
* [https://ko.wikipedia.org/wiki/Su_(%EC%9C%A0%EB%8B%89%EC%8A%A4)](https://ko.wikipedia.org/wiki/Su_(%EC%9C%A0%EB%8B%89%EC%8A%A4))
