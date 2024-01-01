---
title: Linux 배포판
author: dejavuhyo
date: 2021-07-21 06:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-distribution, 리눅스-배포판]
---

## 1. Linux 배포판

![linux-distribution](/assets/img/2021-07-21-linux-distribution/linux-distribution.png)

Linux 배포판은 Linux 커널을 중심으로 여러 시스템 소프트웨어 및 응용 소프트웨어를 같이 묶어서 구성한 운영 체제이다. 흔히 패키지 매니저, 개발 툴, 체인, 각종 유틸리티 프로그램, 데스크톱 환경 등을 포함한다.

주요 용도에 따라 데스크톱 용도, 서버 용도, 임베디드 용도, 다용도 배포판 등 여러 배포판이 존재한다.

Linux GUI 요소들은 배포판 간의 차이보다는 데스크톱 환경의 차이가 크므로 데스크톱 환경에 대해 알아보는 것이 좋다. 물론 배포판에 따라서 지원되는 데스크톱 환경들에 차이가 있다.

매우 다양한 데스크톱 환경을 지원하는 배포판(Ubuntu, Arch Linux 등)도 있는 반면 하나의 데스크톱 환경을 전문적으로 지원하는 배포판(elementary OS, Deepin 등)도 있다. Linux 배포판들은 주로 패키지 관리자와 저장소를 기준으로 묶인다.

