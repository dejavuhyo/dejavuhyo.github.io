---
title: Ubuntu에 Node.js 설치
author: dejavuhyo
date: 2025-05-29 11:00:00 +0900
categories: [Language, JavaScript]
tags: [nodejs, node, nodejs-install, javascript, 노드js, 노드, 자바스크립트]
---

## 1. Ubuntu에 Node.js LTS(v22.x) 설치
시작하기 전에 시스템에 `curl`이 설치되어 있는지 확인한다. `curl` 설치되어 있지 않으면 다음 명령을 사용하여 설치할 수 있다.

```shell
sudo apt-get install -y curl
```

## 2. 설치 스크립트 다운로드

```shell
curl -fsSL https://deb.nodesource.com/setup_lts.x -o nodesource_setup.sh
```

## 3. sudo로 설치 스크립트 실행

```shell
sudo -E bash nodesource_setup.sh
```

## 4. N|Solid 또는 Node.js 설치

### 1) N|Solid

* `N|Solid` 설치

```shell
sudo apt-get install -y nsolid
```

* 설치 확인

```shell
nsolid -v
```

### 2) Node.js

* Node.js 설치

```shell
sudo apt-get install -y nodejs
```

* 설치 확인

```shell
node -v
```

## [출처 및 참고]
* [https://github.com/nodesource/distributions?tab=readme-ov-file#debian-and-ubuntu-based-distributions](https://github.com/nodesource/distributions?tab=readme-ov-file#debian-and-ubuntu-based-distributions)
