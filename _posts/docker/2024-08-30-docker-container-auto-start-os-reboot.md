---
title: 운영체제 재부팅시 Docker 컨테이너 자동 시작
author: dejavuhyo
date: 2024-08-30 09:11:00 +0900
categories: [DevOps, Docker]
tags: [docker-auto-start, container-auto-start, docker-update, 도커-자동-시작, 컨테이너-자동-시작]
---

## 1. 컨테이너 자동 시작
Docker는 컨테이너가 종료될 때 또는 Docker가 재시작될 때 컨테이너가 자동으로 시작되는지 여부를 제어하는 [​​재시작 정책](https://docs.docker.com/engine/reference/run/#restart-policies---restart)을 제공한다. 재시작 정책은 올바른 순서로 연결된 컨테이너를 시작한다. Docker는 재시작 정책을 사용하고 프로세스 관리자를 사용하여 컨테이너를 시작하는 것을 피하는 것이 좋다.

재시작 정책은 `dockerd` 명령의 `--live-restore` 플래그와 다르다. `--live-restore`를 사용하면 네트워킹 및 사용자 입력이 중단되더라도 도커 업그레이드 중에 컨테이너를 계속 실행할 수 있다.

## 2. 재시작 정책 사용
컨테이너의 재시작 정책을 설정하려면 `docker run` 명령을 사용할 때 `--restart` 플래그를 사용한다. `--restart` 플래그의 값은 다음 중 하나일 수 있다.

| Flag | Description |
|:-----:|:-----:|
| `no` | 컨테이너를 자동으로 다시 시작하지 않는다. (기본값) |
| `on-failure[:max-retries]` | 오류로 인해 컨테이너가 종료되면 다시 시작하고, 이는 0이 아닌 종료 코드로 나타난다. 선택적으로 Docker 데몬이 `:max-retries` 옵션을 사용하여 컨테이너를 다시 시작하려고 시도하는 횟수를 제한한다. `on-failure` 정책은 컨테이너가 실패로 종료된 경우에만 다시 시작하라는 메시지를 표시한다. 데몬이 다시 시작하면 컨테이너를 다시 시작하지 않는다. |
| `always` | 컨테이너가 중지되면 항상 재시작한다. 수동으로 중지된 경우 Docker 데몬이 재시작되거나 컨테이너 자체가 수동으로 재시작될 때만 재시작된다. |
| `unless-stopped` | `always`와 비슷하지만, 컨테이너가 중지되면(수동 또는 기타 방법으로) Docker 데몬이 다시 시작된 후에도 다시 시작되지 않는다. |

다음 명령은 Redis 컨테이너를 시작하고, 컨테이너가 명시적으로 중지되거나 데몬이 다시 시작되지 않는 한 항상 다시 시작되도록 구성한다.

```shell
$ docker run -d --restart unless-stopped redis
```

다음 명령은 이미 실행 중인 컨테이너인 `redis`에 대한 재시작 정책을 변경한다.

```shell
$ docker update --restart unless-stopped redis
```

다음 명령은 실행 중인 모든 컨테이너를 다시 시작한다.

```shell
$ docker update --restart unless-stopped $(docker ps -q)
```

## 3. 재시작 정책 세부 정보
재시작 정책을 사용할 때 다음 사항에 유의한다.

* 재시작 정책은 컨테이너가 성공적으로 시작된 후에만 적용된다. 이 경우 성공적으로 시작한다는 것은 컨테이너가 최소 10초 동안 작동 중이고 Docker가 모니터링을 시작했다는 것을 의미한다. 이렇게 하면 전혀 시작되지 않는 컨테이너가 재시작 루프에 들어가는 것을 방지할 수 있다.

* 컨테이너를 수동으로 중지하면 Docker 데몬이 다시 시작되거나 컨테이너가 수동으로 다시 시작될 때까지 재시작 정책이 무시된다. 이렇게 하면 재시작 루프가 방지된다.

* 재시작 정책은 컨테이너에만 적용된다. Swarm 서비스에 대한 재시작 정책을 구성하려면 [service restart와 관련된 플래그](https://docs.docker.com/reference/cli/docker/service/create/)를 참조한다.

## [출처 및 참고]
* [https://docs.docker.com/config/containers/start-containers-automatically/](https://docs.docker.com/config/containers/start-containers-automatically/)
