---
title: CentOS Linux 대안
author: dejavuhyo
date: 2021-07-19 06:00:00 +0900
categories: [DevOps, Linux]
tags: [centos-linux-alternative, centos-alternative, centos-linux-대안, centos-대안]
---

## 1. CentOS Linux 개발 종료
Red Hat에서 RHEL 재구축으로 CentOS Linux 개발 종료를 발표하면서 많은 Linux 사용자의 마음을 아프게 했다.

비록 CentOS가 완전히 비활성화된 것이 아니라 CentOS Linux는 현재 Red Hat의 업스트림(개발)으로 계속 발전하고 있다. 새로운 CentOS 운영 체제의 이름은 CentOS Stream이다.

이전 CentOS를 [CentOS Stream으로 업그레이드](https://www.centlinux.com/2021/01/upgrade-centos-linux-8-server-to-stream.html)할 수 있다.

Red Hat이 전통적인 CentOS 리눅스 릴리스와 같은 정기적인 업데이트를 제공하기로 약속했지만, 아무도 CentOS는 더 이상 RHEL의 안정적인 대안이 아니다.

CentOS 커뮤니티의 실망을 해결하기 위해, Red Hat은 최대 16대의 프로덕션 서버에 대한 RHEL의 무료 라이센스를 발표했다.

이 숫자는 중소기업에 적합할 정도이다. 하지만 그들이 언제까지 이 무료 Linux 운영 체제를 계속 제공할 것인지, 아니면 이것은 CentOS Linux 사용자를 RHEL로 전환하기 위한 마케팅 전략일 뿐인지 지켜보자.

한편 CentOS 커뮤니티는 리눅스의 미래에 대해 매우 절망적이다. 또한 IT 요구사항을 충족할 수 있는 대안을 모색하고 있다.

현재 CentOS Linux에서 마이그레이션하려는 경우 다음과 같은 방법을 사용하는 것이 좋다.

## 2. Rocky Linux

![rocky-linux](/assets/img/2021-07-19-centos-linux-alternatives/rocky-linux.png)

[Rocky Linux](https://rockylinux.org/)는 다운스트림 파트너가 방향을 전환한 현재 미국 최고의 엔터프라이즈 Linux 배포판과 100% 호환되도록 설계된 커뮤니티 엔터프라이즈 운영 체제이다.

Rocky Linux는 커뮤니티에 의해 집중적으로 개발되고 있다. Rocky Linux는 CentOS 프로젝트의 설립자인 Gregory Kurtzer가 이끌고 있다.

릴리즈에 대한 ETA는 없다. Rocky Linux에 대한 자세한 내용은 [GitHub](https://github.com/rocky-linux) 페이지에서 확인할 수 있다.

> **참고:** [Migrate CentOS 8 Operating System to Rocky Linux](https://www.centlinux.com/2021/07/migrate-centos-8-operating-system-rocky-linux.html)

## 3. AlmaLinux

![almalinux](/assets/img/2021-07-19-centos-linux-alternatives/almalinux.png)

[AlmaLinux](https://almalinux.org/)는 Project Lenix라는 코드 이름으로 시작된다. AlmaLinux는 오픈 소스이며, community-driven project로 CentOS의 안정적 릴리즈가 끝나면서 남은 공백을 메우려는 것이다. AlmaLinux는 RHEL 8의 1:1 binary compatible fork이며, 기존 CloudLinux OS의 제작자에 의해 구축된다.

독립 실행형으로서, 완전히 무료 OS인 AlmaLinux는 CloudLinux Inc.로부터 연간 100만 달러의 후원을 받고 있다. AlmaLinux를 업데이트하여 향후 RHEL 릴리스를 지원할 예정이다. 지속적인 개발 노력은 community 구성원에 의해 통제된다.

AlmaLinux는 엔터프라이즈급 서버 OS로, 긴 지원 윈도우와 함께 제공되는 정기적인 릴리스가 포함된 안정적인 리눅스 배포이다. AlmaLinux를 사용하여 사용자와 클라이언트의 중요 워크로드를 실행할 수 있다.

AlmaLinux의 첫 베타 버전은 2021년 1월 28일에 이미 출시되었다. AlmaLinux 웹사이트에서 다운로드할 수 있습니다.

> **참고:** [How to Migrate CentOS 8 Operating System to AlmaLinux](https://www.centlinux.com/2021/07/migrate-centos-8-operating-system-to-almalinux.html)

## 4. Oracle Linux

![oracle-linux](/assets/img/2021-07-19-centos-linux-alternatives/oracle-linux.png)

[Oracle Linux](https://www.oracle.com/linux/)는 개방형 완벽한 운영 환경이다. Oracle Linux는 가상화, 관리 및 클라우드 네이티브 컴퓨팅 툴과 운영 체제를 단일 지원 제품으로 제공한다. Oracle Linux는 Red Hat Enterprise 리눅스와 100% 호환되는 애플리케이션 바이너리이다.

2006년부터 Oracle Linux는 완전히 자유롭게 다운로드하고 사용할 수 있게 되었다. 사용 가능한 소스 코드, 이진 파일 및 업데이트 자유롭게 재배포 가능하다. 프로덕션용으로 무료이다.

> **참고:** [How to Migrate CentOS 8 Operating System to Oracle Linux](https://www.centlinux.com/2021/07/migrate-centos-8-operating-system-to-oracle-linux.html)

## 5. Red Hat Enterprise Linux

![red-hat-enterprise-linux](/assets/img/2021-07-19-centos-linux-alternatives/red-hat-enterprise-linux.png)

[Red Hat Enterprise Linux(RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux)는 퍼블릭 클라우드에 가장 많이 구축된 상용 리눅스 배포판이다. RHEL은 수백 개의 퍼블릭 클라우드 및 서비스 프로바이더는 물론 수천 개의 다른 유형의 소프트웨어 및 하드웨어에 대해 인증을 받았다.

Red Hat Enterprise Linux는 세계 최고의 오픈 소스 운영 체제이다. 내장된 보안 및 관리 편의성으로 제어 기능을 제공하므로 안정적인 고성능 플랫폼에서 중요 워크로드를 자신 있게 실행할 수 있다. 애플리케이션 배포, 데이터 센터 인프라 또는 가상 및 클라우드 환경에 무료로 Red Hat Enterprise Linux를 평가할 수 있다.

> **참고:** [How to Migrate CentOS 8 Operating System to RHEL](https://www.centlinux.com/2021/02/migrate-centos-linux-to-rhel-8-operating-system.html)

## 6. Springdale Linux

![springdale-linux](/assets/img/2021-07-19-centos-linux-alternatives/springdale-linux.png)

[Springdale Linux](http://springdale.math.ias.edu/)는 프린스턴 대학교 및 고등 연구소의 컴퓨팅 담당 직원이다. Scientific Linux와 마찬가지로 CentOS Linux의 Scientific Linux fork이다. (작년에 발표되었다.)

Springdale은 잘 알려져 있지 않지만, 개발자들은 CentOS가 Springdale을 출시하기 전에 스프링데일 리눅스 8 스프링데일 8을 출시했다고 한다.

사실 8의 DVD ISO는 사용할 수 없지만, Springdale Linux 8.3의 Boot/PXE ISO는 다운로드할 수 있다.

> **참고:** [How to Migrate CentOS 8 Operating System to Springdale Linux](https://www.centlinux.com/2021/07/migrate-centos-8-operating-system-springdale-linux.html)

## [출처 및 참고]
* [https://www.centlinux.com/2021/02/centos-linux-is-dead-what-are-alternatives.html](https://www.centlinux.com/2021/02/centos-linux-is-dead-what-are-alternatives.html)
* [https://rockylinux.org/](https://rockylinux.org/)
* [https://almalinux.org/](https://almalinux.org/)
* [https://www.oracle.com/linux/](https://www.oracle.com/linux/)
* [https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux)
* [http://springdale.math.ias.edu/](http://springdale.math.ias.edu/)
