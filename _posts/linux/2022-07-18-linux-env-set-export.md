---
title: Linux 환경변수 env, set, export
author: dejavuhyo
date: 2022-07-18 09:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-env, linux-set, linux-export, env, set, export, linux-환경변수, 리눅스-환경변수]
---

## 1. env, set, export

### 1) env
전역 변수 설정 및 조회한다.

### 2) set
사용자 환경 변수 설정 및 조회한다.

### 3) export
사용자 환경 변수를 전역 변수로 설정한다.

```shell
# PARAM_01을 선언하고 전역 변수(env)와 사용자 환경 변수(set)에서 확인
# 사용자 환경 변수(set)에만 값이 존재
$ PARAM_01=Hello
$ env | grep PARAM_01
$ set | grep PARAM_01
PARAM_01=Hello

# PARAM_01을 export로 전역 변수로 변경
$ export PARAM_01
# 전역 변수(env)와 사용자 환경 변수(set) 모두 존재
$ env | grep PARAM_01
PARAM_01=Hello
$ set | grep PARAM_01
PARAM_01=Hello
```

> 환경 변수 설정은 현재의 세션에만 유효하다. 모든 세션에 적용하기 위해서는 `.bashrc`나 `.profile` 같은 설정 파일에 선언해야 한다.

## 2. set 명령어 옵션
set 명령어에는 몇 가지 옵션이 있다. 스크립트의 선언부나 스크립트 작성중에 입력하면 된다.

| 옵션 | 비고 |
|:-----:|:-----:|
| `-a` | 생성, 변경되는 변수를 export 함 |
| `-e` | 오류가 발생하면 스크립트 종료 |
| `-x` | 수행하는 명령어를 출력 후 실행 |
| `-c` | 다음의 명령을 실행 ex) `bash -c "echo 'A'". bash -c date` |
| `-o` | 옵션 설정 |

다음의 명령어를 실행하면 Hello 까지만 출력되고 프로그램이 종료된다.

```shell
#!/bin/bash
set -e

echo "Hello"
aaa
echo "World"
```

## 3. 옵션 끄기
옵션을 꺼야 하는 경우에는 `+`를 이용할 수 있다.

```shell
#!/bin/bash
set -x

echo "AA"
ls -alh ./

# 위의 명령을 실행 할 때는 디버깅 옵션이 출력되지만 아래 명령을 실행할 때는 출력되지 않는다.
set +x

echo "BB"
ls -alh ./
```

## 4. 입력 모드 설정
`set -o vi`나 `set -o emacs`를 이용해서 명령어 입력 모드를 설정할 수 있다. 홈키나 엔드키를 입력했을때 문자가 대문자, 소문자로 변경된다면 `set -o emacs`로 입력 모드를 변경하면 된다.

## 5. 전체 옵션 확인
`set --help` 명령을 이용해서 모든 옵션을 확인할 수 있다.

```shell
$ set --help
set: set [-abefhkmnptuvxBCHP] [-o option-name] [--] [arg ...]
    Set or unset values of shell options and positional parameters.

    Change the value of shell attributes and positional parameters, or
    display the names and values of shell variables.

    Options:
      -a  Mark variables which are modified or created for export.
      -b  Notify of job termination immediately.
      -e  Exit immediately if a command exits with a non-zero status.
      -f  Disable file name generation (globbing).
      -h  Remember the location of commands as they are looked up.
      -k  All assignment arguments are placed in the environment for a
          command, not just those that precede the command name.
      -m  Job control is enabled.
      -n  Read commands but do not execute them.
      -o option-name
          Set the variable corresponding to option-name:
              allexport    same as -a
              braceexpand  same as -B
              emacs        use an emacs-style line editing interface
              errexit      same as -e
              errtrace     same as -E
              functrace    same as -T
              hashall      same as -h
              histexpand   same as -H
              history      enable command history
              ignoreeof    the shell will not exit upon reading EOF
              interactive-comments
                           allow comments to appear in interactive commands
              keyword      same as -k
              monitor      same as -m
              noclobber    same as -C
              noexec       same as -n
              noglob       same as -f
              nolog        currently accepted but ignored
              notify       same as -b
              nounset      same as -u
              onecmd       same as -t
              physical     same as -P
              pipefail     the return value of a pipeline is the status of
                           the last command to exit with a non-zero status,
                           or zero if no command exited with a non-zero status
              posix        change the behavior of bash where the default
                           operation differs from the Posix standard to
                           match the standard
              privileged   same as -p
              verbose      same as -v
              vi           use a vi-style line editing interface
              xtrace       same as -x
      -p  Turned on whenever the real and effective user ids do not match.
          Disables processing of the $ENV file and importing of shell
          functions.  Turning this option off causes the effective uid and
          gid to be set to the real uid and gid.
      -t  Exit after reading and executing one command.
      -u  Treat unset variables as an error when substituting.
      -v  Print shell input lines as they are read.
      -x  Print commands and their arguments as they are executed.
      -B  the shell will perform brace expansion
      -C  If set, disallow existing regular files to be overwritten
          by redirection of output.
      -E  If set, the ERR trap is inherited by shell functions.
      -H  Enable ! style history substitution.  This flag is on
          by default when the shell is interactive.
      -P  If set, do not resolve symbolic links when executing commands
          such as cd which change the current directory.
      -T  If set, the DEBUG and RETURN traps are inherited by shell functions.
      --  Assign any remaining arguments to the positional parameters.
          If there are no remaining arguments, the positional parameters
          are unset.
      -   Assign any remaining arguments to the positional parameters.
          The -x and -v options are turned off.

    Using + rather than - causes these flags to be turned off.  The
    flags can also be used upon invocation of the shell.  The current
    set of flags may be found in $-.  The remaining n ARGs are positional
    parameters and are assigned, in order, to $1, $2, .. $n.  If no
    ARGs are given, all shell variables are printed.

    Exit Status:
    Returns success unless an invalid option is given.
```

## [출처 및 참고]
* [https://wikidocs.net/72712](https://wikidocs.net/72712)
