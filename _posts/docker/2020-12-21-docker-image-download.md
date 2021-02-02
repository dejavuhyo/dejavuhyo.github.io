---
title: Docker Image 다운로드
author: dejavuhyo
date: 2020-12-21 11:10:00 +0900
categories: [Tools, Docker]
tags: [docker-image, docker-image-download, docker, 도커-이미지, 도커-이미지-다운로드, 도커]
---

## 1. Docker Hub
도커(Docker)에서 사용하는 이미지(Image)들은 레파지토리(Repository)를 통해 내려받거나 공유 할 수 있다. 일반적으로 사용하는 어플리케이션의 공식 이미지들은 Docker의 공식 Repository인 [Docker Hub](https://hub.docker.com/)에서 다운이 가능하다.

![img001](/assets/img/2020-12-21-docker-image-download/img001.png)

## 2. 이미지 검색

### 1) Docker Search Help
```shell
[root@localhost ~]# docker search --help

Usage:  docker search [OPTIONS] TERM

Search the Docker Hub for images

Options:
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print search using a Go template
      --limit int       Max number of search results (default 25)
      --no-trunc        Don't truncate output
```

### 2) OPTIONS 없이 검색
아무런 옵션 없이 검색했을 때 대부분 인기도(Stars)가 가장 높은 공식 이미지(Official Image)가 상위에 정렬된다.

```shell
[root@localhost ~]# docker search postgres
NAME                                    DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
postgres                                The PostgreSQL object-relational database sy…   8702      [OK]       
sameersbn/postgresql                                                                    156                  [OK]
paintedfox/postgresql                   A docker image for running Postgresql.          77                   [OK]
bitnami/postgresql                      Bitnami PostgreSQL Docker Image                 74                   [OK]
centos/postgresql-96-centos7            PostgreSQL is an advanced Object-Relational …   45                   
postgrest/postgrest                     REST API for any Postgres database              38                   
arm32v7/postgres                        The PostgreSQL object-relational database sy…   27                   
wrouesnel/postgres_exporter             Postgres metrics exporter for Prometheus.       24                   
circleci/postgres                       The PostgreSQL object-relational database sy…   23                   
centos/postgresql-10-centos7            PostgreSQL is an advanced Object-Relational …   18                   
centos/postgresql-94-centos7            PostgreSQL is an advanced Object-Relational …   16                   
schickling/postgres-backup-s3           Backup PostgresSQL to S3 (supports periodic …   16                   [OK]
debezium/postgres                       PostgreSQL for use with Debezium change data…   15                   [OK]
prodrigestivill/postgres-backup-local   Backup PostgresSQL to local filesystem with …   13                   [OK]
postdock/postgres                       PostgreSQL server image, can work in master …   13                   [OK]
clkao/postgres-plv8                     Docker image for running PLV8 1.4 on Postgre…   12                   [OK]
camptocamp/postgres                     Docker image for PostgreSQL including some e…   7                    [OK]
centos/postgresql-95-centos7            PostgreSQL is an advanced Object-Relational …   6                    
jgiannuzzi/postgres-bdr                 Docker image for PostgreSQL with BDR support    5                    [OK]
blacklabelops/postgres                  Postgres Image for Atlassian Applications       4                    [OK]
dcm4che/postgres-dcm4chee               PostgreSQL for dcm4che-arc 5.x                  4                    [OK]
ansibleplaybookbundle/postgresql-apb    An APB which deploys RHSCL PostgreSQL           2                    [OK]
fredboat/postgres                       PostgreSQL 10.0 used in FredBoat's docker-co…   1                    
manageiq/postgresql                     Container with PostgreSQL and built on CentO…   0                    [OK]
cfcommunity/postgresql                  https://github.com/cloudfoundry-community/po…   0                    
```

## 3. 이미지 다운로드

### 1) Docker Pull Help

```shell
[root@localhost ~]# docker pull --help

Usage:  docker pull [OPTIONS] NAME[:TAG|@DIGEST]

Pull an image or a repository from a registry

Options:
  -a, --all-tags                Download all tagged images in the repository
      --disable-content-trust   Skip image verification (default true)
      --platform string         Set platform if server is multi-platform capable
  -q, --quiet                   Suppress verbose output
```

### 2) 최근 버전 다운로드
태그 옵션을 통해 원하는 버전을 명시하여 받을 수 있으며 생략할 경우 Default tag를 사용하여 가장 최근 버전 내려받는다.

* 옵션 생략

```shell
[root@localhost ~]# docker pull postgres
Using default tag: latest
latest: Pulling from library/postgres
6ec7b7d162b2: Pull complete 
a7d6065bfd90: Pull complete 
1b99004acb42: Pull complete 
e11ab41f0489: Pull complete 
eaa59f6a7cf0: Pull complete 
e27743da9368: Pull complete 
10ffdd8ade29: Pull complete 
bdaa066489bc: Pull complete 
18fcf6a7b457: Pull complete 
7e48453e674b: Pull complete 
d6a3ea95116d: Pull complete 
0cd33e8b20bb: Pull complete 
75f979730af2: Pull complete 
17a2798f3b3f: Pull complete 
Digest: sha256:c846f37f65fd0d3d8b43040df5ebdc5856a038415b019ba596864848fb717a8b
Status: Downloaded newer image for postgres:latest
docker.io/library/postgres:latest
```

* 특정 버전 받기

```shell
[root@localhost ~]# docker pull postgres:12.5
12.5: Pulling from library/postgres
6ec7b7d162b2: Already exists 
a7d6065bfd90: Already exists 
1b99004acb42: Already exists 
e11ab41f0489: Already exists 
eaa59f6a7cf0: Already exists 
e27743da9368: Already exists 
10ffdd8ade29: Already exists 
bdaa066489bc: Already exists 
889684a529fa: Pull complete 
a89eb4df2d61: Pull complete 
053ae58e1489: Pull complete 
d2c54ffd195a: Pull complete 
9af5769854f9: Pull complete 
e4d297047587: Pull complete 
Digest: sha256:87eb6772b300b260f080eceb7658c39a37a6bd1e302f459305d2d4b5df3523b0
Status: Downloaded newer image for postgres:12.5
docker.io/library/postgres:12.5
```

## 4. 이미지 목록

* 다운받은 이미지 목록

```shell
[root@localhost ~]# docker images
REPOSITORY          TAG       IMAGE ID       CREATED      SIZE
eclipse-mosquitto   latest    131df074d5c8   3 days ago   9.54MB
postgres            12.5      044aa8666500   9 days ago   314MB
postgres            latest    a6cd86e1dfce   9 days ago   314MB
```

## [출처 및 참고]
* <https://ifuwanna.tistory.com/249>
