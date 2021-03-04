---
title: Docker를 이용하여 Jenkins 설치
author: dejavuhyo
date: 2021-03-05 06:10:00 +0900
categories: [DevOps, Jenkins]
tags: [docker-jenkins-install, jenkins-install, docker-jenkins, 도커-젠킨스-설치, 젠킨스-설치, 도커-젠킨스]
---

## 1. Jenkins Docker images 다운로드

* <https://hub.docker.com/r/jenkins/jenkins>

![jenkins-docker-images](/assets/img/2021-03-05-install-jenkins-with-docker/jenkins-docker-images.png)

```bash
[root@localhost ~]# docker pull jenkins/jenkins
```

## 2. pull 이미지 확인

```bash
[root@localhost ~]# docker images
```

## 3. Jenkins Images 실행

```bash
[root@localhost ~]# docker run --name jenkins -p 9080:8080 -p 50000:50000 -v /var/jenkins_home jenkins:latest
```

## 4. Jenkins 로그 확인

```bash
[root@localhost ~]# docker logs jenkins
```

## 5. 접속

* <http://127.0.0.1:9080>

## 6. 오류
퍼미션 문제로 파일을 생성할 수 없다.

* 오류 내용
```text
touch: cannot touch '/var/jenkins_home/copy_reference_file.log': Permission denied
Can not write to /var/jenkins_home/copy_reference_file.log. Wrong volume permissions?
```

* 해결 방법
```bash
[root@localhost /]# chown -R 1000:1000 /var/jenkins_home/
```

## [출처 및 참고]
* <https://www.jenkins.io/doc/book/installing/docker/>
* <https://github.com/jenkinsci/docker/issues/493>
