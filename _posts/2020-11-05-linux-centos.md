---
title: Red Hat Linux와 CentOS
author: Hyosik
date: 2020-11-05 11:30:00 +0900
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

## 2. Fedora 리눅스
레드햇이 후원하고 개발 공동체에 의해 진행되는 Linux 배포판으로 새로운 기술과 SW 를 실험하고 선도하는 것을 주요 목적으로 하고 있다. Fedora 개발자는 Open Source SW 를 수정하면 변경한 내용을 페도라 배포본에만 적용하지 않고 원 개발자에게 보내(upstream 패치) 다른 리눅스 배포본 공동체에도 반영되게 하는것을 선호한다. fedora 의 주요 특징중 하나는 보안을 중요시하고 이에 많은 투자를 한다는 점으로 fedora는 SELinux 가 가장 먼저 적용된 OS 이기도 하다.

fedora 위에서 SELinux 의 다양한 정책과 관련 유틸리티가 개발되고 있으며 안정성이 검증되면 RHEL 에 재이식되기도 한다. 예로 Ruby와 Python 의 Application Server 인 Phusion Passenger 가 SELinux 하에서 제대로 동작하지 않자 이를 수정한 policy 가 RHEL 6.4 에 이식되어 배포되기도 한다.

레드햇의 다른 제품들도 Fedora 와 RHEL 의 관계처럼 오픈소스 커뮤니티를 지원하고 이를 활용하여 기능을 개발하고 안정화되면 상용 제품에 반영하는 개발 절차를 밟고 있다. 이는 유명한 웹어플리케이션 서버인 JBoss Community 와  JBoss Enterprise, 기업용 리눅스 관리 솔루션인 Satellite 와 Space Walk 에서도 찾아 볼 수 있다.

> 스트림(Upstream) 패치
>
> 수정한 오픈소스 소프트웨어를 원 저자나 개발팀에게 보내서  메인 개발 소스에 반영시키는 작업을 의미한다. 예로 아파치 웹서버를 사용중인데 버그를 발견후 소스를 내려받아서 수정하고 내부에서만 사용하고 있다고 가정하자. 이 경우 아파치 웹서버 개발팀에 수정된 내용이 전달되지 않았으므로 아파치 웹서버가 업그레이드 되어도 동일한 버그가 존재할 소지가 높고 수정자는 업그레이드때마다 동일한 소스 패치 작업을 반복해야 한다. 수정 부분을 원 개발팀에 전달하여 메인 소스에 반영하는 업스트림 패치가 이루어졌다면 이런 반복 패치 작업을 할 필요가 없어지며 지구상의 또 다른 누군가가 동일한 시행 착오를 겪지 않아도 된다.

## 3. CentOS
CentOS는 Community Enterprise Operating System 의 약자로 Red Hat 이 공개한 RHEL의 Source 를 가져와서 Red Hat 의 브랜드와 로고를 제거하고 컴파일하여 만드는 배포본이다. RHEL 의 소스를 거의 수정없이 사용하므로 RHEL 과 OS 버전, Kernel 버전, 패키지 구성이 똑같고 바이너리가 100%로 호환된다. 무료로 사용 가능하며 문제 발생시 커뮤니티를 통해 지원된다. RHEL 사용시 가장 중요한 기능중 하나인 Yum 저장소도 제공되고 있으며 우리나라에서도 포탈사이트나 IDC 서비스를 제공하는 회사등을 통해 mirror 서비스가 제공되고 있다.

특히 Web Server 로 인기가 높으며 업무의 중요도에 따라 RHEL 과 혼용하여 사용할 수도 있다. 예로 Web Server 는 CentOS, WAS 와 DBMS는 RHEL 의 조합으로도 사용 가능하다. 단점으로는 버그나 보안 패치가 RHEL 보다 늦게 나오며 문제 발생시 자체적으로 해결해야 하는 점이다.

* 배포판별 비교표

![img001](/assets/img/2020-11-05-linux-centos/img001.png)

