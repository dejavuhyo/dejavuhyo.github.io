---
title: Kubernetes 노드 NotReady 해결 방법
author: dejavuhyo
date: 2026-05-15 10:30:00 +0900
categories: [DevOps, Kubernetes]
tags: [kubernetes, kubeadm, kubelet, kubectl, kubernetes-notready, node-notready]
---

## 1. 증상
쿠버네티스 클러스터를 운영하다 보면 재부팅 후 노드가 NotReady 상태는 흔한 일이다.

Ubuntu 환경에서 노드 하나만 문제가 생겼다면, 해당 노드의 특정 설정이 재부팅 시 초기화되었거나 서비스가 정상적으로 올라오지 않았을 가능성이 높다.

kubelet 서비스 자체는 실행 중(Active)이지만, 내부에 `Unable to read config path`라는 에러가 반복되고 있다.

이는 보통 특정 설정 파일이나 디렉토리가 유실되었을 때 발생하지만, 노드가 NotReady인 상황에서 가장 먼저 의심해야 할 부분은 네트워크 브릿지 설정과 Swap이다.

## 2. Swap 메모리 상태 확인
쿠버네티스는 기본적으로 Swap 메모리가 비활성화되어 있어야 정상 작동한다. Ubuntu는 재부팅 시 `/etc/fstab` 설정에 따라 Swap을 다시 켤 수 있다.

### 1) 확인 방법
Swap 수치가 0인지 확인한다.

```bash
free -m
```

### 2) 해결 방법
재부팅 후에도 유지하려면 `/etc/fstab`에서 swap 관련 라인을 주석 처리한다.

```bash
sudo swapoff -a
```

## 3. Kubelet 서비스 상태 확인
쿠버네티스 에이전트인 `kubelet`이 제대로 실행 중인지 확인한다.

### 1) 확인 방법

```bash
sudo systemctl status kubelet
```

### 2) 증상
`Active: activating (auto-restart)` 상태이면 설정 오류나 런타임 문제로 무한 재시작 중인 것이다.

### 3) 로그 확인

```bash
ournalctl -u kubelet -f
```

## 4. 컨테이너 런타임(Docker/containerd) 확인
Kubelet이 살아있어도 컨테이너를 실행하는 런타임(containerd 또는 Docker)에 문제가 있으면 노드는 `NotReady`가 된다.

### 1) 확인 방법

```bash
## containerd로 실행하는 경우
sudo systemctl status containerd

## docker로 실행하는 경우
sudo systemctl status docker
```

> 재부팅 후 네트워크 브릿지 설정(br_netfilter)이 풀려서 통신이 안 되는 경우도 있으니 런타임 로그를 확인한다.

## 5. 네트워크 플러그인(CNI) 확인
노드 간 통신을 담당하는 CNI(Calico, Flannel 등) 포드가 정상적으로 스케줄링되었는지 확인한다.

### 1) 확인 방법
특정 네트워크 포드가 `CrashLoopBackOff` 상태라면 해당 노드의 네트워크 설정 문제일 확률이 높다.

```bash
kubectl get pods -n kube-system -o wide | grep <노드>
```

### 2) 상세 원인 파악 방법
마스터 노드에서 왜 `NotReady`인지 결정적인 이유를 알려준다.

```bash
kubectl describe node <노드>
```

## [출처 및 참고]
*[https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm)
