---
title: CentOS 9에 Docker 이미지로 GitLab Runner 설치
author: dejavuhyo
date: 2024-08-07 08:40:00 +0900
categories: [DevOps, Git]
tags: [centos-gitlab-runner, gitlab-runner, gitlab-runner-docker, gitlab-runner-docker-compose, gitlab-runner-install]
---

## 1. GitLab Runner Docker 이미지 pull
GitLab Runner 최신 안정된 릴리즈 `latest` 이미지를 pull 한다.

```shell
$ docker pull gitlab/gitlab-runner:latest
```

## 2. Docker 사용

### 1) 이미지 실행

```shell
$ docker run -d --name gitlab-runner gitlab/gitlab-runner:latest
```

## 3. Docker Compose 사용

### 1) docker-compose.yml 생성

```yml
version: '3.9'
services:
  gitlab-runner:
    image: gitlab/gitlab-runner:latest
    container_name: gitlab-runner
    restart: always
    volumes:
      - '~/gitlab-runner/config:/etc/gitlab-runner'
      - '/var/run/docker.sock:/var/run/docker.sock'
```

### 2) docker-compose 실행

```shell
$ docker-compose up -d
```

## 4. GitLab Runner 등록

### 1) GitLab Admin Area 페이지 이동

![admin-area](/assets/img/2024-08-07-install-gitlab-runner-docker-centos-9/admin-area.png)

### 2) CI/CD > Runners 페이지 이동
`New instance runner` 클릭한다.

![new-instance-runner](/assets/img/2024-08-07-install-gitlab-runner-docker-centos-9/new-instance-runner.png)

### 3) Tags 항목
`Run untaged jobs` 체크, `Create runner` 클릭한다

![create-runner](/assets/img/2024-08-07-install-gitlab-runner-docker-centos-9/create-runner.png)

### 4) Register runner
`Step1` 명령어 복사한다.

![setp1](/assets/img/2024-08-07-install-gitlab-runner-docker-centos-9/setp1.png)

### 4) GitLab Runner 컨테이너 접속

```shell
$ docker exec -it gitlab-runner /bin/bash
```

복사한 `Step1` 명령어 실행한다.

* Enter the GitLab instance URL: 아무것도 입력하지 않고 Enter 키를 누른다. `--url` 값이 설정된다.

* Enter the registration token: 아무것도 입력하지 않고 Enter 키를 누른다. `--token` 값이 설정된다.

* Enter a name for the runner: 러너에 대한 이름을 입력하고 Enter 키를 누른다. (예: `workshop runner`)

* Enter tags for the runner: 아무것도 입력하지 않고 Enter 키를 누른다.

* Enter an executor: `docker`을 입력하고 Enter 키를 누른다.

* Enter the default Docker image: `alpine:latest` 입력하고 Enter 키를 누른다.

### 5) 정상 등록 확인
`You’ve registered a new runner!` 문구가 나타난다.

![registered](/assets/img/2024-08-07-install-gitlab-runner-docker-centos-9/registered.png)

### 6) CI/CD > Runners 페이지 이동
등록된 러너가 목록에 나타난다.

![runners](/assets/img/2024-08-07-install-gitlab-runner-docker-centos-9/runners.png)

## 5. GitLab Runner 구성
GitLab Runner 및 등록된 개별 Runner의 동작을 변경할 수 있다.

GitLab Runner의 구성을 변경하려면 `config.toml` 파일을 수정해야 한다.

대부분의 옵션을 변경할 때 GitLab Runner를 다시 시작할 필요가 없다. 여기에는 `listen_address`를 제외한 `[[runners]]` 섹션의 파라미터와 글로벌 섹션의 대부분의 파라미터가 포함된다.

GitLab Runner는 3 초마다 구성 수정사항을 확인하고 필요한 경우 다시 로드한다.

### 1) Job 동시성(concurrency) 설정
GitLab Runner가 동시에 여러 Job을 실행할 수 있도록 적절하게 concurrent을 수정한다.

예를 들어, 4vCPU/16GiB인 경우 `concurrent = 4`로 설정한다.

GitLab.com의 자동 확장(Auto-scaling) Shared Runner는 단일 작업이 1vCPU와 3.75GiB를 사용하여 단일 인스턴스에서 실행되도록 구성된다.

### 2) Docker 특권(privileged) 모드 설정
아래 CI 파이프라인(`.gitlab-ci.yml`)과 같이, Docker-in-Docker 컨테이너를 사용하여 docker build와 같은 스크립트를 실행하기 위해서는 특권 모드(privileged mode) 설정이 필요하다.

```yml
image: docker:git
services:
- docker:dind

build:
  script:
  - docker build -t my-image .
  - docker push my-image
```

### 3) 설정 방법
`[runners.docker]` 섹션에서 `privileged = true`로 설정한다.

Runner 옵션을 수정하려면 Runner 작업 디렉토리(예: /gitlab-runner)에서 아래 명령을 실행하고 수정한다.

```shell
$ vi gitlab-runner/config/config.toml
```

수정한 `config.toml` 파일이다.

```text
concurrent = 4
check_interval = 0
shutdown_timeout = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "demo runner"
  url = "http://127.0.0.1"
  id = 1
  token = "glrt-ne9Cd9UsTGiqHf7fxL2v"
  token_obtained_at = 2024-07-18T07:04:01Z
  token_expires_at = 0001-01-01T00:00:00Z
  executor = "docker"
  [runners.custom_build_dir]
  [runners.cache]
    MaxUploadedArchiveSize = 0
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
  [runners.docker]
    tls_verify = false
    image = "alpine:latest"
    privileged = true
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
    network_mtu = 0
```

## [출처 및 참고]
* [https://workshop.infograb.io/setup-gitlab/4_setup_gitlab_runner/](https://workshop.infograb.io/setup-gitlab/4_setup_gitlab_runner/)
