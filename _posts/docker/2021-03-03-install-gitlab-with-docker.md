---
title: Docker를 이용하여 GitLab 설치
author: dejavuhyo
date: 2021-03-03 06:10:00 +0900
categories: [DevOps, Git]
tags: [docker-gitlab-install, gitlab-install, docker-gitlab, 도커-깃랩-설치, 깃랩-설치, 도커-깃랩]
---

## 1. GitLab Docker images 다운로드

* <https://hub.docker.com/r/gitlab/gitlab-ce>

![gitlab-docker-images](/assets/img/2021-03-03-install-gitlab-with-docker/gitlab-docker-images.png)

```bash
[root@localhost ~]# docker pull gitlab/gitlab-ce:latest
```

## 2. Pull 이미지 확인

```bash
[root@localhost ~]# docker images
```

## 3. GitLab Images 실행
도커 포트 매핑은 호스트 포트:컨테이너 포트로 이루어진다. 즉, 웹에서 접근시 서버 주소:80으로 접속할 수 있고 SSH로 GitLab 접속시 서버 주소와 ssh 22 포트로 접속할 수 있다.

* 포트 번호 설명
  - 22: SSH
  - 80: HTTP
  - 443: HTTPS

```bash
 [root@localhost ~]# docker run --detach --hostname 127.0.0.1 --publish 4443:443 --publish 9080:80 --publish 2222:22 --name gitlab --restart always --volume /srv/gitlab/config:/etc/gitlab --volume /srv/gitlab/logs:/var/log/gitlab --volume /srv/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce:latest
```

## 4. Gitlab Configuration 설정
포트를 변경하였기 때문에 내부 설정도 변경한다.

### 1) gitlab 컨테이너 접속

```bash
[root@localhost ~]# docker exec -it gitlab /bin/bash
```

### 2) nano 에디터로 gitlab.rb 파일 수정

```bash
root@127:/# nano /etc/gitlab/gitlab.rb
```

```text
external_url 'http://127.0.0.1:9080'
gitlab_rails['gitlab_shell_ssh_port'] = 2222
```

### 3) 설정 적용
Ctrl + o로 저장하고 Enter를 누른 후 Ctrl + x로 나노 에디터를 나온다.

```bash
root@127:/# gitlab-ctl reconfigure
```

### 4) Docker 재시작

```bash
[root@localhost ~]# docker restart gitlab
```

## 5. 접속

* <http://127.0.0.1:9080>

## 6. 참고: Error

### 1) gitlab 이름의 container가 있음

```text
docker: Error response from daemon: Conflict. The container name "/gitlab" is already in use by container "7cc5f742bee7b7fd1abfb7bda954e02fd82f8cd87eeddb327e891a2e46446e98". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
```

### 2) 22번 포트 사용중

```text
docker: Error response from daemon: driver failed programming external connectivity on endpoint gitlab (4e9c2e29d9490c79ed30a40a5d1593ace6584c488000b2185e63990f11f3b504): Error starting userland proxy: listen tcp4 0.0.0.0:22: bind: address already in use.
```

## [출처 및 참고]
* <https://docs.gitlab.com/omnibus/docker/>
* <https://mrgamza.tistory.com/764>
* <https://stackoverflow.com/questions/31697828/docker-error-name-is-already-in-use-by-container>
