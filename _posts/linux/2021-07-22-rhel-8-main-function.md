---
title: Red Hat Enterprise Linux 8 주요 기능
author: dejavuhyo
date: 2021-07-22 06:00:00 +0900
categories: [DevOps, Linux]
tags: [red-hat-enterprise-linux-8-main-function, rhel-8-main-function, rhel-8-function, red-hat-enterprise-linux-8-주요-기능, rhel-8-주요-기능, rhel-8-기능]
---

## 1. 컨테이너 툴
Red Hat Enterprise Linux 8은 오픈 컨테이너 이니셔티브(Open Container Initiative, OCI) 표준과 호환되는 다양한 툴로 컨테이너를 검색, 실행, 구축 및 공유하도록 시스템을 맞춤 설정할 수 있는 컨테이너 툴을 제공한다. 이처럼 선택의 유연성을 확보하고 애플리케이션 컨테이너 지원을 확대하여 원하는 때에 원하는 방식으로 비즈니스 솔루션을 구현할 수 있다.

### 1) Podman

![podman](/assets/img/2021-07-22-rhel-8-main-function/podman.png)

Podman은 데몬이 없는(daemon-less) 명령줄 툴로서, 사용자가 직접 컨테이너 이미지를 생성하고 관리할 수 있도록 한다. 런타임 환경 없이 컨테이너로 작업할 수 있으므로 소프트웨어 구성 요소에 부여한 권한을 보다 엄격하게 관리할 수 있다.

### 2) Buildah

![buildah](/assets/img/2021-07-22-rhel-8-main-function/buildah.png)

Buildah는 이미지 레이어가 커밋되는 방식과 빌드 도중 데이터 액세스 방식을 제어할 수 있도록 지원하는 빌드 툴이다. Buildah를 사용하기 위해 컨테이너 런타임이 필요하지 않으며 루트 액세스도 요구되지 않는다. Buildah는 이미지 자체 내에 빌드 툴을 포함하지 않으므로, 구축하는 이미지의 크기를 줄여준다.

### 3) Skopeo

![skopeo](/assets/img/2021-07-22-rhel-8-main-function/skopeo.png)

Skopeo는 레지스트리 서버와 컨테이너 호스트 간에 이미지를 이동, 서명 및 검증할 수 있도록 지원하는 유연한 유틸리티이다. Skopeo를 사용하여 시스템 외부 이미지를 검사할 수 있다.

## 2. 프로세스의 간소화
애플리케이션은 실행이 중단되어서는 안되며 OS도 항상 실행되어야 한다. Red Hat Enterprise Linux 8을 사용하면 비즈니스 가치 실현 및 기반 인프라의 수동 관리에 소요되는 시간을 더욱 단축시킬 수 있다. Red Hat Enterprise Linux 8의 빌트인 제어는 새로운 Linux 사용자가 웹 콘솔을 통해 신속하게 적응할 수 있도록 지원하고, 애플리케이션 스트림을 사용하여 간편하게 애플리케이션을 업데이트하며, 규정 준수 및 보안에 집중할 수 있도록 지원한다.

### 1) 어플리케이션 스트림
이제 OS 버전과 상관없이 개발자가 원하는 애플리케이션을 제공할 수 있다. 애플리케이션 스트림을 사용하여 애플리케이션을 기본 OS와 분리하면 다음 주요 OS 버전을 기다릴 필요 없이 애플리케이션을 업데이트할 수 있다.

### 2) 웹콘솔
새로운 웹 콘솔은 Red Hat Enterprise Linux 시스템을 관리하기 위한 브라우저 기반 그래픽 인터페이스를 제공한다. 이 웹 콘솔을 통해 Linux 경험이 풍부하지 않은 사용자는 명령줄을 사용하지 않고도 로컬, 원격 및 가상 머신을 관리할 수 있다. Red Hat Enterprise Linux는 Identity 관리를 개선하도록 지원한다. Identity 관리 구성 요소는 웹 콘솔과 통합하여 간편한 SSO(Single Sign-On)를 제공하고 개별 호스트를 원활하게 관리하도록 해준다.

### 3) 보안
OpenSSL 1.1.1 및 TLS 1.3을 지원하는 시스템 전체의 암호화 정책과 같은 빌트인 보안 제어 기능으로 암호화 규정 준수를 유지관리할 수 있다. 또한 Red Hat Enterprise Linux 8은 워크로드 지원에 필요한 패키지를 배포하는 것만으로 공격 범위(attack surface)를 최소화한다. SELinux(Security-Enhanced Linux) 필수 액세스 제어를 지원하는 보안 정책 개선 사항이 Red Hat Enterprise Linux 8에 포함되어 있다. 이제 서브스크립션에 포함된 Red Hat Insights를 사용하여 보안성, 가용성, 성능 및 확장성과 관련된 시스템 문제와 취약점을 사전에 감지하고 문제 해결 계획을 수립할 수 있다.

## 3. 저장소(Repositories)
RHEL 8은 BaseOS, AppStream 2개 저장소를 사용한다.

### 1) BaseOS 저장소
운영체제의 기반이 되는 기본 기능의 코어 세트를 제공하는데 사용된다. 커널과 같은 기본 OS 기능의 핵심 패키지 그룹으로 물리적 하드웨어, 가상머신, 클라우드 인스턴스 또는 컨테이너에서 실행 가능하도록 하는 패키지를 제공한다.

### 2) AppStream 저장소
운영 서비스에서 수행 가능한 애플리케이션(예: Apache, Cyrus-imapd, Vim 등), 런타임 언어(예: Java, PHP, Python 등) 및 데이터베이스(예: MariaDB, PostgreSQL 등) 등이 포함되어 있다.

BaseOS 저장소의 핵심 운영체제 패키지보다 더 자주 업데이트 된다. BaseOS 저장소와 AppStream 저장소의 분리로 AppStream 저장소 패키지 업데이트 시 이미 구성되어 있는 플랫폼 또는 특정 배포의 기본 안정성에 영향을 주지 않고 업데이트가 가능한 유연성을 제공한다.

예들 들어 PostgreSQL의 경우 v10과 v9.6을 제공하고 원하는 버전을 설치해 사용할 수 있다. 이전에 단일 패키지로 구성되어 최신 버전만을 지원했지만 제공되는 버전 중 원하는 것을 선택해 사용 가능하다.

## 4. RHEL 7과 RHEL 8의 차이점

![difference](/assets/img/2021-07-22-rhel-8-main-function/difference.png)

## [출처 및 참고]
* <https://www.s-core.co.kr/insight/view/rhelred-hat-enterprise-linux-8-%EC%A3%BC%EC%9A%94-%EB%B3%80%EA%B2%BD-%EC%82%AC%ED%95%AD-%EA%B0%80%EC%9D%B4%EB%93%9C/>
* <https://www.redhat.com/ko/enterprise-linux-8/details#technologies>
* <https://sysops.tistory.com/97>
