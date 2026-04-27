---
title: Windows에 Terraform 설치
author: dejavuhyo
date: 2026-04-27 13:50:00 +0900
categories: [DevOps, Terraform]
tags: [terraform, terraform-install, 테라폼, 테라폼-설치]
---

## 1. Terraform 다운로드
[Terraform](https://developer.hashicorp.com/terraform/install) 사이트에서 install 파일 다운로드 한다.

Windows 환경이기 때문에 Windows 설치 파일을 다운로드 한다.

![download](/assets/img/2026-04-27-install-terraform-windows/download.png)

## 2. 폴더 생성
`C:\terraform`폴더를 만들고, 다운로드 받은 `terraform.exe` 파일을 생성한 폴더로 이동한다.

![terraform-file](/assets/img/2026-04-27-install-terraform-windows/terraform-file.png)

## 3. 환경변수 설정

* `Path`를 선택하고 편집 버튼을 누른다.

![path](/assets/img/2026-04-27-install-terraform-windows/path.png)

* Terraform 경로를 추가한다.

![terraform-path](/assets/img/2026-04-27-install-terraform-windows/terraform-path.png)

## 4. 설치 확인
`CMD`창에서 `terraform` 명령어를 입력한다.

![check](/assets/img/2026-04-27-install-terraform-windows/check.png)

## [출처 및 참고]
[https://1104minho.tistory.com/27](https://1104minho.tistory.com/27)
