---
title: GitHub Actions ArgoCD Kubernetes 배포
author: dejavuhyo
date: 2026-05-28 11:00:00 +0900
categories: [DevOps, CI/CD]
tags: [github, github-actions, argocd, docker, kubernetes, microk8s, ci-cd, gitops]
---

## 1. 서버 IP 및 환경

* Kubernetes Master: 192.168.98.129

* Kubernetes Node1: 192.168.98.131

* Kubernetes Node2: 192.168.98.133

* ArgoCD: 192.168.98.135

* GitHub Repository: demo-app, demo-gitops

## 2. 전체 파이프라인 흐름 (Workflow)

① Developer: `demo-app`(소스 코드 저장소)에 코드를 푸시한다.

② GitHub Actions (`demo-app`): 코드를 빌드하여 컨테이너 이미지를 생성하고 Registry에 푸시한다.

③ GitHub Actions (`demo-app` → `demo-gitops`): 개인 액세스 토큰(PAT)을 사용하여 `demo-gitops`(배포 저장소)의 매니페스트 파일 내 이미지 태그를 자동으로 업데이트하고 푸시한다.

④ ArgoCD: 오직 `demo-gitops`만 바라보고 있으며, 변경 사항을 감지하여 `192.168.98.129` K8s 서버에 동기화한다.

## 3. 소스 코드 저장소 설정 (`demo-app`)

![github](/assets/img/2026-05-28-github-actions-argocd-kubernetes-deploy/github.png)

### 1) GitHub Actions 워크플로우 설정
`demo-app` repository 루트에 `.github/workflows/ci.yml` 파일을 생성한다.

소스 코드가 업데이트되면 빌드 후 `demo-gitops`를 업데이트하는 역할을 한다.

```yaml
name: CI and Trigger CD

on:
  push:
    branches:
      - main

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Application Code
      uses: actions/checkout@v4

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v3

    - name: Login to Docker Hub
      uses: docker/login-action@v3
      with:
        username: ${{ secrets.DOCKERHUB_USERNAME }}
        password: ${{ secrets.DOCKERHUB_TOKEN }}

    # 1. Docker 이미지 빌드 및 푸시
    - name: Build and push Docker image
      uses: docker/build-push-action@v5
      with:
        context: .
        push: true
        tags: ${{ secrets.DOCKERHUB_USERNAME }}/my-app:${{ github.sha }}

    # 2. GitOps 배포 레포지토리(demo-gitops) 체크아웃
    - name: Checkout Deployment Repository
      uses: actions/checkout@v4
      with:
        repository: '본인의-GitHub-이름/demo-gitops' # 배포용 레포지토리 이름
        token: ${{ secrets.GH_PAT }} # 사전 준비에서 등록한 PAT
        path: demo-gitops # 현재 작업 디렉토리 내에 다운로드받을 폴더명

    # 3. 배포 레포지토리 안의 매니페스트 파일 이미지 태그 변경
    - name: Update Kubernetes manifest
      run: |
        cd demo-gitops
        sed -i 's|image: .*|image: '${{ secrets.DOCKERHUB_USERNAME }}'/my-app:'${{ github.sha }}'|g' deployment.yaml

    # 4. 배포 레포지토리에 변경사항 커밋 및 푸시
    - name: Commit and Push to GitOps Repo
      run: |
        cd demo-gitops
        git config --global user.name "github-actions[bot]"
        git config --global user.email "github-actions[bot]@users.noreply.github.com"
        git add deployment.yaml
        git commit -m "chore: update image tag to ${{ github.sha }} from demo-app"
        git push origin main
```

### 2) 배포 매니페스트 저장소 설정 (`demo-gitops`)
별도로 새로 만든 비어있는 `demo-gitops` repository의 루트(Root) 경로에 Kubernetes 매니페스트 파일들을 생성한다.

* deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: default
spec: # 1. Deployment 자체의 설정을 시작하는 spec
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec: # 2. template(Pod) 내부의 설정을 시작하는 spec
      containers:
      - name: my-app
        image: your-dockerhub-username/my-app:latest # GitHub Actions에 의해 자동으로 업데이트되는 항목
        ports:
        - containerPort: 8080
    # 비공개 레포지토리일 경우 추가 (containers와 같은 들여쓰기 라인)
    imagePullSecrets:
      - name: dockerhub-secret # 쿠버네티스에 미리 생성해둔 시크릿 이름