## 4. RHEL을 기업이 선호하는 이유
OS 는 서비스와 솔루션을 구동하기 위한 중요한 요소이므로 기업에서는 OS 를 선택할때 여러 가지 척도로 제품을 선정하고 있다. 특히 중요한 척도들은 다음과 같다.

* OS 를 설치하려는 장비가 해당 OS를  잘 지원하는가
* 사용하려는 소프트웨어나 솔루션이 해당 OS에서 잘 구동되는가
* 원활한 기술 지원이 제공되는가
* 제품이 지속적으로 업데이트되고 있고 버그나 보안 문제점에 대해 패치가 신속하게 이루어지는가
* 검증된 성능 및 안정성 
* 사용자가 많이 있고 관련 정보를 획득하기 용이한가

왜 여러가지 배포본중에 RHEL 을 기업에서 선호하는지 위 조건에 맞춰서 살펴보자.

### 1) 레드햇 하드웨어 인증
리눅스를 데스크탑으로 사용하고자 할 경우 중요하게 살펴봐야 할 부분은 사용하고 있는 하드웨어가 리눅스에서 잘 동작하는지 여부이다.

사용자의 하드웨어가 리눅스용 장치 드라이버가 없어서 사용을 못 할 수도 있고 또 드라이버가 있지만 제조사의 지원이 윈도에 비하면 미비하여 해당 제품을 사용하는데 문제가 발생할 수 있다.

PC용 하드웨어 제조사는 해당 제품을 윈도에서 사용하는 고객이 많으므로 장치 드라이버나 지원을 윈도를 최우선으로 하는 경향이 있다.

레드햇에서는 IBM이나 HP, Dell 등 인텔의 x86 CPU 기반의 서버 제조사들이 제작하는 서버 제품에 대해 RHEL 이 잘 설치되고 구동되는지 하드웨어 인증을 수행하고 이를 데이타 베이스화하여 제공하고 있다.

이로서 데스크탑으로 사용할 때와는 다르게 인증받은 서버 장비를 구입한다면 장치 드라이버 문제없이 설치와 구동에 대해 보장받고 안정적으로 업무에 사용할 수 있다. 레드햇 인증을 받은 장비의 목록은 <http://www.redhat.com/rhel/compatibility/hardware/> 에서 확인할 수 있다.

### 2) 풍부한 어플리케이션
오라클 DBMS, IBM DB2, MySQL, MariaDB, PostgreSQL 등 대부분의 DBMS 가 RHEL 에서 잘 동작하며 IBM websphere, JBoss, tomcat, jetty 등의 WAS 도 RHEL을 지원하고 있다.

그외 SAP, SAS등 기업에서 많이 사용되는 소프트웨어들은 거의 다 RHEL 을 잘 지원하고 있다.

RHEL의 패키지 정책이 보수적이라 패키지가 잘 업데이트되지 않는 단점도 있지만 기업내에서 리눅스를 서비스용 서버로 사용한다면 버그 패치/보안 패치등을 적용하는 작업외에는 서비스용 서버는 큰 변경이 없으므로 큰 단점이 아닐수 있다.

