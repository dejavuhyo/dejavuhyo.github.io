---
title: GitLab Webhooks로 Jenkins 빌드 유발 설정
author: dejavuhyo
date: 2020-04-10 14:30:00 +0900
categories: [DevOps, CI/CD]
tags: [jenkins-build-trigger, jenkins-build, gitlab-webhooks, webhooks, 젠킨스-빌드-유발, 젠킨스-빌드, 깃랩-웹-후크]
---

Jenkins 빌드 유발은 두 가지 방법이 있다. 첫 번째는 GitLab의 User Setting에서 Access Token을 생성해 Jenkins에 등록하여 인증하는 방법, 두 번째는 Jenkins의 Item에서 Secret token을 생성해 GitLab 프로젝트 Integrations의 Webhooks에 등록하여 인증하는 방법이다. 내가 설정한 방법은 두 번째 방법으로 빌드 유발을 설정했다.

## 1. Jenkins 플러그인 설치

![img001](/assets/img/2020-04-10-gitlab-webhooks-jinkins-build-trigger/img001.png)

## 2. Item 구성 이동

![img002](/assets/img/2020-04-10-gitlab-webhooks-jinkins-build-trigger/img002.png)

![img003](/assets/img/2020-04-10-gitlab-webhooks-jinkins-build-trigger/img003.png)

* 빌드 유발 설정

![img004](/assets/img/2020-04-10-gitlab-webhooks-jinkins-build-trigger/img004.png)

* Secret token 생성

![img005](/assets/img/2020-04-10-gitlab-webhooks-jinkins-build-trigger/img005.png)

## 3. GitLab Integrations 이동

![img006](/assets/img/2020-04-10-gitlab-webhooks-jinkins-build-trigger/img006.png)

* Jenkins 빌드 유발의 URL 및 Secret token 정보 입력

![img007](/assets/img/2020-04-10-gitlab-webhooks-jinkins-build-trigger/img007.png)

* Hooks 테스트

![img008](/assets/img/2020-04-10-gitlab-webhooks-jinkins-build-trigger/img008.png)

![img009](/assets/img/2020-04-10-gitlab-webhooks-jinkins-build-trigger/img009.png)

## [출처 및 참고]
* [https://lemontia.tistory.com/882](https://lemontia.tistory.com/882)
