---
title: WSL2에 Ubuntu 설치
author: dejavuhyo
date: 2025-11-25 15:00:00 +0900
categories: [DevOps, Linux]
tags: [wsl-ubuntu, wsl-ubuntu-install, ubuntu-imstall, wsl-ubuntu-설치]
---

## 1. Windows 기능 설정

* Winodws 기능 켜기/끄기 → Linux용 Windows 하위 시스템

![windows-control-panel](/assets/img/2025-11-25-install-ubuntu-on-wsl2/windows-control-panel.png)

## 2. Ubuntu 설치

### 1) 방법 1: Console 설치

```shell
wsl --install -d Ubuntu-22.04.5
```
### 2) 방법 2: Microsoft Store에서 Ubuntu 22.04.5 LTS 설치

![microsoft-store](/assets/img/2025-11-25-install-ubuntu-on-wsl2/microsoft-store.png)

## 3. Ubuntu 터미널 열기

* Windows PowerShell → Ubuntu 선택

![powershell](/assets/img/2025-11-25-install-ubuntu-on-wsl2/powershell.png)
