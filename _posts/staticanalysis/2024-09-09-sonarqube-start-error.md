`---
title: SonarQube vm.max_map_count 에러
author: dejavuhyo
date: 2024-09-09 08:33:00 +0900
categories: [DevOps, StaticAnalysis]
tags: [sonarqube-error, sonarqube-vm-error, vm-max_map_count, 소나큐브-에러, 소나큐브-vm-에러]
---

## 1. 에러 로그
`vm.max_map_count`관련 에러가 발생하고 SonarQube가 구동되지 않는다.

```text
ERROR: [1] bootstrap checks failed. You must address the points described in the following [1] lines before starting Elasticsearch.
bootstrap check failure [1] of [1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
ERROR: Elasticsearch did not exit normally - check the logs at /opt/sonarqube/logs/sonarqube.log
```

## 2. 원인
Elasticsearch 구동시에 많이 발생하는 에러이며, SonarQube는 Elasticsearch를 사용하고 있다.

`vm.max_map_count`의 설정값이 애플리케이션에서 필요로 하는 수보다 작아서 발생하는 오류이다.

## 3. 해결방법

* vm.max_map_count 설정 확인

```shell
$ sudo sysctl -a | grep vm.max_map_count
vm.max_map_count = 65530
```

* vm.max_map_count 변경

```shell
$ sudo sysctl -w vm.max_map_count = 262144
vm.max_map_count = 262144

$ sudo sysctl -a | grep vm.max_map_count
vm.max_map_count = 262144
```

## [출처 및 참고]
* [https://happylie.tistory.com/112](https://happylie.tistory.com/112)
