---
title: Ubuntu에 nvm으로 Node.js 설치
author: dejavuhyo
date: 2026-06-22 09:00:00 +0900
categories: [Language, JavaScript]
tags: [nodejs, node, nodejs-install, 노드js, 노드]
---

## 1. 시스템 패키지 업데이트
패키지 리스트를 최신 상태로 업데이트한다.

```bash
sudo apt update && sudo apt upgrade -y
```

## 2. NVM(Node Version Manager) 설치
NVM을 설치하기 위해 curl을 사용해 설치 스크립트를 다운로드하고 실행한다.

### 1) 다운로드

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
```

### 2) 설정 적용

```bash
source ~/.bashrc
```

### 3) NVM 설치 확인
버전 번호가 출력되면 성공이다.

```bash
nvm --version
```

## 3. Node.js 설치
LTS(Long Term Support) 버전을 설치한다.

### 1) 설치

```bash
nvm install --lts
```

### 2) 설치 확인

* Node.js 버전 확인: v24.16.0 출력

```bash
node -v
```

* NVM 버전 확인: v24.16.0 출력

```bash
nvm current
```

* npm 버전 확인: 11.13.0 출력

```bash
npm -v
```

## [출처 및 참고]
* [https://nodejs.org/ko/download](https://nodejs.org/ko/download)
