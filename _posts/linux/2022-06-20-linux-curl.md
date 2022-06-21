---
title: 리눅스 curl
author: dejavuhyo
date: 2022-06-20 11:40:00 +0900
categories: [DevOps, Linux]
tags: [linux-curl, curl, 리눅스-curl, 리눅스]
---

## 1. curl
curl은 여러 프로토콜을 이용하여 네트워크 명령을 전송하는 도구이다. 지원되는 프로토콜 (HTTP, HTTPS, FTP, FTPS, SCP, SFTP, TFTP, DICT, TELNET, LDAP 또는 FILE) 중 하나를 사용하여 서버간에 데이터를 전송할 수 있다.

프록시 지원, 사용자 인증, FTP 업로드, HTTP 게시, SSL 연결, 쿠키, 파일 전송 이력서 등과 같은 유용한 기능을 제공한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:---:|:---:|
| `-o {파일명} {경로}` | 지정한 파일명으로 파일 다운로드 |
| `-O {경로}` | 파일 이름으로 다운로드 |
| `-s` | 추가 정보없이 처리 |
| `-I` | 헤더 정보만 확인 |
| `-i` | 헤더정보 확인 |
| `-H` | 헤더 정보 전달 |
| `-G` | 파라미터 전달 |
| `-X` | 메소드 타입 설정(기본 GET), POST, DELETE 등 |
| `-d` | 전달 데이터, POST 타입일 때 json 형태로 입력하면 데이터 전달 |
| `-L` | 3XX 응답이 오면 경로를 변경함 |
| `-x` | 프록시 설정 |
| `-v` | 상세 |
| `--trace-ascii <파일명>` | curl로 송신한 정보, 수신한 정보를 파일로 저장 |

## 3. 사용예제

```shell
# 파일 다운로드
$ curl -O http://apache.mirror.cdnetworks.com/oozie/4.3.1/oozie-4.3.1.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 2374k  100 2374k    0     0  23.4M      0 --:--:-- --:--:-- --:--:-- 37.4M

# 파일 이름을 지정하여 다운로드
$ curl -o oozie.tar.gz http://apache.mirror.cdnetworks.com/oozie/4.3.1/oozie-4.3.1.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 2374k  100 2374k    0     0  35.6M      0 --:--:-- --:--:-- --:--:-- 38.6M

# s 옵션을 이용하면 전송 속도와 같은 부가 정보가 나타나지 않음
$ curl -so oozie.tar.gz http://apache.mirror.cdnetworks.com/oozie/4.3.1/oozie-4.3.1.tar.gz

# i 옵션으로 헤더 정보 확인
$ curl -i http://127.0.0.1/latest/dynamic/instance-identity/document
HTTP/1.0 200 OK
Content-Type: text/plain
Accept-Ranges: bytes
ETag: "3177614890"
Last-Modified: Wed, 18 Sep 2019 04:09:27 GMT
Content-Length: 505
Connection: close
Date: Wed, 18 Sep 2019 04:13:18 GMT
Server: EC2ws

# H 옵션으로 헤더 전달
$ curl -H "Content-Type: application/json" http://127.0.0.1/latest/dynamic/instance-identity/document

# GET 방식 파라미터 전달
$ curl -G http://127.0.0.1/latest/dynamic/instance-identity/document?a=b

# POST 방식 파라미터 전달
$ curl http://127.0.0.1/latest/dynamic/instance-identity/document -d a=b
$ curl http://127.0.0.1/latest/dynamic/instance-identity/document -d "a=b&c=d"

# 메소드 변경
$ curl -X "DELETE" http://127.0.0.1/latest/dynamic/instance-identity/document

# POST 형식 데이터 전달
$ curl -X POST -d "{\"key\":\"val\"}" http://127.0.0.1
```

### 1) 프록시 설정

```shell
$ curl -x proxy_host:proxy_port http://target-url
```

### 2) HTTP 인증
특정 사이트에서 로그인 정보를 필요로 하는 경우 `-u`, `--user` 옵션을 이용해서 로그인 할 수 있다.

```shell
$ curl -X POST --user "user:password" http://airflow/api/v1/dags/sample/dagRuns
```

### 3) Kerberos 인증 사용
Kerberos 인증을 `curl` 명령으로 사용하는 경우에는 `--negotiate` 옵션을 이용한다.

```shell
# --negotiate 옵션을 사용하기 전에 kinit으로 Kerberos 인증을 해야 함
# -u: 옵션은 커버로스 인증을 사용
$ curl --negotiate -u: -sS -L -X GET -k https://name-node.com:8090
```

### 4) 송/수신한 데이터를 파일로 저장
curl을 사용하여 실제로 전달한 정보를 파일로 저장하여 확인하고 싶은 경우 `--trace-ascii` 옵션을 이용한다.

form을 이용하여 전달한 데이터가 실제로 어떻게 전달되는 지 확인하고, 수정할 때 유용하게 사용할 수 있다.

```shell
# sample.log 파일에 송/수신 저장
curl --trace-ascii sample.log www.google.com

$ cat sample.log
== Info:   Trying 172.217.175.36:80...
== Info: Connected to www.google.com (172.217.175.36) port 80 (#0)
=> Send header, 78 bytes (0x4e)
0000: GET / HTTP/1.1
0010: Host: www.google.com
0026: User-Agent: curl/7.79.1
003f: Accept: */*
004c:
== Info: Mark bundle as not supporting multiuse
<= Recv header, 17 bytes (0x11)
0000: HTTP/1.1 200 OK
<= Recv header, 37 bytes (0x25)
0000: Date: Thu, 28 Apr 2022 05:22:37 GMT
<= Recv header, 13 bytes (0xd)
0000: Expires: -1
<= Recv header, 35 bytes (0x23)
0000: Cache-Control: private, max-age=0
<= Recv header, 45 bytes (0x2d)
0000: Content-Type: text/html; charset=ISO-8859-1
<= Recv header, 69 bytes (0x45)
0000: P3P: CP="This is not a P3P policy! See g.co/p3phelp for more inf
0040: o."
```

### 5) 파일 업로드
파일을 직접 업로드 하는 경우 `--data-binary` 옵션을 이용한다.

```shell
$ curl --location --request PUT 'https://url' \
--header 'Content-Type: application/octet-stream'\
--data-binary "@../user-file"
```

### 6) HTTP/2 stream 0 was not closed cleanly: PROTOCOL_ERROR (err 1) 오류
curl을 이용할 때 이런 오류가 발생하는 경우에는 다음과 같이 http1.1 옵션을 추가한다.

```shell
$ curl --location --request PUT 'https://urlt' \
--header 'Content-Type: application/octet-stream' --http1.1 \
--data-binary "@../user-file"
```

## [출처 및 참고]
* <https://wikidocs.net/48025>
