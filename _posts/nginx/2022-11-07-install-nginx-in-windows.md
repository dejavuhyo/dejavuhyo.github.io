---
title: 윈도우에 Nginx 설치
author: dejavuhyo
date: 2022-11-07 20:50:00 +0900
categories: [DevOps, Nginx]
tags: [install-nginx, nginx, windows-nginx, 윈도우-nginx-설치, nginx-설치, 윈도우-nginx]
---

## 1. Nginx 다운로드

* 윈도우용 Nginx 다운로드: <http://nginx.org/en/download.html>

![nginx-download](/assets/img/2022-11-07-install-nginx-in-windows/nginx-download.png)

## 2. 압축 해제 및 실행

### 1) nginx.exe 파일 더블클릭

![nginx-folder](/assets/img/2022-11-07-install-nginx-in-windows/nginx-folder.png)

### 2) 명령 프롬프트
nginx.exe 파일이 있는 위치에서 명령어를 실행시킨다.

```shell
> nginx
```

### 3) 장상 종료
nginx.exe 파일이 있는 위치에서 명령어를 실행시킨다.

```shell
> nginx -s quit
```

## 3. 실행 확인
브라우저에서 <http://localhost>로 접속한다. 기본 80 port로 설정되어 있다.

![nginx-start](/assets/img/2022-11-07-install-nginx-in-windows/nginx-start.png)

## [출처 및 참고]
* <https://harrydony.tistory.com/665>