```

* service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service   # 서비스의 이름 (이름 자체가 도메인 주소 역할을 함)
  namespace: default
spec:
  type: ClusterIP        # 서비스 종류 (ClusterIP, NodePort, LoadBalancer 등)
  selector:
    app: my-app          # 어떤 Pod들을 묶어줄지 지정 (deployment.yaml의 label과 일치해야 함)
  ports:
  - protocol: TCP
    port: 80             # 서비스가 외부(또는 내부 다른 앱)로부터 요청을 받을 포트
    targetPort: 8080     # Pod(컨테이너) 내부에서 실제로 실행 중인 앱의 포트
```

### 3) Dockerfile 생성

![docker](/assets/img/2026-05-28-github-actions-argocd-kubernetes-deploy/docker.png)

Java 21, Spring Boot, Maven 환경의 Dockerfile을 생성한다.

Java 21부터는 경량화와 보안성이 뛰어난 Eclipse Temurin 공식 이미지를 사용하는 것이 표준적이다.

빌드 속도와 컨테이너 용량을 최적화하기 위해 멀티 스테이징(Multi-stage) 구조로 작성된 Dockerfile이다.

`demo-app` 최상위에 생성한다.

```dockerfile
# ==========================================
# 1단계: 빌드 스테이지 (Maven + Java 21 환경)
# ==========================================
FROM maven:3.9.6-eclipse-temurin-21 AS builder
WORKDIR /build

# 의존성(Dependencies) 라이브러리를 먼저 다운로드하여 캐싱 (빌드 속도 향상)
COPY pom.xml .
RUN mvn dependency:go-offline -B

# 소스 코드를 복사하고 가볍게 패키징 (테스트 제외)
COPY src ./src
RUN mvn clean package -DskipTests

# ==========================================
# 2단계: 실행 스테이지 (가벼운 Java 21 JRE 환경)
# ==========================================
FROM eclipse-temurin:21-jre-jammy
WORKDIR /app

# 1단계 builder에서 생성된 최종 jar 파일만 실행 스테이지로 복사
COPY --from=builder /build/target/*.jar app.jar

# Spring Boot 내부 톰캣 포트 노출 (기본값: 8080)
EXPOSE 8080

# 가상 머신 최적화 옵션을 원하시면 여기에 추가 가능
# Java 21의 가상 스레드(Virtual Threads) 등을 활용할 때 기본 옵션으로 충분하다.
ENTRYPOINT ["java", "-jar", "app.jar"]
```

## 4. MicroK8s 기반 ArgoCD와 K8s 연동

![kubernetes](/assets/img/2026-05-28-github-actions-argocd-kubernetes-deploy/kubernetes.png)

### 1) 129번 K8s 서버 외부 접속 주소 재확인
129번 K8s 서버의 인증 파일 내 서버 주소가 내부 루프백(`127.0.0.1`)이 아닌 외부에 열려있는 IP인지 확인한다.

```bash
# 129번 서버에서 실행
cat ~/.kube/config | grep server
```

`server: https://192.168.98.129:6443`과 같이 129번 IP가 정상적인지 확인한다.

### 2) 135번 MicroK8s 서버로 K8s 인증 파일 복사
135번 서버는 MicroK8s가 설치되어 있으므로, 129번의 인증 설정을 MicroK8s 사용자 홈 디렉토리에 격리된 파일로 가져온다.

```bash
# 135번 서버(MicroK8s)에서 실행
mkdir -p ~/.kube

# 129번 서버의 K8s 설정을 복사해옴
scp root@192.168.98.129:~/.kube/config ~/.kube/config-129
```

135번 서버에서 129번 원격 클러스터를 테스트해야 한다.

이때 MicroK8s 내부 전용 툴(microk8s kubectl)을 이용하거나 패키지 관리자로 설치한 일반 kubectl을 이용해 검증한다.

```bash
export KUBECONFIG=~/.kube/config-129

# MicroK8s 명령어 뒤에 조회를 붙여 129번 노드가 나오는지 확인합니다.
microk8s kubectl get nodes
```

