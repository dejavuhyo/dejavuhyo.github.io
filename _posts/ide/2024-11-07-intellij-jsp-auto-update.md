---
title: IntelliJ JSP 수정시 자동 업데이트
author: dejavuhyo
date: 2024-11-07 09:32:00 +0900
categories: [Application, IDE]
tags: [intellij, intellij-tomcat, intellij-jsp, intellij-jsp-update, 인텔리제이-jsp-업데이트, jsp-업데이트]
---

![intellij-idea-icon](/assets/img/2024-11-07-intellij-jsp-auto-update/intellij-idea-icon.jpg)

## 1. 자동 업데이트 설정
웹 개발시 HTML 및 JSP를 수정하면 자동으로 수정된 내용이 업데이트 되도록 설정하는 방법이다.

### 1) Edit Configurations
위쪽 Edit Configurations을 선택한다.

![edit-configurations](/assets/img/2024-11-07-intellij-jsp-auto-update/edit-configurations.jpg)

### 2) war exploded 확인
Deployment에서 "war exploded"로 설정되어 있는지 확인한다. "war"로 되어있는 경우 'Update classes and resourcesr'가 나타나지 않는다.

![deployment](/assets/img/2024-11-07-intellij-jsp-auto-update/deployment.jpg)

### 3) Server 설정
Server에서 "On Update action"과 "On frame deactivation"을 "Update classes and resources"로 변경한다.

![server](/assets/img/2024-11-07-intellij-jsp-auto-update/server.jpg)

## [출처 및 참고]
* [https://velog.io/@coreminw/Intellij에서-JSP-수정시-자동-리로드-되도록-설정](https://velog.io/@coreminw/Intellij에서-JSP-수정시-자동-리로드-되도록-설정)