### 3) 안정적인 기술 지원
subscription을 구매하면 레드햇 고객 포탈(<https://access.redhat.com/home>) 에서 방대하고 양질의 기술 문서와 Know-how 에 접근할 수 있으며 문제 발생시 Web 과 전화를 통해 기술 지원을 받을 수 있다.

RHEL 은 버전 5부터 10년의 프로덕션 단계의 지원을 하고 있으며 이후 3년간 연장 단계 지원을 제공하고 있다. RHEL의 수정이나 패치는 레드햇 고객 포탈이나 기타 공인 사이트에서 이루어 지며  에라타 권고라고 하는 개별 업데이트를 통해 제공된다.

수정된 패키지는 기존 런타임 환경과 바이너리 호환성을 갖기 위해 노력하고 있다.

* 단계별 지원 내역은 다음 표와 같다.

|  | 프로덕션 단계1 (약 5년 6개월) | 프로덕션 단계2 (약 1년) | 프로덕션 단계3 (약 3년 6개월) | 연장 라이프 단계 (약 3년) |
|:---|:---:|:---:|:---:|:---:|:---:|
| Red Hat Customer Portal을 통해 이전에 출시된 콘텐츠에 액세스 | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) |
| Red Hat Customer Portal을 통한 자가 지원 | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) |
| 기술 지원 | 무제한 | 무제한 | 무제한 | 기존 설치에 한해 지원 |
| RHSA(비동기 보안 에라타) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![error](/assets/img/2020-11-05-linux-centos/error.png) |
| 비동기 버그 픽스 Errata(RHBA) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![error](/assets/img/2020-11-05-linux-centos/error.png) |
| 업데이트 릴리스 | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![error](/assets/img/2020-11-05-linux-centos/error.png) |
| 개정된 하드웨어 지원 | 기본 | 소스 변경이 많이 않을 경우에 한해 지원 | 가상화 사용 | 가상화 사용 |
| 소프트웨어 기능 개선 | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![error](/assets/img/2020-11-05-linux-centos/error.png) | ![error](/assets/img/2020-11-05-linux-centos/error.png) | ![error](/assets/img/2020-11-05-linux-centos/error.png) |
| 업데이트된 설치 이미지 | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![check](/assets/img/2020-11-05-linux-centos/check.png) | ![error](/assets/img/2020-11-05-linux-centos/error.png) |

* 단계별 기술 지원표

위와 같이 긴 제품 수명 주기를 갖고 지원을 하고 있으므로 OS 의 해당 버전 단종이나 기술 지원 만료로 구동하는 솔루션이나 서비스에 문제가 생길수 있는 우려가 적다. (RHEL 3, 4는 7년을 지원한다.)

![img002](/assets/img/2020-11-05-linux-centos/img002.png)

* RHEL 기술지원 연대표

또한 국내의 많은 리눅스 기술지원 업체와 별도 계약을 통해 장애나 문제 발생시 외부 전문가들의 현장 방문 지원도 받을 수 있다.

### 4) 검증된 성능과 안정성
많은 솔루션이 리눅스위에서 최적의 성능을 발휘하고 있으며 주식 거래 같은 미션 크리티컬한 업무도 안정적이고 지속적으로 제공할 수 있을 정도로 성능과 안정성이 검증되어 있다.

### 5) 지속적인 업데이트 및 보안 패치
레드햇은 주기적으로 마이너 버전을 출시하면서 기능을 개선하고 있으며 기존 패키지도 개별로 업데이트를 제공하고 있다.

인터넷을 통한 공격이 늘어나면서 보안성은 핵심적인 평가 지표중에 하나이다. RHEL 은 보안을 고려하여 설계/개발되었고 linux 커널 기반의 보안 강화 기능인 SELinux 의 개발을 주도적으로 지원하였으며 이를 RHEL 에 적용하여 SELinux 와 완벽한 통합을 자랑한다.

RHEL 의 보안 대응팀은 신규 보안취약점(CVE - Common Vulnerabilities and Exposure)이 발표되면 발빠르게 대응하여 신속하게 패치를 발표하고 있으며 가끔은 원 저자보다 보안 취약점을 빨리 수정하고 업스트림 패치를 보내는 경우도 있다.

### 6) 폭넓은 사용자층과 정보
사용자가 많으므로 관련 정보 얻기가 쉬우며 검색 엔진과 블로그등을 통해 많은 정보와 지식을 얻을 수 있다.

fedora 도 훌륭한 배포본이지만 위과 같은 이유로 안정성이 우선인 기업환경에서는 RHEL을 선호할 수 밖에 없다. 

fedora 를 설치하고 사용하면서 버그 리포팅을 하는 것은 Linux 공동체에 기여하는 좋은 방법이지만 이는 Desktop 이나 개인적인 컴퓨팅 환경에 사용하고 기업에서 Linux 를 서버로 도입한다면 RHEL 이나 이의 파생 배포본을 기반으로 진행하는 방안을 권장한다.

## [출처 및 참고]
* <https://www.lesstif.com/linux-infra-book/red-hat-linux-centos-20775405.html>
