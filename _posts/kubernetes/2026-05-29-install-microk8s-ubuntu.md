---
title: Ubuntu에 MicroK8s 설치 방법
author: dejavuhyo
date: 2026-05-29 10:30:00 +0900
categories: [DevOps, Kubernetes]
tags: [kubernetes, microk8s, snap, microk8s-install]
---

![microk8s-logo](/assets/img/2026-05-29-install-microk8s-ubuntu/microk8s-logo.png)

## 1. 패키지 업데이트 및 Snap 설치 확인
MicroK8s는 Canonical사에서 관리하기 때문에 Ubuntu의 패키지 매니저인 `snap`을 이용하여 설치할 수 있다.

Ubuntu는 기본적으로 `snapd`가 설치되어 있지만, 시스템 패키지를 최신 상태로 업데이트 한다.

```bash
sudo apt update && sudo apt upgrade -y
```

## 2. MicroK8s 설치
`snap` 명령어를 사용해 MicroK8s를 설치한다. 시스템 전반에 제어 권한을 부여하기 위해 `--classic` 옵션이 필요하다.

```bash
# 최신 안정 버전을 설치할 때
sudo snap install microk8s --classic

# 특정 버전을 지정하여 설치할 때 (예: 1.28 버전)
sudo snap install microk8s --classic --channel=1.28/stable
```

## 3. 사용자 권한 설정 (관리자 권한 없이 사용)
기본적으로 MicroK8s 명령어는 `sudo` 권한이 필요하다. 매번 `sudo`를 붙이는 번거로움을 피하기 위해 현재 사용자를 `microk8s` 그룹에 등록한다.

```bash
# 현재 사용자를 microk8s 그룹에 추가
sudo usermod -aG microk8s $USER

# .kube 폴더의 소유권 변경
sudo chown -f -R $USER ~/.kube

# 변경된 그룹 설정을 현재 터미널 세션에 바로 적용
newgrp microk8s
```

## 4. 설치 확인 및 상태 체크
MicroK8s가 정상적으로 작동하고 있는지 확인한다.

```bash
# MicroK8s 상태 및 활성화된 애드온 확인
microk8s status --wait-ready

# Kubernetes 클러스터 노드 정보 확인
microk8s kubectl get nodes
```

## 5. 명령어 에일리어스(Alias) 설정
매번 `microk8s kubectl`이라고 길게 입력하는 대신 표준 쿠버네티스처럼 `kubectl`로만 입력하고 싶다면 별칭을 등록하는 것이 편리하다.

```bash
# snap 기능을 이용해 공식 에일리어스 지정
sudo snap alias microk8s.kubectl kubectl

# 적용 확인
kubectl get po -A
```

## 6. 유용한 애드온(Addon) 활성화
MicroK8s는 핵심 기능만 가볍게 띄운 후, 필요한 기능은 애드온 형태로 켜고 끄는 방식을 사용한다. 개발 환경에 유용한 대표적인 애드온 몇 가지 이다.

```bash
# DNS 서비스 활성화 (필수)
microk8s enable dns

# 대시보드(웹 UI) 활성화
microk8s enable dashboard

# 인그레스(Ingress) 컨트롤러 활성화
microk8s enable ingress

# 로컬 컨테이너 이미지 레지스트리 활성화 (localhost:32000)
microk8s enable registry
```

> 애드온 비활성화 방법: microk8s disable [애드온이름] (예: microk8s disable dashboard)

## [출처 및 참고]
* [https://canonical.com/microk8s/docs/getting-started](https://canonical.com/microk8s/docs/getting-started)
