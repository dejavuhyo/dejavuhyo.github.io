---
title: GitLab Webhooks로 Jenkins 빌드 유발
author: dejavuhyo
date: 2024-08-09 08:58:00 +0900
categories: [DevOps, CI/CD]
tags: [jenkins-gitlab, jenkins-build, jenkins-ci, gitlab-webhooks, jenkins-build-trigger, jenkins-빌드-유발]
---

## 1. 환경

* CentOS Stream release 9

* OpenJDK 17.0.6

* Jenkins 2.452.3

* GitLab 17.1

* Docker version 27.0.3

* Docker Compose version v2.28.1

* Spring Boot 3.3.2

* Apache Maven 3.9.8

## 2. Jenkins 플러그인 설치

* Install suggested plugins (추천 플러그인)

* GitLab

* Pipeline

* Pipeline: Stage View

* Docker

* Docker Pipeline

* Maven Integration

* Publish Over SSH

![install-plugins](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/install-plugins.png)

## 3. Pipeline 생성

![new-item](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/new-item.png)

## 4. Jenkins Build Triggers 설정

### 1) GitLab webhooks URL 확인

![build-triggers](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/build-triggers.png)

### 2) Secret Token 생성

![secret-token](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/secret-token.png)

## 5. GitLab Webhooks 설정

### 1) Webhooks 추가

![new-webhooks](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/new-webhooks.png)

### 2) Jenkins Triggers 정보 입력
`Secret token` 값은 Jenkins에서 생성한 `Secret token` 값을 넣는다.

![webhooks](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/webhooks.png)

![trigger](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/trigger.png)

## 6. GitLab Webhooks 테스트

### 1) Push Events

![push-events](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/push-events.png)

### 2) Push Events 결과 확인

![push-events-status](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/push-events-status.png)

## 7. GitLab Personal Access Token 생성

![gitlab-access-token](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/gitlab-access-token.png)

![add-personal-access-token](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/add-personal-access-token.png)

![personal-access-token](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/personal-access-token.png)

## 8. Jenkins Credential 등록

![credentials](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/credentials.png)

![add-credentials](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/add-credentials.png)

### 1) GitLab API Token
`API Token` 항목은 GitLab에서 생성한 `Personal Access Token`이다.

이 Credentials은 `Jenkins 관리 > System`에서 `GitLab connection`을 위해 사용한다.

![gitlab-token-credentials](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/gitlab-token-credentials.png)

### 2) GitLab - Username with password
`Username` 값은 `GitLab 로그인 아이디`, `Password` 값은 GitLab에서 생성한 `Personal Access Token`이다.

이 Credentials은 `Pipeline script from SCM`에서 GitLab에 접속하기 위해 사용한다.

![gitlab-username-credentials](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/gitlab-username-credentials.png)

### 3) DockerHub - Username with password
`Username` 값은 `DockerHub 로그인 아이디`, `Password` 값은 `DockerHub 로그인 비번`이다.

이 Credentials은 DockerHub에 Docker Image push를 위해 사용한다.

![dockerhub-credentials](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/dockerhub-credentials.png)

## 9. GitLab connections 테스트

![gitlab-connections](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/gitlab-connections.png)

## 10. Tools 설정

![tools](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/tools.png)

### 1) Maven installations 설정

![maven-installations](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/maven-installations.png)

## 11. Docker in Docker 설치
Docker로 설치한 Jenkins 컨테이너 안에 Docker Build를 위해 Docker를 설치한다.

### 1) Docker Container 접속

```shell
$ docker exec -it jenkins /bin/bash
```

### 2) Docker 설치를 위한 Shell 다운로드

```shell
root@12a53e47f651:/# curl -fsSL get.docker.com -o get-docker.sh
```

### 3) Docker install shell script 실행

```shell
root@12a53e47f651:/# sh get-docker.sh
```

### 4) Docker 설치 확인

```shell
root@12a53e47f651:/# docker --version
Docker version 27.1.0, build 6312585
```

## 12. 매개변수 추가
Docker 빌드시 tag로 사용된다.

![parameter](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/parameter.png)

![string-parameter](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/string-parameter.png)

## 13. Pipeline 설정

### 1) Pipeline script from SCM 설정

![pipeline1](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/pipeline1.png)

