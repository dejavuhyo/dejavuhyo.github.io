---
title: Ubuntu apt Update와 Upgrade 차이점
author: dejavuhyo
date: 2024-08-23 08:26:00 +0900
categories: [DevOps, Linux]
tags: [ubuntu-update, ubuntu-upgrade, ubuntu-update-upgrade, update-upgrade]
---

## 1. Ubuntu 패키지 업데이트

### 1) apt update
`apt update` 명령은 시스템의 패키지 목록을 최신 상태로 업데이트한다.

이 과정에서 apt는 `/etc/apt/sources.list` 파일과 `/etc/apt/sources.list.d/` 디렉토리에 정의된 저장소의 목록을 사용하여 사용 가능한 패키지와 그들의 최신 버전 정보를 다운로드 한다.

실제로 설치된 패키지들을 업데이트하지 않는다. 이용 가능한 패키지들의 새로운 버전이 있는지를 파악하고, 시스템이 다음 단계인 upgrade 명령을 통해 실제 업데이트를 수행할 수 있도록 준비한다.

```shell
sudo apt update
```

### 2) apt upgrade
`apt upgrade` 명령은 시스템에 설치된 패키지들을 최신 버전으로 업그레이드한다.

`apt update`로 최신화된 패키지 목록을 기반으로, 새로운 버전이 있는 패키지들을 찾아 설치 한다.

의존성 문제나 중요한 시스템 변경이 필요한 경우, 해당 패키지는 업그레이드되지 않을 수 있다.

```shell
sudo apt upgrade
```

## [출처 및 참고]
* [https://velog.io/@qlgks1/리눅스-패키지-매니저-ubuntu-apt-update-vs-upgrade-remove-등-활용법](https://velog.io/@qlgks1/리눅스-패키지-매니저-ubuntu-apt-update-vs-upgrade-remove-등-활용법)
