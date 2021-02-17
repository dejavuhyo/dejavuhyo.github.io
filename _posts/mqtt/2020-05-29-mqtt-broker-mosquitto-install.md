---
title: MQTT Broker Mosquitto 설치 및 테스트
author: dejavuhyo
date: 2020-05-29 11:00:00 +0900
categories: [DevOps, MQTT]
tags: [mqtt-broker, mqtt-mosquitto, mosquitto-install, mosquitto-setup, mosquitto-subscriber, mosquitto-publisher, mosquitto-message, mqtt-브로커, mosquitto-설치, mosquitto-설정, mosquitto-메시지]
---

## 1. 설치 파일 다운로드

### 1) 설치 환경

> Windows 10 x64
>
> Mosquitto v1.6.9: mosquitto-1.6.9-install-windows-x64.exe
>
> OpenSSL v1.0.2u Light: Win64OpenSSL_Light-1_0_2u.exe

### 2) Mosquitto

* <https://mosquitto.org/download/>

### 3) OpenSSL

* <http://slproweb.com/products/Win32OpenSSL.html>

## 2. 설치

### 1) Mosquitto 설치

![img001](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img001.png)

![img002](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img002.png)

![img003](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img003.png)

### 2) OpenSSL 설치

![img004](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img004.png)

* mosquitto 설치 경로 선택

![img005](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img005.png){: width="499" height="388"}

![img006](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img006.png){: width="499" height="388"}

![img007](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img007.png){: width="499" height="388"}

### 3) dll 파일 설정

* 파일 복사: C:\Program Files\mosquitto\OpenSSL-Win64\bin
  - libcrypto-1_1-x64.dll
  - libssl-1_1-x64.dll
  - ossltest.dll
  - padlock.dll

![img008](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img008.png)

* 붙여넣기: C:\Program Files\mosquitto

![img009](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img009.png)

## 3. 포트 확인

```command
C:\Windows\system32>netstat -an
```

![img010](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img010.png)

## 4. 시작 및 종료
명령 프롬프트 창을 관리자 권한으로 실행 하고 Mosquitto 설치 폴더로 이동

### 1) Mosquitto Broker 실행

* 모든 통신 로그를 보면서 시작

```command
C:\Program Files\mosquitto>mosquitto -v
```

![img011](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img011.png)

* 시작

```command
C:\Program Files\mosquitto>net start mosquitto
```

* 종료

```command
C:\Program Files\mosquitto>net stop mosquitto
```

![img012](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img012.png)

### 2) Subscriber 실행

```command
C:\Program Files\mosquitto>mosquitto_sub -h localhost -t /testTopic
```

![img013](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img013.png)

### 3) Publisher로 메시지 전송

![img014](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img014.png)

### 4) 메시지 수신 확인

![img015](/assets/img/2020-05-29-mqtt-broker-mosquitto-install/img015.png)

## 출처 및 참고
* <https://jolog.tistory.com/12>
* <https://dalkomit.tistory.com/119>
