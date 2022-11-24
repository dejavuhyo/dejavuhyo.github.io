---
title: Nginx 명령어
author: dejavuhyo
date: 2022-11-08 20:20:00 +0900
categories: [DevOps, Nginx]
tags: [start-nginx, stop-nginx, restart-nginx, nginx, nginx-command-line, command-line-parameters, nginx-parameters, nginx-명령어, nginx-매개-변수, 명령줄-매개-변수]
---

## 1. 형태
Nginx를 시작하려면 실행 파일을 실행한다. Nginx가 시작되면 `-s` 매개 변수를 사용하여 실행 파일을 호출하여 제어할 수 있다. 다음 구문을 사용한다.

```shell
$ nginx -s signal
```

여기서 signal은 다음 중 하나일 수 있다.

* stop: 빠른 종료

* quit: 정상 종료

* reload: 구성 파일 다시 로드

* reopen: 로그 파일 다시 열기

## 2. 시작

```shell
$ nginx
```

권한이 없거나, 이미 포트가 오픈되어 있으면 아래 에러가 발생한다.

```shell
# permission denied
$ nginx
nginx: [alert] could not open error log file: open() "/var/log/nginx/error.log" failed (13: Permission denied)

# address aready in use
$ nginx
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
nginx: [emerg] still could not bind()
```

## 3. 중지
worker 프로세스가 현재 요청의 서비스를 마칠 때까지 기다리는 동안 nginx 프로세스를 중지하기 위해 다음 명령을 실행할 수 있다.

```shell
# 연결 중인 컨넥션이 모두 완료될 때까지 기다린 후 종료
$ nginx -s quit

# 프로세스를 바로 종료
$ nginx -s stop
```

> 이 명령은 nginx를 시작한 사용자와 동일한 사용자로 실행되어야 한다.

## 4. 구성 다시 로드
구성을 다시 로드하는 명령이 nginx로 전송되거나 nginx가 다시 시작될 때까지 구성 파일의 변경 사항이 적용되지 않는다. 구성을 다시 로드하려면 다음을 실행한다.

```shell
$ nginx -s reload
```

## [출처 및 참고]
* <https://nginx.org/en/docs/beginners_guide.html>
* <https://gongzza.github.io/linux/how-to-control-nginx/>
