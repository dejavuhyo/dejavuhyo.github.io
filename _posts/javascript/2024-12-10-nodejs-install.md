---
title: Node.js Windows 설치
author: dejavuhyo
date: 2024-12-10 10:25:00 +0900
categories: [Language, JavaScript]
tags: [nodejs, node, nodejs-install, javascript, 노드js, 노드, 자바스크립트]
---

## 1. Node.js
`Node.js`는 V8으로 빌드된 이벤트 기반 자바스크립트 런타임이다. 웹 서버와 같이 확장성 있는 네트워크 프로그램 제작을 위해 고안되었다.

파이썬으로 만든 트위스티드, 펄로 만든 펄 객체 환경, 루비로 만든 이벤트 머신과 그 용도가 비슷하다.

대부분의 자바스크립트가 웹 브라우저에서 실행되는 것과는 달리, 서버 측에서 실행된다.

일부 CommonJS 명세를 구현하고 있으며, 쌍방향 테스트를 위해 REPL 환경을 포함하고 있다.

## 2. Windows 설치

### 1) Node.js 다운로드
Node.js 사이트에서 설치 파일을 [다운로드](https://nodejs.org/en/download/package-manager) 한다.

![nodejs-download](/assets/img/2024-12-10-nodejs-install/nodejs-download.png)

### 2) 설치 실행

![setup](/assets/img/2024-12-10-nodejs-install/setup.png)

### 3) 라이선스 동의

![license-agreement](/assets/img/2024-12-10-nodejs-install/license-agreement.png)

### 4) 설치 위치

![filder](/assets/img/2024-12-10-nodejs-install/filder.png)

### 5) 사용자 설정

* Node.js runtime: Node.js 런타임

* corepack manager: 패키지 관리자로 npm, yarn 등 패키지 관리자의 여러 버전을 설치하고, 시용여부를 지정, 서로 다른 개발 또는 배포 환경에서 상호간의 설치 호환성을 유지하기 위해서 사용되어질 수 있음

* npm package manager: 패키지 관리

* Online documentation shortcuts: 온라인 문서에 대한 바로가기

* Add to PATH: PATH 환경변수 등록

![custom-setup](/assets/img/2024-12-10-nodejs-install/custom-setup.png)

### 6) Native 모듈 설치 선택
Native 모듈들을 위한 툴을 설치할지 선택한다. npm 모듈중 컴파일이 필요할 경우, 필요한 도구(Python, Visual Studio Build Toos)를 자동 설치 여부를 선택한다.

![tools](/assets/img/2024-12-10-nodejs-install/tools.png)

### 7) 설치 진행

![install](/assets/img/2024-12-10-nodejs-install/install.png)

### 8) 설치 완료

![completed](/assets/img/2024-12-10-nodejs-install/completed.png)

### 9) 설치 확인

![check](/assets/img/2024-12-10-nodejs-install/check.png)

## [출처 및 참고]
* [https://ko.wikipedia.org/wiki/Node.js](https://ko.wikipedia.org/wiki/Node.js)
* [https://offbyone.tistory.com/441](https://offbyone.tistory.com/441)
