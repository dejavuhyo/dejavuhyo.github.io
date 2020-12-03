--- 
title: Linux 권한 설정
author: Hyosik
date: 2020-11-26 09:30:00 +0900
categories: [OS, Linux]
tags: [linux_permission, permission, chmod, chown, 리눅스_권한, 권한]
---

## 1. 접근 권한(chmod)
기존 파일 또는 디렉터리에 대한 접근 권한을 변경할때 사용하고, 파일 소유자나 슈퍼 유저만 변경 가능하다. 파일모드는 문자나 8진수로 지정한다.

![img001](/assets/img/2020-11-26-linux-permission/img001.png)

### 1) chmod 도움말

```shell
[root@localhost ~]# chmod --help
사용법: chmod [옵션]... MODE[,MODE]... FILE...
  또는: chmod [옵션]... 8진수-MODE FILE...
  또는: chmod [옵션]... --reference=RFILE FILE...
Change the mode of each FILE to MODE.
With --reference, change the mode of each FILE to that of RFILE.

  -c, --changes          like verbose but report only when a change is made
  -f, --silent, --quiet  suppress most error messages
  -v, --verbose          output a diagnostic for every file processed
      --no-preserve-root  do not treat '/' specially (the default)
      --preserve-root    fail to operate recursively on '/'
      --reference=RFILE  use RFILE's mode instead of MODE values
  -R, --recursive        change files and directories recursively
      --help     이 도움말을 표시하고 끝냅니다
      --version  버전 정보를 출력하고 끝냅니다

Each MODE is of the form '[ugoa]*([-+=]([rwxXst]*|[ugo]))+|[-+=][0-7]+'.

GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
Report chmod translation bugs to <http://translationproject.org/team/>
For complete documentation, run: info coreutils 'chmod invocation'
```

* 주요 옵션
  - -R: 하위 디렉터리와 파일의 권한까지 변경

### 2) 문자 표현
문자 표기법은 변경할 사용자(대상), 수행 명령(연산), 설정 퍼미션(접근 권한) 세 부분으로 분류 된다.

* 사용자(대상)
  - u: user의 약자로 파일이나 디렉터리 소유자
  - g: 그룹 소유자
  - o: other의 약자로 기타 소유자, 그룹 멤버를 제외
  - a: all의 약자로 u, g, o를 합친 전체

* 수행 명령(연산)
  - +: 허가권 추가
  - -: 허가권 제거
  - =: 허가권 지정, 기존의 속성 값은 사라짐

* 설정 퍼미션(접근 권한)
  - r: 읽기
  - w: 쓰기
  - x: 실행

### 3) 숫자 표현

| 8진수 표기 | 2진수 표기 | 파일 모드 | 권한 |
|:---:|:---:|:---:|:---:|
| 0 | 000 | --- | 권한 없음 |
| 1 | 001 | --x | 읽기(첫번째 비트) |
| 2 | 010 | -w- | 쓰기(두번째 비트) |
| 3 | 011 | -wx | 실행(세번째 비트) |
| 4 | 100 | r-- | 쓰기(두번째 비트), 실행(세번째 비트) |
| 5 | 101 | r-x | 읽기(첫번째 비트), 실행(세번째 비트) |
| 6 | 110 | rw- | 읽기(첫번째 비트), 쓰기(두번째 비트) |
| 7 | 111 | rwx | 읽기, 쓰기, 실행(첫번째, 두번째, 세번째 비트) |

### 4) 사용방법

* chmod [소유자 권한][그룹 권한][일반사용자 권한] 파일명

* 예: test.txt 파일명의 소유자 권한은 [읽기, 쓰기, 실행], 그룹 권한은 [읽기, 쓰기], 일반사용자 [읽기, 쓰기]로 변경

```shell
[root@localhost ~]# chmod 766 test.txt
```

## 2. 소유권 및 그룹(chown)
파일이나 디렉터리 소유주나 그룹을 변경할 때 사용한다. 명령을 실행하면 이전 소유주는 해당 파일에 이 명령을 다시 시행할 수 없다. 명령어를 사용하기 위해서는 슈퍼유저 권한이 필요하다.

### 1) chown 도움말

```shell
[root@localhost ~]# chown --help
Usage: chown [OPTION]... [OWNER][:[GROUP]] FILE...
  or:  chown [OPTION]... --reference=RFILE FILE...
Change the owner and/or group of each FILE to OWNER and/or GROUP.
With --reference, change the owner and group of each FILE to those of RFILE.

  -c, --changes          like verbose but report only when a change is made
  -f, --silent, --quiet  suppress most error messages
  -v, --verbose          output a diagnostic for every file processed
      --dereference      affect the referent of each symbolic link (this is
                         the default), rather than the symbolic link itself
  -h, --no-dereference   affect symbolic links instead of any referenced file
                         (useful only on systems that can change the
                         ownership of a symlink)
      --from=CURRENT_OWNER:CURRENT_GROUP
                         change the owner and/or group of each file only if
                         its current owner and/or group match those specified
                         here.  Either may be omitted, in which case a match
                         is not required for the omitted attribute
      --no-preserve-root  do not treat '/' specially (the default)
      --preserve-root    fail to operate recursively on '/'
      --reference=RFILE  use RFILE's owner and group rather than
                         specifying OWNER:GROUP values
  -R, --recursive        operate on files and directories recursively

The following options modify how a hierarchy is traversed when the -R
option is also specified.  If more than one is specified, only the final
one takes effect.

  -H                     if a command line argument is a symbolic link
                         to a directory, traverse it
  -L                     traverse every symbolic link to a directory
                         encountered
  -P                     do not traverse any symbolic links (default)

      --help     이 도움말을 표시하고 끝냅니다
      --version  버전 정보를 출력하고 끝냅니다

Owner is unchanged if missing.  Group is unchanged if missing, but changed
to login group if implied by a ':' following a symbolic OWNER.
OWNER and GROUP may be numeric as well as symbolic.

Examples:
  chown root /u        Change the owner of /u to "root".
  chown root:staff /u  Likewise, but also change its group to "staff".
  chown -hR root /u    Change the owner of /u and subfiles to "root".

GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
Report chown translation bugs to <http://translationproject.org/team/>
For complete documentation, run: info coreutils 'chown invocation'
```

* 주요 옵션
  - -h: 심볼릭 링크 파일 소유권이나 그룹 변경
  - -R: 하위 디렉터리와 하위 모든 파일의 소유권 변경

### 2) 사용방법

* chown [소유권자]:[그룹식별자] [소유권을 변경하고 싶은 파일명]

* 예: text.txt 파일명의 소유권자를 [aaa]로 하고, 그룹식별자를 [bbb]로 변경

```shell
[root@localhost ~]# chmod aaa:bbb text.txt
```

## [출처 및 참고]
* <https://eunguru.tistory.com/93>
* <https://jhnyang.tistory.com/11>