정상적으로 129번 노드들이 출력된다면 연결 통로는 확보된 것이다.

이때 사용된 컨텍스트(Context) 이름을 조회해 둔다.

```bash
microk8s kubectl config get-contexts -o name
```

* 예시 결과: `kubernetes-admin@kubernetes`

### 3) 135번 MicroK8s 서버 ArgoCD CLI 설치 및 로그인

![argocd](/assets/img/2026-05-28-github-actions-argocd-kubernetes-deploy/argocd.png)

```bash
# ArgoCD CLI 설치
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64

sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd

rm argocd-linux-amd64

# 135번 내부 ArgoCD 서비스에 로그인
argocd login 192.168.98.135 --username admin --password <본인_ArgoCD_비밀번호>
```

> 인증서 경고(Proceed insecurely?) 문구가 나오면 y를 누른다.

### 4) 135번 MicroK8s 서버 최종 클러스터 등록 명령 실행
MicroK8s 환경에서 외부에 있는 다른 쿠버네티스 클러스터를 조작 권한에 편입시킨다. 환경변수를 실행한 터미널 창에서 실행한다.

```bash
export KUBECONFIG=~/.kube/config-129

# argocd cluster add [단계 2에서 획득한 컨텍스트명]
argocd cluster add kubernetes-admin@kubernetes
```

### 5) 연동 후 확인
135번 MicroK8s 내부에서 구동 중인 ArgoCD가 129번 K8s로 자격 증명용 ServiceAccount를 설정한다. 연동이 완료 되었는지 목록을 확인한다.

```bash
argocd cluster list
```

* 결과 예시

```text
SERVER                          NAME                         VERSION   STATUS      MESSAGE                                                  PROJECT
https://192.168.98.129:6443     kubernetes-admin@kubernetes  v1.30.14  Successful
https://kubernetes.default.svc  in-cluster                   1.34.5    Unknown     Cluster has no applications and is not being monitored.
```

`STATUS`가 `Successful`이면 연동이 성공한 것이다.

### 6) connection refused 에러 발생할 경우
135번 서버(MicroK8s)에서 실행한 ArgoCD CLI가 자기 자신(192.168.98.135)의 443번 포트(HTTPS)로 접속하려고 시도했으나, 보안이나 서비스 미구동 등의 이유로 연결이 거부(Connection Refused)되었을 때 발생한다.

① MicroK8s 내에서 ArgoCD 서비스 상태 확인

```bash
# ArgoCD 관련 모든 자원 상태 확인
microk8s kubectl get all -n argocd
```

* 확인할 부분: `pod/argocd-server-...` 항목의 STATUS가 `Running` 상태인지 확인한다.

* 만약 `argocd` 네임스페이스가 없다면 `microk8s kubectl get ns`로 ArgoCD가 어디에 설치되었는지 확인해야 한다.

② 외부 노출 포트(NodePort 또는 Port-Forward) 확인
ArgoCD API 서버가 내부적으로는 잘 돌고 있지만, 135번 서버의 443번 포트로 연결할 수 있는 길(네트워크 매핑)이 없어서 에러가 난 것이다.

* NodePort 서비스로 변경
ArgoCD 서비스 타입을 ClusterIP에서 NodePort로 바꾸어 외부 포트를 영구적으로 열어주는 방법이다.

```bash
# ArgoCD 서버 서비스 수정 창 열기
microk8s kubectl edit svc argocd-server -n argocd
```

* `type: ClusterIP` 부분을 `type: NodePort`로 수정

③ 바뀐 포트 번호를 확인

```bash
microk8s kubectl get svc argocd-server -n argocd
```

출력 결과 중 `PORT(S)` 항목에 `443:3xxxx/TCP` 형태로 `30000~32767` 사이의 포트(예: 32001)가 지정된 것을 볼 수 있다.

④ NodePort 번호를 붙여서 로그인

```bash
argocd login 192.168.98.135:3xxxx --username admin --password <비밀번호>
```

## 5. ArgoCD 설정 (192.168.98.135)
ArgoCD는 소스 코드가 들어있는 레포지토리가 아닌, `demo-gitops`를 바라보도록 설정해야 한다.

