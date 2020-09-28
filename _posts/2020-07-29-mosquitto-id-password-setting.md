---
title: Mosquitto ID, Password 설정
author: Hyosik
date: 2020-07-29
categories: [Tools, MQTT]
tags: [mosquitto_id_password, mosquitto_conf, mosquitto_access_control, mosquitto_authentication]
---

## 1. ID, Password 등록 파일 생성

### 1) password.txt 파일을 생성하여 id:password 형식으로 작성

![img001](/assets/img/2020-07-29-mosquitto-id-password-setting/img001.png)

### 2) password.txt 파일을 Mosquitto가 설치된 폴더로 이동

![img002](/assets/img/2020-07-29-mosquitto-id-password-setting/img002.png)

## 2. Password 암호화

### 1) 암호화

* mosquitto가 설치된 폴더로 이동 및 암호화

```command
C:\mosquitto>mosquitto_passwd -b password.txt admin password
```

![img003](/assets/img/2020-07-29-mosquitto-id-password-setting/img003.png)

### 2) Password 암호화 확인

![img004](/assets/img/2020-07-29-mosquitto-id-password-setting/img004.png)

## 3. mosquitto.conf 설정

### 1) Security

```text
기본값은 true, true로 설정하면 아무나 브로커 서버에 접근이 가능합니다.
하지만 false 로 설정하면 아무나 브로커 서버에 접근이 가능하지 않으며
password_file옵션으로 인증된 사용자를 제어해야 합니다.

allow_anonymous false
```

### 2) Default authentication and topic access control

```text
암호 파일을 사용하여 브로커에 대한 액세스를 제어합니다.
이 파일은 mosquitto_passwd 유틸리티를 사용하여 생성됩니다.
TLS 지원이 컴파일되지 않은 경우 mosquitto (TLS 지원을 포함하는 것이 좋습니다)로
일반 텍스트 암호가 사용되는 경우 파일은 텍스트 파일이어야 합니다.
다음과 같은 형식의 줄이 있습니다.
사용자 이름 : 암호
암호 (및 콜론)는 원한다면 생략 될 수 있지만,
보안을 거의 제공하지 않습니다.
TLS 클라이언트 require_certificate 및 use_identity_as_username로 대체 인증이 가능한 옵션이 있습니다.

password_file password.txt
```

## 출처 및 참고
* <https://mosquitto.org/documentation/>
* <https://m.blog.naver.com/PostView.nhn?blogId=phkaa&logNo=220937990534&proxyReferer=https:%2F%2Fwww.google.com%2F>
