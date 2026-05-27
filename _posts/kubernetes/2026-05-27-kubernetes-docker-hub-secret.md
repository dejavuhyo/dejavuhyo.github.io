---
title: Kubernetes에 Docker Hub Secret 생성 방법
author: dejavuhyo
date: 2026-05-27 09:50:00 +0900
categories: [DevOps, Kubernetes]
tags: [kubernetes, docker, docker-hub, docker-hub-secret]
---

## 1. Docker Hub Secret
쿠버네티스(Kubernetes)에서 Docker Hub의 프라이빗 리포지토리에 접근하거나 Image Pull 제한을 해결하기 위해 인증 정보를 담은 시크릿(imagePullSecret)을 생성한다.

가장 쉽고 직관적인 CLI(명령어) 방식과 관리하기 좋은 YAML 선언 방식이 있다.

> Docker Hub의 로그인 비밀번호 대신, Docker Hub 웹사이트(Account Settings → Security → New Access Token)에서 개인 액세스 토큰(Personal Access Token)을 발급받아 비밀번호 자리에 사용하는 것을 강력히 권장한다.

## 2. (방법 1) kubectl 명령어로 바로 생성
터미널에서 `kubectl create secret docker-registry` 명령어를 사용하면 복잡한 인코딩 과정 없이 한 줄로 시크릿을 만들 수 있다.

```bash
kubectl create secret docker-registry <시크릿_이름> \
  --docker-server=docker.io \
  --docker-username=<도커허브_아이디> \
  --docker-password=<도커허브_비밀번호_또는_토큰> \
  --docker-email=<도커허브_이메일> \
  -n <네임스페이스>
```

* `<시크릿_이름>`: 원하는 이름으로 지정 (예: dockerhub-secret)

* `--docker-server~: Docker Hub의 경우 `docker.io`를 입력

* `-n <네임스페이스>`: 파드(Pod)가 생성될 네임스페이스와 동일한 곳에 생성 (생략 시 `default` 네임스페이스에 생성)

## 3. (방법 2) YAML 파일로 선언하여 생성하기
GitOps나 파일 관리가 필요하다면, 먼저 Docker Hub 로그인 정보가 담긴 `config.json` 내용을 Base64로 인코딩한 뒤 YAML 파일로 작성해야 한다.

### 1) local 환경에서 Docker 로그인 후 설정 파일 확인

```bash
docker login
cat ~/.docker/config.json
```

출력되는 JSON 내용 전체를 복사한다.

### 2) JSON 내용을 Base64로 인코딩

```bash
# 리눅스/맥 기준 (복사한 JSON 문자열을 그대로 넣거나 파일을 변환)
cat ~/.docker/config.json | base64 | tr -d '\n'
```

### 3) `secret.yaml` 파일 작성

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: dockerhub-secret
  namespace: default # 파드를 배포할 네임스페이스
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: <2단계에서_복사한_Base64_암호문>
```

### 4) 쿠버네티스 적용

```bash
kubectl apply -f secret.yaml
```

## 4. Deployment에 적용하는 방법
시크릿을 생성했다면, 이미지를 다운로드받을 Deployment의 YAML 설정에 `imagePullSecrets` 옵션을 반드시 추가해야 쿠버네티스가 이 인증 정보를 사용한다.

* deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: <도커허브_아이디>/<프라이빗_리포지토리_이름>:latest
      # 이 부분 추가
      imagePullSecrets:
      - name: dockerhub-secret # 위에서 생성한 시크릿 이름
```

## [출처 및 참고]
* [https://kubernetes.io/ko/docs/tasks/configure-pod-container/pull-image-private-registry/](https://kubernetes.io/ko/docs/tasks/configure-pod-container/pull-image-private-registry/)
* [https://kubernetes.io/ko/docs/tasks/configure-pod-container/pull-image-private-registry/](https://kubernetes.io/ko/docs/tasks/configure-pod-container/pull-image-private-registry/)
* [https://docs.docker.com/security/access-tokens/](https://docs.docker.com/security/access-tokens/)