```

### 1) ArgoCD에 배포 저장소 연동
Kubernetes Secret을 이용하여 ArgoCD에 GitHub 저장소를 등록한다.

* argocd-repo-secret.yaml

```yaml
metadata:
  name: github-private-repo-secret # Secret의 이름 (원하는 대로 지정 가능)
  namespace: argocd                # ArgoCD가 설치된 네임스페이스 (기본값: argocd)
  labels:
    argocd.argoproj.io/secret-type: repository # ArgoCD가 저장소로 인식하게 하는 라벨
stringData:
  type: git
  url: https://github.com/<GitHub_사용자명>/<저장소명>.git
  username: <GitHub_사용자명>
  password: <발급받은_Personal_Access_Token> # GitHub 비밀번호가 아닌 PAT를 입력
```

* 실행

```bash
microk8s kubectl apply -f argocd-repo-secret.yaml
```

### 2) ArgoCD Application 생성
`+ NEW APP`을 클릭하여 애플리케이션을 생성한다.

| 설정 항목 | 입력 값 및 설명 |
|:-----:|:-----:|
| Application Name | `demo-gitops-app` |
| Project Name | `default` |
| Sync Policy | `Automatic` |
| Repository URL | demo-gitops 주소 (`https://github.com/유저명/gitops-repo.git`) |
| Revision | HEAD 또는 main |
| Path | `.` (매니페스트 파일들이 루트 경로에 있으므로 점 입력) |
| Cluster URL | `https://192.168.98.129:6443` (대상 K8s 서버) |
| Namespace | `default` |

## 6.배포 완료 확인
모든 설정(GitHub Actions → Docker Hub → ArgoCD → 192.168.98.129 K8s)을 마치고 코드를 푸시했다면, 배포가 성공적으로 완료되었는지 단계별로 확인해야 한다.

### 1) GitHub Actions 빌드 확인 (CI 단계)
가장 먼저 코드가 Docker Hub에 올라갔는지 확인한다.

* `CI/CD Pipeline`에서 모든 스텝(Step)에 녹색 체크 표시가 되어 있는지 확인한다.

### 2) ArgoCD 동기화 상태 확인 (CD 단계)
GitHub Actions가 배포 저장소(`demo-gitops`)를 성공적으로 수정했다면, ArgoCD가 이를 감지했는지 확인한다.

* ArgoCD Web UI에 접속하여 생성한 애플리케이션(my-gitops-app)을 확인한다.
  - Synced: GitHub 저장소의 파일과 K8s 상태가 일치함
  - Healthy: K8s에 배포된 Pod가 에러 없이 정상 구동 중임

### 3) Kubernetes 클러스터 상태 확인 (K8s 단계)
ArgoCD가 완료되었다고 해도 실제 192.168.98.129 (K8s 서버) 내부에서 Spring Boot가 정상적으로 부팅되었는지 확인한다.

#### (1) Pod 상태 및 이름 확인

```bash
kubectl get pods -n default
```

* 확인
  - STATUS: Running
  - READY: 1/1

#### (2) Spring Boot 실시간 로그 확인

```bash
# kubectl logs -f [포드이름]
kubectl logs -f my-app-xxxxxxxxx-xxxxx
```

#### (3) 서비스 및 포트 확인
129번 서버로 들어오는 포트를 확인한다.

```bash
kubectl get svc my-app-service -n default
```

* 확인
  - TYPE: NodePort
  - PORT(S): 80:3XXXX/TCP

### 4) 브라우저 직접 접속 확인 (최종 단계)
브라우저에서 129번 서버의 해당 포트로 배포된 애플리케이션을 호출 한다.

* 접속 주소: `http://192.168.98.129:<3XXXX 포트번호>`

## [출처 및 참고]
* [https://kubernetes.io/docs/concepts/services-networking/service/](https://kubernetes.io/docs/concepts/services-networking/service/)
* [https://argo-cd.readthedocs.io/en/stable/](https://argo-cd.readthedocs.io/en/stable/)
* [https://argo-cd.readthedocs.io/en/stable/user-guide/commands/argocd_cluster_add/](https://argo-cd.readthedocs.io/en/stable/user-guide/commands/argocd_cluster_add/)
