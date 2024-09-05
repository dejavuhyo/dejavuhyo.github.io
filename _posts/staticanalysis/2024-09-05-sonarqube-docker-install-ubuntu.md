---
title: Ubuntu에 Docker 이미지로 SonarQube 설치
author: dejavuhyo
date: 2024-09-05 09:07:00 +0900
categories: [DevOps, StaticAnalysis]
tags: [ubuntu-sonarqube, sonarqube-docker, sonarqube-install, 우분투-소나큐브, 소나큐브-도커, 소나큐브-설치]
---

## 1. SonarQube 도커 이미지

![sonarqube](/assets/img/2024-09-05-sonarqube-docker-install-ubuntu/sonarqube.jpg)

SonarQube 도커 이미지는 amd64 아키텍처와 arm64 기반의 Apple Silicon에서 실행하는 것을 모두 지원한다.

[Docker Engine](https://docs.docker.com/engine/) 버전 20.10 이상을 사용하는 것이 좋다.

## 2. 데이터를 유지 볼륨 생성
다음 볼륨을 생성하면 새 버전으로 업데이트하거나 상위 에디션으로 업그레이드할 때 정보 손실을 방지하는데 도움이 된다.

* sonarqube_data: Elasticsearch 인덱스와 같은 데이터 파일을 포함한다.

* sonarqube_logs: 액세스, 웹 프로세스, CE 프로세스 및 Elasticsearch에 대한 SonarQube 로그를 포함한다.

* sonarqube_extensions: 필요한 경우 설치된 모든 플러그인과 Oracle JDBC 드라이버가 포함된다.

다음 명령으로 볼륨을 생성한다.

```shell
$ docker volume create --name sonarqube_data
$ docker volume create --name sonarqube_logs
$ docker volume create --name sonarqube_extensions
```

> 위의 명령으로 표시된 대로 [volumes](https://docs.docker.com/storage/volumes/)을 사용하고 [bind mounts](https://docs.docker.com/storage/bind-mounts/)를 사용하지 않도록 한다. bind mounts를 사용하면 플러그인이 올바르게 채워지지 않는다.

## 3. JDBC 드라이버를 추가
지원되는 데이터베이스(Oracle 제외)의 드라이버는 이미 제공된다. Oracle 데이터베이스를 사용하는 경우 볼륨에 JDBC 드라이버를 `sonar_extensions`에 추가해야 한다. 이렇게 하려면 다음을 수행한다.

① 내장된 H2 데이터베이스로 SonarQube 컨테이너를 시작한다.

② SonarQube가 제대로 시작되면 종료한다.

③ Oracle JDBC 드라이버를 `sonarqube_extensions/jdbc-driver/oracle`에 복사한다.

## 4. SonarQube 컨테이너 시작
SonarQube 컨테이너를 시작한다.

* 명령줄(docker run)

* 구성 파일(docker compose)

도커 기반 설정의 경우 환경 변수는 속성과 함께 제공된 모든 매개변수를 대체한다. 자세한 내용은 [Docker 환경 변수](https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/configure-and-operate-a-server/environment-variables/)를 참조한다.

Docker 볼륨 내에서 SonarQube 플러그인을 설치하고 업데이트하는 방법에 대한 자세한 내용은 [플러그인 설치](https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-a-plugin/) 페이지에서 확인할 수 있다.

### 1) docker run을 사용하여 컨테이너 시작
`-e` 환경 변수 플래그를 사용하여 정의된 데이터베이스 속성으로 이미지를 실행한다.

```shell
$ sudo docker run -d --name sonarqube \
    -p 9000:9000 \
    -e SONAR_JDBC_URL=... \
    -e SONAR_JDBC_USERNAME=... \
    -e SONAR_JDBC_PASSWORD=... \
    -v sonarqube_data:/opt/sonarqube/data \
    -v sonarqube_extensions:/opt/sonarqube/extensions \
    -v sonarqube_logs:/opt/sonarqube/logs \
    <image_name>
```

### 2) Docker Compose를 사용하여 컨테이너 시작

> 이미지를 실행할 때 데이터베이스를 삭제하고 새로 시작하려는 경우가 아니라면 `-v`를 사용하여 `docker-compose down`하지 않도록 주의하고 `docker system prune` 또는 `docker volume prune`과 같은 명령을 실행할 때 주의한다. `external: true` 매개 변수를 사용하더라도 데이터베이스 볼륨은 소나큐브의 초기 시작 및 종료 이후에는 유지되지 않는다.

[Docker Compose](https://docs.docker.com/compose/)를 사용하는 경우 `.yml` 파일을 구성할 때 다음 예를 참조로 사용한다.

> 아래 예제는 최신 버전의 SonarQube Docker 이미지를 사용한다. SonarQube의 LTA(Long-Term Active) 버전을 사용하려면 `sonarqube:lts-community` 이미지 태그로 예제를 업데이트해야 한다.

* docker-compose.yml 예제

```yml
version: "3"

services:
  sonarqube:
    image: sonarqube:lts-community
    container_name: sonarqube
    restart: always
    depends_on:
      - postgres
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://postgres:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
    ports:
      - '9000:9000'
  postgres:
    image: postgres:latest
    container_name: postgres
    restart: always
    ports:
      - '5432:5432'
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
    volumes:
      - postgres:/var/lib/postgresql
      - postgres_data:/var/lib/postgresql/data
```

## [출처 및 참고]
* [https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-the-server/installing-sonarqube-from-docker/](https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-the-server/installing-sonarqube-from-docker/)
