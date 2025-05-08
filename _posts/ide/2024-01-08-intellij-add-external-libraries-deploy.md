---
title: IntelliJ 외부 라이브러리 추가하여 배포
author: dejavuhyo
date: 2024-01-08 09:20:00 +0900
categories: [DevOps, IDE]
tags: [intellij-jar, intellij-maven-jar, build-artifacts, 외부-jar-배포, jar-배포, jar-추가-배포, 라이브러리-추가]
---

## 1. Project Structure 이동

* File → Project Structure

![project-strecture](/assets/img/2024-01-08-intellij-add-external-libraries-deploy/project-strecture.png)

## 2. Dependencies 확인

![modules](/assets/img/2024-01-08-intellij-add-external-libraries-deploy/modules.png)

## 3. Libraries 확인

![libraries](/assets/img/2024-01-08-intellij-add-external-libraries-deploy/libraries.png)

## 4. Artifacts 추가

* JAR → From modules with dependencies

![artifacts](/assets/img/2024-01-08-intellij-add-external-libraries-deploy/artifacts.png)

## 5. Create JAR from Modules

* Main Class 선택

* extract to the target JAR 선택

![target-jar](/assets/img/2024-01-08-intellij-add-external-libraries-deploy/target-jar.png)

## 6. Main Class 선택

![select-class](/assets/img/2024-01-08-intellij-add-external-libraries-deploy/select-class.png)

## 7. Artifacts 추가 라이브러리 확인

![artifats-jar](/assets/img/2024-01-08-intellij-add-external-libraries-deploy/artifats-jar.png)

## 8. Build

![build](/assets/img/2024-01-08-intellij-add-external-libraries-deploy/build.png)

![build-artifact](/assets/img/2024-01-08-intellij-add-external-libraries-deploy/build-artifact.png)

## 9. JAR 파일 배포 확인

* 생성된 out 폴더 확인

![out](/assets/img/2024-01-08-intellij-add-external-libraries-deploy/out.png)

## [출처 및 참고]
* [https://tlo-developer.tistory.com/308](https://tlo-developer.tistory.com/308)
