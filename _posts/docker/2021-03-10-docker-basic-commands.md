---
title: Docker 기본 명령
author: dejavuhyo
date: 2021-03-10 06:00:00 +0900
categories: [DevOps, Docker]
tags: [docker-basic-commands, docker-commands, 도커-기본-명령, 도커-명령]
---

## 1. 기본 명령
Docker CLI의 기본 명령

![docker-commands-diagram](/assets/img/2021-03-10-docker-basic-commands/docker-commands-diagram.png)

| 명령 | 설명 |
|:-----:|:-----:|
| [docker attach](https://docs.docker.com/engine/reference/commandline/attach/) | 실행 중인 컨테이너에 로컬 표준 입력, 출력 및 오류 스트림 연결 |
| [docker build](https://docs.docker.com/engine/reference/commandline/build/) | 도커 파일에서 이미지 빌드 |
| [docker builder](https://docs.docker.com/engine/reference/commandline/builder/) | 빌드 관리 |
| [docker checkpoint](https://docs.docker.com/engine/reference/commandline/checkpoint/) | 체크포인트 관리 |
| [docker commit](https://docs.docker.com/engine/reference/commandline/commit/) | 컨테이너의 변경 사항에서 새 이미지 생성 |
| [docker config](https://docs.docker.com/engine/reference/commandline/config/) | 도커 구성 관리 |
| [docker container](https://docs.docker.com/engine/reference/commandline/container/) | 컨테이너 관리 |
| [docker context](https://docs.docker.com/engine/reference/commandline/context/) | 컨텍스트 관리 |
| [docker cp](https://docs.docker.com/engine/reference/commandline/cp/) | 컨테이너와 로컬 파일 시스템 간에 파일/폴더 복사 |
| [docker create](https://docs.docker.com/engine/reference/commandline/create/) | 새 컨테이너 생성 |
| [docker diff](https://docs.docker.com/engine/reference/commandline/diff/) | 컨테이너 파일 시스템의 파일 또는 디렉터리 변경 내용 검사 |
| [docker events](https://docs.docker.com/engine/reference/commandline/events/) | 서버에서 실시간 이벤트 가져오기 |
| [docker exec](https://docs.docker.com/engine/reference/commandline/exec/) | 실행 중인 컨테이너에서 명령 실행 |
| [docker export](https://docs.docker.com/engine/reference/commandline/export/) | 컨테이너의 파일 시스템을 tar 아카이브로 내보내기 |
| [docker history](https://docs.docker.com/engine/reference/commandline/history/) | 이미지 기록 표시 |
| [docker image](https://docs.docker.com/engine/reference/commandline/image/) | 이미지 관리 |
| [docker images](https://docs.docker.com/engine/reference/commandline/images/) | 이미지 나열 |
| [docker import](https://docs.docker.com/engine/reference/commandline/import/) | tarball에서 콘텐츠를 가져와 파일 시스템 이미지 생성 |
| [docker info](https://docs.docker.com/engine/reference/commandline/info/) | 시스템 전체 정보 표시 |
| [docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/) | 도커 개체에 대한 하위 수준의 정보 반환 |
| [docker kill](https://docs.docker.com/engine/reference/commandline/kill/) | 하나 이상의 실행중인 컨테이너 kill |
| [docker load](https://docs.docker.com/engine/reference/commandline/load/) | tar 아카이브 또는 STDIN에서 이미지 로드 |
| [docker login](https://docs.docker.com/engine/reference/commandline/login/) | 도커 레지스트리에 로그인 |
| [docker logout](https://docs.docker.com/engine/reference/commandline/logout/) | 도커 레지스트리에서 로그아웃 |
| [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) | 컨테이너의 로그 가져오기 |
| [docker manifest](https://docs.docker.com/engine/reference/commandline/manifest/) | 도커 이미지 매니페스트 및 매니페스트 목록 관리 |
| [docker network](https://docs.docker.com/engine/reference/commandline/network/) | 네트워크 관리 |
| [docker node](https://docs.docker.com/engine/reference/commandline/node/) | 스왑 노드 관리 |
| [docker pause](https://docs.docker.com/engine/reference/commandline/pause/) | 하나 이상의 컨테이너 내의 모든 프로세스 일시 중지 |
| [docker plugin](https://docs.docker.com/engine/reference/commandline/plugin/) | 플러그인 관리 |
| [docker port](https://docs.docker.com/engine/reference/commandline/port/) | 컨테이너에 대한 포트 매핑 또는 특정 매핑 나열 |
| [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) | 컨테이너 나열 |
| [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) | 레지스트리에서 이미지 또는 리포지토리 꺼내기 |
| [docker push](https://docs.docker.com/engine/reference/commandline/push/) | 이미지 또는 리포지토리를 레지스트리에 푸시 |
| [docker rename](https://docs.docker.com/engine/reference/commandline/rename/) | 컨테이너 이름 변경 |
| [docker restart](https://docs.docker.com/engine/reference/commandline/restart/) | 하나 이상의 컨테이너 재시작 |
| [docker rm](https://docs.docker.com/engine/reference/commandline/rm/) | 하나 이상의 컨테이너 제거 |
| [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) | 하나 이상의 이미지 제거 |
| [docker run](https://docs.docker.com/engine/reference/commandline/run/) | 새 컨테이너에서 명령 실행 |
| [docker save](https://docs.docker.com/engine/reference/commandline/save/) | 하나 이상의 이미지를 tar 아카이브에 저장(기본적으로 STDOUT으로 스트리밍됨) |
| [docker search](https://docs.docker.com/engine/reference/commandline/search/) | 도커 허브에서 이미지 검색 |
| [docker secret](https://docs.docker.com/engine/reference/commandline/secret/) | 도커 비밀 관리 |
| [docker service](https://docs.docker.com/engine/reference/commandline/service/) | 서비스 관리 |
| [docker stack](https://docs.docker.com/engine/reference/commandline/stack/) | 도커 스택 관리 |
| [docker start](https://docs.docker.com/engine/reference/commandline/start/) | 중지된 하나 이상의 컨테이너 시작 |
| [docker stats](https://docs.docker.com/engine/reference/commandline/stats/) | 컨테이너 리소스 사용 통계의 라이브 스트림 표시 |
| [docker stop](https://docs.docker.com/engine/reference/commandline/stop/) | 하나 이상의 실행 중인 컨테이너 중지 |
| [docker swarm](https://docs.docker.com/engine/reference/commandline/swarm/) | Swarm 관리 |
| [docker system](https://docs.docker.com/engine/reference/commandline/system/) | 도커 관리 |
| [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) | SOURCE_IMAGE를 참조하는 TARGET_IMAGE 태그를 생성 |
| [docker top](https://docs.docker.com/engine/reference/commandline/top/) | 컨테이너의 실행 프로세스 표시 |
| [docker trust](https://docs.docker.com/engine/reference/commandline/trust/) | Docker 이미지에 대한 신뢰 관리 |
| [docker unpause](https://docs.docker.com/engine/reference/commandline/unpause/) | 하나 이상의 컨테이너 내의 모든 프로세스 일시 중지 해제 |
| [docker update](https://docs.docker.com/engine/reference/commandline/update/) | 하나 이상의 컨테이너 구성 업데이트 |
| [docker version](https://docs.docker.com/engine/reference/commandline/version/) | 도커 버전 정보 표시 |
| [docker volume](https://docs.docker.com/engine/reference/commandline/volume/) | 볼륨 관리 |
| [docker wait](https://docs.docker.com/engine/reference/commandline/wait/) | 하나 이상의 컨테이너가 멈출 때까지 차단한 다음 종료 코드를 인쇄 |

## [출처 및 참고]
* [https://docs.docker.com/engine/reference/commandline/docker/](https://docs.docker.com/engine/reference/commandline/docker/)
* [https://yuehhua.github.io/categories/Docker/](https://yuehhua.github.io/categories/Docker/)
