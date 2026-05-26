---
title: ArgoCD에서 Personal Access Token 방식으로 GitHub 연동 방법
author: dejavuhyo
date: 2026-05-26 10:00:00 +0900
categories: [GitOps, ArgoCD]
tags: [argocd, argocd-github, gitops, kubernetes-secret]
---

## 1. 개요
Ubuntu 환경에서 ArgoCD와 GitHub을 Personal Access Token(PAT) 방식으로 연동하는 방법이다.

안전하고 권장되는 방식인 HTTPS Repository 연동 기준이다.

## 2. GitHub에서 Personal Access Token (PAT) 발급
ArgoCD가 GitHub 저장소에 접근할 수 있도록 토큰을 생성한다.

### 1) GitHub Settings
GitHub 로그인 후 Settings → Developer Settings → Personal Access Tokens → Tokens (classic)

> 참고: Fine-grained tokens도 가능하지만, ArgoCD 연동에는 classic이 직관적이다.

### 2) Token 생성
`Generate new token (classic)`을 클릭한다.

![token](/assets/img/2026-05-26-argocd-github-connection/token.png)

### 3) Note 입력

* Note: 토큰 용도(예: argocd-token)를 입력

### 4) `Select scopes`에서 권한 체크

* public_repo: 공개 저장소인 경우

* repo: 비공개(Private) 저장소인 경우 (전체 체크 필요)

### 5) Token 생성
**Generate token**을 클릭하고, 생성된 토큰 값을 반드시 따로 복사한다. (페이지를 벗어나면 다시 볼 수 없음)

## 3. ArgoCD에 GitHub 저장소 연동
Kubernetes Secret을 이용하여 ArgoCD에 GitHub 저장소를 등록하는 방식은 설정 자체를 선언적(Declarative)으로 관리할 수 있어 GitOps 운영에 가장 추천하는 방식이다.

ArgoCD는 `argocd` 네임스페이스 내에 특정 라벨(`argocd.argoproj.io/secret-type: repository`)을 가진 Secret이 생성되면, 이를 자동으로 감지하여 저장소(Repository)로 등록한다.

### 1) Secret YAML 파일 작성
MicroK8s가 설치된 별도 서버의 터미널에서 저장소 정보를 담은 YAML 파일을 생성한다.

* 파일 생성

```bash
vi argocd-repo-secret.yaml
```

* 작성 내용

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

### 2) MicroK8s 명령어로 Secret 적용
작성한 파일을 `microk8s kubectl` 명령어를 사용하여 클러스터에 배포한다.

* 배포

```bash
microk8s kubectl apply -f argocd-repo-secret.yaml
```

* 정상 적용 시 출력

```text
secret/github-private-repo-secret created
```

## 4. 연동 상태 확인

### 1) 웹 UI
브라우저로 ArgoCD UI에 접속한다.

* Settings → Repositories 메뉴로 이동

![repositories](/assets/img/2026-05-26-argocd-github-connection/repositories.png)

* 목록에 등록한 GitHub URL이 보이고, `CONNECTION STATUS`가 `Successful` (녹색 체크)로 표시되면 성공이다.

### 2) Failed (연결 실패)인 경우
만약 상태가 Failed로 뜬다면 대부분 다음 두 가지 원인이다.

* PAT 토큰 권한 부족: GitHub에서 토큰을 만들 때 `repo` 권한(비공개 저장소인 경우)을 체크했는지 확인 한다.

* 오타: YAML 파일 내의 `url`, `username`, `password` 경로에 오타가 없는지 확인 후, 파일을 수정하여 다시 `microk8s kubectl apply -f argocd-repo-secret.yaml` 명령어를 실행하면 갱신된다.

## [출처 및 참고]
* [https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
* [https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/)
* [https://argo-cd.readthedocs.io/en/stable/user-guide/private-repositories/](https://argo-cd.readthedocs.io/en/stable/user-guide/private-repositories/)
* [https://canonical.com/microk8s/docs/command-reference](https://canonical.com/microk8s/docs/command-reference)
