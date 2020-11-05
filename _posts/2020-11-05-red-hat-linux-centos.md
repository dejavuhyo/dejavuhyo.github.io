---
title: Red Hat Linux와 CentOS
author: Hyosik
date: 2020-11-05
categories: [OS, Linux]
tags: [red_hat_linux, rhel, linux, centos, linux_centos]
---

## 1. Red Hat Enterprise Linux(RHEL)
RHEL 은 Red Hat 사가 개발하는 상업용 리눅스 배포판으로 Intel 과 AMD 의 x86과 x86-64 아키텍처가 주요 플랫폼이며 추가로 IBM POWER 와 IBM System Z & S/390 아키텍처도 지원하고 있다. 버전 5까지 Intel의 Itanium 플랫폼도 지원했으나 6 부터 제외가 되었다.

소스는 공개되지만 매년 기술 지원에 대해 비용을 지불해야 사용할 수 있는 서브스크립션(subscription) 라이선스이며 구매한 고객들은 문제 발생시 기술 지원과 OS 나 SW 의 패치와 업그레이드를 받을 수 있다.

주요 목표는 기업 환경에 맞게 안정성과 성능, 보안성을 최우선 목적으로 하고 있으며 이에 따라 철저하게 검증된 기능과 패키지만 배포본에 포함시키고 있다.

하지만 안정성 중시에 따라 내장된 패키지가 오래된 버전인 경우가 많아서 새로운 개발 도구나 프레임워크등을 사용하려면 기반 도구부터 패키지까지 별도로 설치와 업그레이드를 해 주어야 하는 경우도 많다.

RHEL 은 특정 버전의 페도라(Fedora) 리눅스 배포판을 기반으로 하여 검증되고 안정화된 코드를 채택하여 개발된다. 또 기반이 된 페도라의 이후 버전에 추가된 기능이라도 중요하거나 유용하다면 해당 기능들을 RHEL 로 역이식(backport)하기도 한다.

2010년 출시되어 현재 주로 쓰이고 있는 버전 6의 RHEL 은 Linux kernel 2.6과 fedora 12 를 기반으로 하여 13, 14등에 추가된 기능들을 역이식하여 개발되었다. 13년 12월 Beta 버전을 발표한 RHEL 7 은 Linux kernel 3.10 과 Fedora 19 를 기반으로 개발되고 있다.

RHEL의 안정성은 유명하여 Oracle 사의 Linux 배포본인 Oracle Linux 도 RHEL 을 기반으로 하고 있으며 아마존 웹서비스에 사용되는 Amazon Linux AMI 및 라우터등의 다양한 어플라이언스에서도 사용되고 있다.

그외에 유럽 입자 물리 연구소(CERN)와 미국의 페르미 국립 가속기 연구소(FermiLab) 에서 과학자들의 연구/개발에 사용하기 위해 개발한 Scientific Linux 도 RHEL 기반의 유명한 배포판이다.

## [출처 및 참고]
* <https://www.lesstif.com/linux-infra-book/red-hat-linux-centos-20775405.html>
