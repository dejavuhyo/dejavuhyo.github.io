--- 
title: IntelliJ 인코딩 설정
author: Hyosik
date: 2020-12-04 09:40:00 +0900
categories: [Tools, IDE]
tags: [intellij_encoding, ide_encoding, intellij, intellij_인코딩]
---

## 1. 전역 인코딩
현재 프로젝트 또는 프로젝트의 모든 파일 또는 디렉터리에 대해 전체적으로 인코딩 옵션을 구성 할 수 있다.

파일 또는 디렉터리 인코딩이 정의되지 않은 경우 프로젝트 인코딩이 사용된다. 프로젝트 인코딩을 사용할 수 없는 경우(예: 프로젝트가 아직 생성되지 않은 경우) IntelliJ IDEA는 전역 인코딩을 사용한다.

* File → Settings → Editor → File Encodings (for Windows and Linux)

* IntelliJ IDEA → Preferences → Editor → File Encodings (for macOS)

![img001](/assets/img/2020-12-04-intellij-encoding-setting/img001.png)

## 2. VM 설정(IntelliJ 가상머신 Encoding 설정)

### 1) 설치 경로 vmoptions 설정

* Windows
  - \bin\idea64.exe.vmoptions (for the default 64-bit JVM)
  - \bin\idea.exe.vmoptions (for the optional 32-bit JVM)

* macOS
  - IntelliJ IDEA.app/Contents/bin/idea.vmoptions

* Linux
  - /bin/idea64.vmoptions (for the default 64-bit JVM)
  - /bin/idea.vmoptions (for the optional 32-bit JVM)

* **추가:** -Dfile.encoding=UTF-8

![img002](/assets/img/2020-12-04-intellij-encoding-setting/img002.png)

### 2) 사용자 홈 폴더 vmoptions 설정

* Help → Edit Custon VM Opeions

* **추가:** -Dfile.encoding=UTF-8

![img003](/assets/img/2020-12-04-intellij-encoding-setting/img003.png)

## [출처 및 참고]
* <https://www.jetbrains.com/help/idea/2019.2/configuring-individual-file-encoding.html>
* <https://www.jetbrains.com/help/idea/2019.2/settings-file-encodings.html>
