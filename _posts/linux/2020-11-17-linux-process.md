--- 
title: Linux 프로세스
author: Hyosik
date: 2020-11-17 11:30:00 +0900
categories: [OS, Linux]
tags: [process, process_id, process_kill, linux_process, 리눅스_프로세스]
---

## 1. 프로세스 확인

### 1) 목록 확인

```shell
[root@localhost ~]# ps
```

### 2) 상세정보 확인

```shell
[root@localhost ~]# ps -f
```

## 2. 모든 프로세스 정보

```shell
# 목록
[root@localhost ~]# ps -e

# 상세 정보
[root@localhost ~]# ps -ef
```

### 1) 목록 및 자원 사용률

```shell
[root@localhost ~]# ps -aux
```

* -aux 옵션으로 확인할 수 있는 정보의 각 필드 의미

![img001](/assets/img/2020-11-17-linux-process/img001.png)

* 프로세스 상태를 나타내는 STAT항목
  - R (Runable): 실행 대기 상태
  - S (Sleeping): 수면 상태
  - D (inDIskwait): 입출력을 기다리는 상태
  - T (sTopped): 멈춰 있거나 흔적이 남아있는 상태
  - Z (Zombie): 죽었지만 프로세스에 남아있는 상태 (자원 낭비)

* ps [-옵션]
  - a (All processes): 프로세스 현황 표시
  - u (User): 유저 지향적 (top 포맷)
  - x: 터미널 제어 없이 프로세스 현황 보기
  - e (All processes): 현재 시스템 내에서 실행중인 모든 프로세스 정보를 출력
  - f (Full listing): 모든 정보 확인
  - o (User): 유저 포맷팅

* ps -f 명령으로 나타나는 항목들의 의미
  - UID: 프로세스의 실행 / 소유자 아이디
  - PID: 프로세스의 고유 번호(Process Identification Number)
  - PPID: 부모 프로세스의 PID(Parent PID)
  - C: 프로세스 우선순위
  - STIME: 프로세스가 시작된 시간
  - TTY: 프로세스와 연결된 터미널
  - TIME: 실행에 걸린 시간
  - CMD: 프로세스를 생성하는데 내린 명령

### 2) 프로세스 트리

```shell
[root@localhost ~]# pstree
```

## 3. 해당 프로세스 정보
ps -ef | grep [프로세스명]

```shell
[root@localhost ~]# ps -ef | grep spring-boot-application
```

## 4. 현재 실행중인 전체 프로세스 목록 확인

```shell
[root@localhost ~]# top
```

## 5. 프로세스 kill
kill -9 [PID]

```shell
[root@localhost ~]# kill -9 25344
```

* 자주쓰는 옵션
  - SIGHUP (1) : 대기
  - SIGKILL (9) : 강제 종료
  - SIGTERM (15) : 종료

* POSIX 신호

| 신호 | 이식 가능한 번호 | 기본 동작 | 설명 |
|:---:|:---:|:---:|:---:|
| SIGABRT | 6 | 종료 (코어 덤프) | 프로세스 중단 신호 |
| SIGALRM | 14 | 종료 | 알람 클럭 |
| SIGBUS | 	없음 | 종료 (코어 덤프) | 정의되지 않은 메모리 오브젝트의 일부분에 접근 |
| SIGCHLD | 없음 | 무시 | 차일드 프로세스 종료, 중단, 계속 |
| SIGCONT | 없음 | 계속 | 정지하지 않으면 계속 실행 |
| SIGFPE | 없음 | 종료 (코어 덤프) | 오류가 있는 산술 조작 |
| SIGHUP | 1 | 종료 | 행업(Hangup) |
| SIGILL | 없음 | 종료 (코어 덤프) | 유효하지 않은 명령 |
| SIGINT | 2 | 종료 | 터미널 인터럽트 신호 |
| SIGKILL | 9 | 종료 | 킬 (신호를 잡거나 무시할 수 없음) |
| SIGPIPE | 없음 | 종료 | 신호를 읽는 사용자가 없는 상태에서 파이프에 기록 |
| SIGPOLL | 없음 | 종료 | 폴링 가능한 이벤트 |
| SIGPROF | 없음 | 종료 | 프로파일링 타이머 시간 초과 |
| SIGQUIT | 3 | 종료 (코어 덤프) | 터미널 종료 신호 |
| SIGSEGV | 없음 | 종료 (코어 덤프) | 잘못된 메모리 참조 |
| SIGSTOP | 없음 | 정지 | 실행 정지 (신호를 잡거나 무시할 수 없음) |
| SIGSYS | 없음 | 종료 (코어 덤프) | 불량 시스템 호출 |
| SIGTERM | 15 | 종료 | 종료 신호 |
| SIGTRAP | 없음 | 종료 (코어 덤프) | 트레이스/브레이크포인트 트랩 |
| SIGTSTP | 없음 | 정지 | 터미널 정지 신호 |
| SIGTTIN | 없음 | 정지 | 백그라운드 프로세스 읽기 시도 |
| SIGTTOU | 없음 | 정지 | 백그라운드 프로세스 쓰기 시도 |
| SIGUSR1 | 없음 | 종료 | 사용자 정의 신호 1 |
| SIGUSR2 | 없음 | 종료 | 사용자 정의 신호 2 |
| SIGURG | 없음 | 무시 | 높은 대역의 데이터를 소켓에서 이용 가능 |
| SIGVTALRM | 없음 | 종료 | 가상 타이머 시간 초과 |
| SIGXCPU | 없음 | 종료 (코어 덤프) | CPU 시간 제한 초과 |
| SIGXFSZ | 없음 | 종료 (코어 덤프) | 파일 크기 제한 초과 |

## [출처 및 참고]
* <https://eehoeskrap.tistory.com/245>
* <https://ko.wikipedia.org/wiki/%EC%9C%A0%EB%8B%89%EC%8A%A4_%EC%8B%A0%ED%98%B8>
