---
title: Kubernetes가 설치 되지 않은 Ubuntu에 Helm으로 ArgoCD 설치하는 방법
author: dejavuhyo
date: 2026-05-18 11:00:00 +0900
categories: [GitOps, ArgoCD]
tags: [argocd, argocd-inatall, helm-argocd, kubernetes-argocd, microk8s, microk8s-argocd]
---

## 1. 개요
ArgoCD는 쿠버네티스(Kubernetes) 위에서만 작동하는 애플리케이션이다.

Ubuntu 환경에 쿠버네티스가 설치되어 있지 않다면, Helm을 사용하기 전에 가장 먼저 경량화된 쿠버네티스 클러스터를 먼저 구성해야 한다.

가볍고 강력한 MicroK8s를 활용한다.

## 2. Ubuntu에 쿠버네티스(MicroK8s) 설치
Ubuntu의 패키지 매니저인 `snap`을 사용한다.

### 1) MicroK8s 설치

```bash
sudo snap install microk8s --classic
```

### 2) 현재 사용자를 microk8s 그룹에 추가 (sudo 없이 명령어 사용 목적)

```bash
sudo usermod -a -G microk8s $USER
sudo chown -f -R $USER ~/.kube
```

### 3) 변경사항 적용을 위해 세션 재시작 (또는 로그아웃 후 재로그인)

```bash
newgrp microk8s
```

### 4) 쿠버네티스 상태 확인 (Ready 상태가 될 때까지 잠시 대기)

```bash
microk8s status --wait-ready
```

## 3. 필수 애드온 및 Helm 활성화
ArgoCD가 원활하게 구동되려면 내부 DNS와 스토리지 기능이 필요하다.

MicroK8s는 Helm을 자체 내장하고 있어 명령어 하나로 활성화할 수 있다.

### 1) DNS, 스토리지 기능 활성화

```bash
microk8s enable dns storage
```

### 2) Helm3 활성화

```bash
microk8s enable helm3
```

### 3) 명령어 편의를 위해 alias(별칭) 설정 (.bashrc에 추가 추천)

```bash
alias kubectl='microk8s kubectl'
alias helm='microk8s helm3'
```

## 4. Helm으로 ArgoCD 설치하기
쿠버네티스 준비가 끝났으니, Helm을 이용해 ArgoCD를 배포한다.

### 1) ArgoCD를 설치할 네임스페이스 생성

```bash
kubectl create namespace argocd
```

### 2) ArgoCD 공식 Helm 리포지토리 추가

```bash
helm repo add argo https://argoproj.github.io/argo-helm
```

### 3) 리포지토리 최신화

```bash
helm repo update
```

### 4) Helm 차트를 이용해 ArgoCD 설치

```bash
helm install argocd argo/argo-cd --namespace argocd
```

### 5) ArgoCD 상태 확인
설치 명령 후, 아래 명령어로 ArgoCD의 모든 Pod가 Running 상태가 될 때까지 1~2분 정도 기다린다.

```bash
kubectl get pods -n argocd
```

## 5. ArgoCD 접속 및 로그인

### 1) 외부 접속 허용하기 (Service 변경)
기본적으로 ArgoCD 서버는 클러스터 내부에서만 접속할 수 있는 ClusterIP로 설정되어 있다.

Ubuntu 포트를 통해 외부 브라우저에서 접속할 수 있도록 NodePort 방식으로 임시 변경해 준다.

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
```

변경된 외부 포트(30000~32767 사이의 랜덤 포트)를 확인한다.

```bash
kubectl get svc argocd-server -n argocd
```

> 출력 예시: `80:31953/TCP, 443:31244/TCP` 이 경우 `https://Ubuntu_IP:31244`로 접속하면 된다.

### 2) 초기 비밀번호 확인
ArgoCD 웹 UI의 기본 아이디는 `admin`이며, 비밀번호는 쿠버네티스 시크릿에 암호화되어 저장되어 있다. 

복호화된 비밀번호를 확인한다.

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

## [출처 및 참고]
* [https://canonical.com/microk8s/docs](https://canonical.com/microk8s/docs)
* [https://github.com/argoproj/argo-helm](https://github.com/argoproj/argo-helm)
* [https://argo-cd.readthedocs.io/en/stable/getting_started](https://argo-cd.readthedocs.io/en/stable/getting_started)
* [https://argo-cd.readthedocs.io/en/stable/cli_installation](https://argo-cd.readthedocs.io/en/stable/cli_installation)
* [https://argo-cd.readthedocs.io/en/stable](https://argo-cd.readthedocs.io/en/stable)
