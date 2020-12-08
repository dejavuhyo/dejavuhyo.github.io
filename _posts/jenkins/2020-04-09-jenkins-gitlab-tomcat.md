---
title: Jenkins와 GitLab 연동 및 Tomcat 배포
author: Hyosik
date: 2020-04-09 18:00:00 +0900
categories: [Tools, Jenkins]
tags: [jenkins-gitlab-tomcat, jenkins-gitlab, jenkins-tomcat, jenkins-tomcat-deploy, jenkins-deploy, 젠킨스-톰켓-배포, 젠킨스-깃랩, 젠킨스-배포]
---

## 1. Jenkins 관리

### 1) Global Tool Configuration

![img001](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img001.png)

![img002](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img002.png)

* Maven Configuration

![img003](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img003.png)

* JDK

![img004](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img004.png)

* Git

![img005](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img005.png)

* Maven

![img006](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img006.png)

### 2) 플러그인 관리

![img007](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img007.png)

* 플러그인 설치

![img008](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img008.png)

![img009](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img009.png)

## 2. Credentials

![img010](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img010.png)

![img011](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img011.png)

### 1) GitLab 계정 ID 및 PW 입력

![img012](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img012.png)

### 2) Tomcat manager 계정 ID 및 PW 입력

![img013](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img013.png)

### 3) Global credentials 설정 확인

![img014](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img014.png)

## 3. 새로운 Item 생성 및 설정

![img015](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img015.png)

![img016](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img016.png)

### 1) 소스 코드 관리

* GitLab Project 주소 복사

![img017](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img017.png)

* 설정

![img018](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img018.png)

### 2) Build

* Invoke top-level Maven targets 선택

![img019](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img019.png)

* Maven Version: M2_HOME

* Goals: clean package

![img020](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img020.png)

### 3) 빌드 후 조치

* Deploy war/ear to a container 선택

![img021](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img021.png)

![img022](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img022.png)

## 4. 빌드

![img023](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img023.png)

* 콘솔 출력

![img024](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img024.png)

## 5. 배포 확인

* http://서버주소:8080/

![img025](/assets/img/2020-04-09-jenkins-gitlab-tomcat/img025.png)

## 출처 및 참고
* <https://www.hanumoka.net/2019/10/23/jenkins-20191023-jenkins-connect-gitlab/>
* <https://wikidocs.net/16281>
