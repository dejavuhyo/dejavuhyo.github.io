--- 
title: IntelliJ 메모리 설정
author: Hyosik
date: 2020-11-18 09:30:00 +0900
categories: [Tools, IDE]
tags: [intellij-memory, intellij-jvm, intellij, memory]
---

## 1. 메모리 부족 메시지
IntelliJ 동작이 느리거나 메모리 부족 메시지가 뜬다.

```text
Low Memory
The IDE is running low on memory and this  mightaffect performance. please consider increasing available heap.
```

## 2. 메뉴에서 설정

### 1) 메뉴

* Help → Edit Custon VM Opeions

![img001](/assets/img/2020-11-18-intellij-memory-setting/img001.png)

### 2) Xms, Xmx 설정

* Xms
  - IntelliJ를 실행하기 위해 JVM에서 할당한 초기 메모리를 지정한다. 기본값은 플랫폼에 따라 다르다. 일반적으로 허용되는 최대 메모리(-Xmx)의 약 절반으로 설정한다.

* Xmx
  - JVM이 IntelliJ 실행을 위해 할당 할 수있는 최대 메모리 힙 크기를 제한한다. 기본값은 플랫폼에 따라 다르다. 속도가 느려지는 경우이 값을 늘릴 수 있다.

![img002](/assets/img/2020-11-18-intellij-memory-setting/img002.png)

## 3. 파일에서 설정

### 1) 설치 경로 이동

* Windows
  - <IDE_HOME>\bin\idea64.exe.vmoptions (for the default 64-bit JVM)
  - <IDE_HOME>\bin\idea.exe.vmoptions (for the optional 32-bit JVM)

* macOS
  - IntelliJ IDEA.app/Contents/bin/idea.vmoptions

* Linux
  - <IDE_HOME>/bin/idea64.vmoptions (for the default 64-bit JVM)
  - <IDE_HOME>/bin/idea.vmoptions (for the optional 32-bit JVM)

### 2) Xms, Xmx 설정

* Xms
  - IntelliJ를 실행하기 위해 JVM에서 할당한 초기 메모리를 지정한다. 기본값은 플랫폼에 따라 다르다. 일반적으로 허용되는 최대 메모리(-Xmx)의 약 절반으로 설정한다.

* Xmx
  - JVM이 IntelliJ 실행을 위해 할당 할 수있는 최대 메모리 힙 크기를 제한한다. 기본값은 플랫폼에 따라 다르다. 속도가 느려지는 경우이 값을 늘릴 수 있다.

![img003](/assets/img/2020-11-18-intellij-memory-setting/img003.png)

> **경고:** IntelliJ IDEA가 업데이트 될 때 대체되므로 기본 파일에서 JVM 옵션을 변경하지 마십시오. 또한 macOS의 경우이 파일을 편집하면 응용 프로그램 서명을 위반합니다.

## 3. Memory Indicator 보기 설정

* File → Settings

![img004](/assets/img/2020-11-18-intellij-memory-setting/img004.png)

* Appearance → Show memory indicator 체크

![img005](/assets/img/2020-11-18-intellij-memory-setting/img005.png)

* 확인

![img006](/assets/img/2020-11-18-intellij-memory-setting/img006.png)

## [출처 및 참고]
* <https://www.jetbrains.com/help/idea/tuning-the-ide.html#configure-jvm-options>
* <https://www.jetbrains.com/help/idea/2016.2/increasing-memory-heap.html>
