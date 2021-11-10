---
title: Docker에서 메모리 및 CPU 제한 설정
author: dejavuhyo
date: 2021-11-10 06:00:00 +0900
categories: [DevOps, Docker]
tags: [docker-momory, docker-cpu, docker-memory-cpu, docker-setting, memory-cpu-limit, memory-cpu-setting, 도커-메모리, 도커-cpu, 도커-메모리-cpu, 도커-설정, 도커-제한-설정]
---

## 1. docker run으로 리소스 제한 설정
docker run 명령을 사용하여 리소스 제한을 직접 설정할 수 있다. 간단한 솔루션이다. 그러나 이 제한은 이미지의 특정 실행에만 적용된다.

### 1) 메모리
예를 들어 컨테이너가 사용할 수 있는 메모리를 512MB로 제한해 보겠다. 메모리를 제한하려면 m 매개변수를 사용해야 한다.

```bash
$ docker run -m 512m nginx
```

예약이라는 소프트 한도를 설정할 수도 있다. docker가 호스트 시스템에서 메모리 부족을 감지하면 활성화된다.

```bash
$ docker run -m 512m --memory-reservation=256m nginx
```

### 2) CPU
기본적으로 호스트 시스템의 컴퓨팅 성능에 대한 액세스는 무제한이다. cpus 매개변수를 사용하여 CPU 제한을 설정할 수 있다. 예를 들어 컨테이너가 최대 2개의 CPU를 사용하도록 제한해 보겠다.

```bash
$ docker run --cpus=2 nginx
```

CPU 할당의 우선순위를 지정할 수도 있다. 기본값은 1024이며 숫자가 높을수록 우선순위가 높다.

```bash
$ docker run --cpus=2 --cpu-shares=2000 nginx
```

메모리 예약과 유사하게 CPU 공유는 컴퓨팅 성능이 부족하고 경쟁 프로세스 간에 분할되어야 할 때 주요 역할을 한다.

## 2. docker-compose 파일로 메모리 제한 설정
docker-compose 파일을 사용하여 비슷한 결과를 얻을 수 있다. 형식과 가능성은 docker-compose 버전에 따라 다르다.

### 1) 버전 3 이상, docker swarm 포함
Nginx 서비스에 CPU의 절반과 512MB의 메모리를 제한하고 CPU의 1/4과 128MB의 메모리를 예약하도록 한다. 서비스 구성에서 "배포" 및 "소스" 세그먼트를 생성해야 한다.

```yaml
services:
  service:
    image: nginx
    deploy:
        resources:
            limits:
              cpus: 0.50
              memory: 512M
            reservations:
              cpus: 0.25
              memory: 128M
```

docker-compose 파일의 배포 세그먼트를 활용하려면 docker stack 명령을 사용해야 한다. 스택을 스웜에 배포하기 위해 배포 명령을 실행한다.

```bash
$ docker stack deploy --compose-file docker-compose.yml bael_stack
```

### 2) 버전 2 docker-compose 포함
이전 버전의 docker-compose에서는 서비스의 기본 속성과 동일한 수준으로 리소스 제한을 설정할 수 있다. 또한 이름이 약간 다르다.

```yaml
service:
  image: nginx
  mem_limit: 512m
  mem_reservation: 128M
  cpus: 0.5
  ports:
    - "80:80"
```

구성된 컨테이너를 생성하려면 docker-compose 명령을 실행해야 한다.

```bash
$ docker-compose up
```

## 3. 리소스 사용 확인
제한을 설정한 후 docker stats 명령을 사용하여 제한을 확인할 수 있다.

```bash
$ docker stats
CONTAINER ID        NAME                                             CPU %               MEM USAGE / LIMIT   MEM %               NET I/O             BLOCK I/O           PIDS
8ad2f2c17078        bael_stack_service.1.jz2ks49finy61kiq1r12da73k   0.00%               2.578MiB / 512MiB   0.50%               936B / 0B           0B / 0B             2
```

## [출처 및 참고]
* <https://www.baeldung.com/ops/docker-memory-limit>