> **참고:** [Linux 배포판 계보도](https://upload.wikimedia.org/wikipedia/commons/8/8c/Linux_Distribution_Timeline_Dec._2020.svg)

## 2. Linux 배포판 솔루션 업체

### 1) [Red Hat](https://www.redhat.com/en)
미국 기반의 Linux 대표기업이다. 오픈소스 비즈니스를 하는 기업 중 가장 성공한 기업이라 할 수 있다. 2012년에는 오픈소스 기업으로는 처음으로 매출 1조원을 기록하기도 했다.

전 세계적으로 엔터프라이즈 서버 환경에서 점유율이 높은 Red Hat Enterprise Linux와 클라우드 관련 솔루션을 판매하고 있다. 2018년 IBM이 40조원에 인수하였고, 5분기 연속 역성장 후 지난해 4분기 시장 예상치를 상회하는 매출액을 기록하며 Red Hat과의 시너지에 대한 기대감을 높이고 있다.

### 2) [CentOS](https://www.centos.org/)
CentOS는 회사가 아니라 오픈소스 프로젝트이며, Red Hat Enterprise Linux(RHEL)의 소스 코드를 재빌드하여 무료로 사용할 수 있는 Linux 배포본 프로젝트이다.

2014년 Red Hat이 CentOS 프로젝트를 흡수했다. CentOS 프로젝트의 핵심 개발자들을 채용하고, RHEL 발표 후 전보다 짧은 시간 내 CentOS 버전이 출시될 수 있도록 하고 있다.

### 3) [Oracle](https://www.oracle.com/linux/)
2006년부터 RHEL의 소스 코드를 재빌드하고 리브랜딩하여 Oracle Linux를 배포하고 있다. Oracle Exadata, Oracle Databse 등의 Oracle Engineered Systems에서 주로 사용되고 있다.

매번 발표하는 패치를 별도의 Subscription 없이도 다운로드받을 수 있고, 저렴한 Subscription 비용과 각 버전에 대한 10년 주기의 Life Cycle을 보장하는 등 Red Hat과의 차별점을 부각하기 위해 노력하고 있다.

### 4) [SUSE](https://www.suse.com/)
유럽에서 강세를 보이는 독일 기반의 Linux 배포판 업체이다. SAP와의 협업을 통해 SAP 솔루션의 높은 점유율을 가지는 SUSE Linux Enterprise Server와 Kubernetes 기반의 클라우드 애플리케이션 플랫폼 솔루션을 판매하고 있다.

2003년Novell로 인수합병, 2010년 The Attachmate Group이 Novell을 인수, 2014년 Micro Focus와 합병, 2018년 SUSE Linux EQT 사모펀드에 매각. SUSE는 그동안 굴곡이 정말 많았다. 독립회사로서 다시 유럽의 강자가 되기 위한 준비를 하고 있다.

### 5) [Canonical](https://canonical.com/)
Ubuntu 개발자인 마크 셔틀워스(Mark Richard Shuttleworth)가 Ubuntu의 보급을 위해 세운 회사이다.

Debian 기반의 배포본이며, 현재 개발자들이 가장 많이 사용하는 Linux 배포본으로 자리 잡았다. 개발자들이 많이 사용하고 있기 때문에 상당수의 오픈소스 플랫폼들이 Ubuntu 기반에서 개발되거나 최적화되어 있다.

## 3. 기술 및 기능
수많은 배포본이 존재하지만, Linux의 전반적인 기능은 대동소이하다. 커널-리소스 접근 관리, 스케줄링 등 코어 부분은 거의 동일하고 라이브러리, 설정 파일, 패키지 설치 및 시스템 관리 기능 등은 회사별로 조금씩 차이가 있다.

### 1) Red Hat Enterprise Linux
안정성을 우선하기 때문에 신기술/신기능 적용을 보수적으로 진행한다. 국내 높은 시장점유율과 함께 운영자들의 운영환경 경험이 많아 가장 선호하는 배포본이다.

Red Hat이 후원하고 있는 Fedora(일반 사용자들을 위한 배포본 프로젝트)를 통해서 새로운 기술이나 기능을 먼저 선보이고, 어느 정도 안정화되고 나면 이러한 기술 및 기능을 기업용 배포본인 Red Hat Enterprise Linux로 포함해 발표한다. 한국 레드햇의 국내 기술지원 인력은 다른 Linux 배포본 업체보다 상대적으로 많다.

### 2) CentOS
Red Hat Enterprise Linux와 동일한 소스 코드 기반으로 커뮤니티에서 새로 빌드해 제공한다. (단, RHEL의 모든 소스 코드를 포함하지는 않는다.)

2014년에 CentOS 프로젝트가 Red Hat으로 흡수되어 빌드와 개발을 맡은 주요 개발자들이 대부분 Red Hat으로 입사하였으나, 기존과 동일하게 커뮤니티 기반으로 활동하고 있다. Red Hat Enterprise Linux에 비해 일부 하드웨어 플랫폼, 드라이버, 특정 기능이 제외된다.

국내 파트너사를 통한 1차 기술지원을 받을 수는 있으나, Red Hat으로부터의 2, 3차 기술지원은 받을 수 없다. 대부분의 주요 상용 애플리케이션(Oracle, SAP 등)은 CentOS를 인증하지 않는다.

### 3) Oracle Linux
Red Hat Enterprise Linux와 동일한 소스 코드 기반으로 Oracle에서 새로 빌드하여 제공하고 있다.

2010년 9월부터 Oracle Engineered Systems에 최적화된 UEK(Unbreakable Enterprise Kernel)를 탑재하여 발표하였다. Solaris의 핵심기능 중 하나인 Dtrace 기능을 사용하여 OS 성능 및 상태를 분석, 추적할 수 있다.

Oracle Database RAC 구성을 위한 Oracle Clusterware가 기본으로 포함되어 있습니다. Oracle Database나 Oracle Business Application 등 Oracle의 소프트웨어를 이미 사용하고 있거나 사용하려는 기업에 적합한 배포본이다.

### 4) SUSE Linux Enterprise Server
신기술, 신기능에 대한 적용(예를 들어, 커널 라이브 패치 등)이 빠르다. 또한 초기부터 IBM Power Systems과 IBM z System 포팅을 가장 적극적으로 지원하여 Red Hat Enterprise Linux보다 높은 안정성을 보여주고 있다.

시스템 관리나 특정 솔루션(SAP 솔루션)에 대한 연계 기능(예: 페이지 캐시 메모리 관리 등) 적용이 빠르다. 수세 코리아는 주로 Hardware 서버 벤더 OEM 계약으로 1, 2차 지원 또는 파트너사 지원체계를 운영한다.

### 5) Ubuntu
Debian 기반에서 시작하였고, 편리한 설치와 사용법 때문에 데스크톱 환경에서 많이 사용하고 있다.

초기에는 데스크톱 환경으로 가장 많이 사용하는 배포본으로 널리 알려졌고, 이후 서버용으로도 사용하고 있다. 6개월 단위로 새 버전을 발표하며, 2년 주기로 LTS(Long Term Support: 5년간 기술지원 제공) 버전을 발표한다.

## [출처 및 참고]
* [https://www.samsungsds.com/kr/insights/linux_distribution.html](https://www.samsungsds.com/kr/insights/linux_distribution.html)
* [https://namu.wiki/w/Linux/%EB%B0%B0%ED%8F%AC%ED%8C%90](https://namu.wiki/w/Linux/%EB%B0%B0%ED%8F%AC%ED%8C%90)
