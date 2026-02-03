---
title: IntelliJ의 Services 탭에서 Docker 연결 방법
author: dejavuhyo
date: 2026-02-03 15:30:00 +0900
categories: [DevOps, IDE]
tags: [intellij, intellij-services, intellij-docker, 인텔리제이-서비스, 인텔리제이-도커]
---

## 1. Docker 설정 열기
IntelliJ가 설치된 Docker를 인식하도록 설정해야 한다.

* File → Settings → Build, Execution, Deployment → Docker

![settings](/assets/img/2026-02-03-intellij-services-connect-docker/settings.png)

## 2. Docker 서버 추가

* 설정창 왼쪽 상단의 + (Add) 버튼을 클릭 → Name: 이름(예: Docker)을 입력

* Connect Docker daemon with: OS 환경에 맞게 선택한다.

> 하단에 "Connection successful" 메시지가 뜨는지 확인한 후 OK를 클릭한다.

![docker-wsl](/assets/img/2026-02-03-intellij-services-connect-docker/docker-wsl.png)

## 3. Services 탭에서 활성화

* 하단 툴바의 Services 탭(없다면 Alt + 8)을 클릭한다.

* Docker 아이콘을 우클릭한 후 Connect를 선택한다.

![connect](/assets/img/2026-02-03-intellij-services-connect-docker/connect.png)

* 연결이 완료되면 현재 실행 중인 Containers, 다운로드된 Images, Networks 등을 한눈에 볼 수 있다.

![start](/assets/img/2026-02-03-intellij-services-connect-docker/start.png)

![console](/assets/img/2026-02-03-intellij-services-connect-docker/console.png)

## [출처 및 참고]
* [https://www.jetbrains.com/help/idea/docker.html#connect_to_docker](https://www.jetbrains.com/help/idea/docker.html#connect_to_docker)
* [https://www.jetbrains.com/help/idea/settings-docker.html](https://www.jetbrains.com/help/idea/settings-docker.html)
* [https://blog.jetbrains.com/idea/](https://blog.jetbrains.com/idea/)
