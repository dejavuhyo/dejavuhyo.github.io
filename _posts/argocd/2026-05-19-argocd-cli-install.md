---
title: ArgoCD CLI 설치
author: dejavuhyo
date: 2026-05-19 10:00:00 +0900
categories: [GitOps, ArgoCD]
tags: [argocd, argocd-cli, cli, argocd-cli-install]
---

## 1. ArgoCD CLI
사용자가 터미널 창에서 명령을 내릴 수 있도록 `argocd`라는 CLI(Command Line Interface)를 Ubuntu에 다운로드하는 명령어이다.

`argocd`라는 명령어가 없어도 웹 UI에서 모두 제어할 수 있기 때문에 필수 설치는 아니다.

## 2. 설치

### 1) ArgoCD CLI 다운로드

```bash
sudo curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
```

### 2) 실행 권한 주기

```bash
sudo chmod +x /usr/local/bin/argocd
```

### 3) 확인

```bash
argocd version --client
```

## 3. 삭제
따로 복잡한 설치 과정을 거친 게 아니라 특정 폴더에 파일 하나만 다운로드해 둔 것이기 때문에, 그 파일만 지워주면 삭제가 끝난다.

### 1) 삭제

```bash
sudo rm -f /usr/local/bin/argocd
```

### 2) 삭제 확인

```bash
argocd version --client
```

## [출처 및 참고]
* [https://argo-cd.readthedocs.io/en/stable/getting_started](https://argo-cd.readthedocs.io/en/stable/getting_started)