![pipeline2](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/pipeline2.png)

## 14. Jenkins 파일 생성
Jenkins Pipeline은 프로젝트의 전체 파이프라인을 개발자가 직접 정의한 코드 형태(Pipeline as Code)로 받아 하나의 Job에서 이를 수행할 수 있는 기능을 제공한다.

Pipeline이 정의된 코드는 `Jenkinsfile`에 저장하고, 프로젝트 소스코드가 있는 Repository에 같이 관리함으로써, 프로젝트 단위의 파이프라인 정의가 이루어진다. 또한 파이프라인의 변경 이력이 남기 때문에 추적 및 복구가 용이하다.

GitLab 저장소에 Jenkins 파일을 생성(확장자 없음)하고 Pipeline을 작성한다.

프로젝트 환경에 맞는 stages를 적용한다.

![jenkinsfile](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/jenkinsfile.png)

```text
pipeline {
    agent any
    tools {
        maven 'M2_HOME'
    }
    environment {
        imageName = 'dejavuhyo/example'
        containerName = 'example'
    }
    stages {
        stage('Maven Build') {
            steps {
                script {
                    sh 'mvn clean package'
                }
            }
        }
        stage('Build Image') {
            steps {
                script {
                    sh 'docker build -t ${imageName}:${BUILD_NUMBER} -f ./Dockerfile .'
                }
            }
        }
        stage('Stop and Remove Container') {
            steps {
                script {
                    sh 'docker stop ${containerName} || true'
                    sh 'docker rm ${containerName} || true'
                }
            }
        }
        stage('Run Image') {
            steps {
                script {
                    sh 'docker run -d -p 8090:8090 --name ${containerName} ${imageName}:${BUILD_NUMBER}'
                }
            }
        }
    }
}
```

## 15. Docker 파일 생성
Dockerfile은 Docker Image를 생성하기 위한 스크립트(설정 파일)이다.

Docker는 `Dockerfile`에 나열된 명령문을 차례대로 수행하며 Docker Image를 생성한다.

GitLab 저장소에 Docker 파일을 생성(확장자 없음)하고 스트립트를 작성한다.

![dockerfile](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/dockerfile.png)

```text
FROM openjdk:17-jdk-alpine
CMD ["./mvnw", "clean", "package"]
VOLUME /tmp
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

![repository](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/repository.png)

## 16. Jenkins 빌드 및 배포 확인

### 1) GitLab main branch
GitLab Webhooks 설정이 되어 있어 main branch에 소스 변경이 일어나면, Jenkins Build Trigger 된다.

![gitlab-webhooks](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/gitlab-webhooks.png)

### 2) 파라미터와 함께 빌드
매개변수를 입력하여 GitLab Webhooks 없이 빌드한다. 빌드 시점의 GitLab 레파지토리 소스를 받아 빌드한다.

Jenkins에서 수동으로 빌드시 Jenkins Build Triggers는 설정하지 않아도 된다.

![parameter-build-number](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/parameter-build-number.png)

![parameter-build](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/parameter-build.png)

### 3) 배포 확인

![release](/assets/img/2024-08-09-gitlab-webhooks-jenkins-build-trigger-centos-9/release.png)

## [출처 및 참고]
* [https://bread-diary.tistory.com/23](https://bread-diary.tistory.com/23)
* [https://velog.io/@imsooyeon/Jenkins-pipeline을-구축하여-Docker-build-및-이미지-push-하기](https://velog.io/@imsooyeon/Jenkins-pipeline을-구축하여-Docker-build-및-이미지-push-하기)
* [https://www.linkedin.com/pulse/jenkins-cicd-pipeline-building-pushing-images-docker-hub-poloju-9gikc](https://www.linkedin.com/pulse/jenkins-cicd-pipeline-building-pushing-images-docker-hub-poloju-9gikc)
* [https://gajananb.hashnode.dev/end-to-end-ultimate-cicd-pipeline-for-spring-boot-java-application](https://gajananb.hashnode.dev/end-to-end-ultimate-cicd-pipeline-for-spring-boot-java-application)
* [https://cwal.tistory.com/24](https://cwal.tistory.com/24)
* [https://wooono.tistory.com/123](https://wooono.tistory.com/123)
