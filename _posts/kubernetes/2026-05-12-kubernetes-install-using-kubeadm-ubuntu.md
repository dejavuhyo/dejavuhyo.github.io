---
title: Ubuntu에서 kubeadm을 이용하여 Kubernetes 설치
author: dejavuhyo
date: 2026-05-12 10:00:00 +0900
categories: [DevOps, Kubernetes]
tags: [kubernetes, kubeadm, kubelet, kubectl, kubernetes-install, k8s, k8s-install, 쿠버네티스, 쿠버네티스-설치]
---

![kubernetes-logo](/assets/img/2026-05-12-kubernetes-install-using-kubeadm-ubuntu/kubernetes-logo.png)

## 1. Swap 비활성화
쿠버네티스는 성능과 안정성을 위해 스왑 메모리 사용을 권장하지 않는다.

### 1) 영구적으로 스왑 끄기 (재부팅 후에도 유지)

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

### 2) 설정 완료 확인

```bash
free -h
```

## 2. MAC 주소와 product_uuid 확인
모든 노드에서 MAC 주소와 product_uuid가 고유한지 확인한다.

### 1) 네트워크 인터페이스 MAC 주소 확인

```bash
ip link
```

### 2) product_uuid 확인

```bash
sudo cat /sys/class/dmi/id/product_uuid
```

## 3. 방화벽 해제 또는 포트 개방

### 1) 학습용이라면 ufw를 끄는 것이 편하다.

```bash
sudo ufw disable
```

### 2) 필요한 포트만 개방

* 마스터 노드에서 실행

```bash
# API Server
sudo ufw allow 6443/tcp

# etcd
sudo ufw allow 2379:2380/tcp

# Kubelet, Scheduler, Controller
sudo ufw allow 10250/tcp
sudo ufw allow 10259/tcp
sudo ufw allow 10257/tcp

# 방화벽 적용 및 상태 확인
sudo ufw reload
sudo ufw status
```

* 워커 노드에서 실행

```bash
# Kubelet API
sudo ufw allow 10250/tcp

# NodePort 서비스 (외부 서비스 노출용)
sudo ufw allow 30000:32767/tcp

# 방화벽 적용 및 상태 확인
sudo ufw reload
sudo ufw status
```

* 네트워크 플러그인(CNI) 포트

Flannel 사용 시: UDP 8472 (VXLAN 통신용)

```bash
sudo ufw allow 8472/udp
```

Calico 사용 시: TCP 179 (BGP 통신용)

```bash
sudo ufw allow 179/tcp
```

> 주의사항: 클라우드 환경(AWS, Azure, GCP 등)을 사용 중이라면 Ubuntu 내부의 `ufw`뿐만 아니라, 클라우드 콘솔의 인바운드 보안 그룹(Security Group) 설정에서도 동일한 포트들을 개방해야 통신이 가능하다.

## 4. 컨테이너 런타임(containerd) 설치
쿠버네티스는 컨테이너를 실행할 엔진이 필요하다. 현재 가장 많이 쓰이는 containerd를 설치한다.

### 1) 커널 모듈 로드 및 네트워크 설정

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# 필요한 네트워크 커널 매개변수 설정
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
```

### 2) containerd 설치

```bash
sudo apt-get update
sudo apt-get install -y containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml > /dev/null
# SystemdCgroup 설정을 true로 변경
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
sudo systemctl restart containerd
```

## 5. 쿠버네티스 구성 요소 설치 (kubeadm, kubelet, kubectl)

### 1) 레포지토리 업데이트 및 필요 패키지 설치

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
```

### 2) 구글 클라우드 공개 키 및 리포지토리 추가

```bash
sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

### 3) 패키지 설치 및 버전 고정

```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

## 6. 쿠버네티스 클러스터 초기화 - 마스터 노드에서만

### 1) 마스터 노드 활성화
`--pod-network-cidr`은 네트워크 플러그인(CNI)에 따라 달라지는데, 가장 대중적인 Calico 기준 값인 `192.168.0.0/16`을 사용한다.

```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

초기화가 완료되면 화면에 `kubeadm join ...`으로 시작하는 명령어가 뜬다.

이 명령어를 메모장 등에 복사한다. (워커 노드를 추가할 때 필요하다.)

### 2) 일반 사용자 계정 설정
일반 사용자 계정으로 kubectl을 사용하기 위한 설정을 한다.

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## 7. 네트워크 플러그인(CNI) 설치 - 마스터 노드에서만
노드 간 통신을 위해 CNI(Container Network Interface)를 설치해야 노드 상태가 Ready로 변한다.

```bash
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/tigera-operator.yaml
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/custom-resources.yaml
```

## 8. 설치 확인 - 마스터 노드에서만

```bash
kubectl get nodes
```

## 9. 워커 노드에서 클러스터 합류

### 1) 마스터 노드에서 가입 명령어 확인
마스터 노드 초기화(kubeadm init) 당시 마지막에 출력되었던 명령어를 사용한다.

만약 명령어를 잊어버렸거나 토큰이 만료(기본 24시간)되었다면, 마스터 노드에서 새 토큰을 생성한다.

```bash
kubeadm token create --print-join-command
```

### 2) 워커 노드에서 클러스터 합류

```bash
sudo kubeadm join 192.168.1.10:6443 --token <토큰값> --discovery-token-ca-cert-hash sha256:<해시값>
```

### 3) 연결 상태 확인 (마스터 노드에서 실행)

```bash
kubectl get nodes
```

## 10. 오류

### 1) Running pre-flight checks
이전에 쿠버네티스 설치나 join 시도를 했던 흔적이 남아 있어서 발생하는 전형적인 상황이다.

kubeadm은 시스템이 깨끗한 상태(Clean state)일 때만 작업을 진행하려고 하기 때문이다.

기존 설정을 초기화한 후 다시 시도하면 해결된다.

#### (1) 노드 초기화 (Clean Up)
워커 노드에서 다음 명령어를 실행하여 기존에 생성된 인증서와 설정 파일들을 삭제한다.

```bash
sudo kubeadm reset
```

#### (2) 남은 파일 수동 삭제
`reset` 명령어로도 다 지워지지 않는 네트워크 설정과 디렉토리를 수동으로 깔끔하게 정리한다.

```bash
sudo rm -rf /etc/kubernetes/ /var/lib/kubelet/ /etc/cni/net.d
```

#### (3) IP 테이블 초기화
네트워크 설정이 꼬이는 것을 방지하기 위해 방화벽 규칙을 초기화한다.

```bash
sudo iptables -F && sudo iptables -t nat -F && sudo iptables -t mangle -F && sudo iptables -X
```

## [출처 및 참고]
* [https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm/install-kubeadm/](https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
* [https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
* [https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/troubleshooting-kubeadm/](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/troubleshooting-kubeadm/)
* [https://github.com/containerd/containerd/blob/main/docs/getting-started.md](https://github.com/containerd/containerd/blob/main/docs/getting-started.md)
* [https://docs.tigera.io/calico/latest/getting-started/kubernetes/quickstart](https://docs.tigera.io/calico/latest/getting-started/kubernetes/quickstart)
* [https://www.youtube.com/watch?v=lheclzO-G7k&list=PLApuRlvrZKohaBHvXAOhUD-RxD0uQ3z0c&index=4](https://www.youtube.com/watch?v=lheclzO-G7k&list=PLApuRlvrZKohaBHvXAOhUD-RxD0uQ3z0c&index=4)
