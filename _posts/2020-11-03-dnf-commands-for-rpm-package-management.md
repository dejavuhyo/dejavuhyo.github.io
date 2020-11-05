---
title: Linux에서 RPM 패키지 관리를 위한 DNF 명령
author: Hyosik
date: 2020-11-03 17:33:00
categories: [OS, Linux]
tags: [linux_dnf, centos_dnf, dnf, dandified_yum, dnf_commands, rpm_패키지_관리, dnf_명령]
---

## 1. DNF란
DNF(Dandified YUM)는 RPM 기반 배포를 위한 차세대 패키지 관리자다. Fedora 18에 처음 도입되었으며, 최근 출시된 Fedora 22에서 YUM 유틸리티를 대체하고 있다.

DNF는 YUM viz, 성능, 메모리 사용량, 종속성 해결, 속도 및 기타 많은 요인의 병목 현상을 개선하는 것을 목표로 한다.

DNF는 RPM, libsolv 및 hawkey 라이브러리를 사용하여 패키지 관리를 한다.

CentOS 및 RHEL 7에 별도로 설치되지는 않지만 yum, dnf 및 yum과 함께 사용할 수 있다.

* [Yum을 DNF로 교체해야 하는 이유](https://www.tecmint.com/dnf-next-generation-package-management-utility-for-linux/)

DNF의 최근 안정적 발매는 1.0(우편 작성 당시)으로 2015년 5월 11일에 발매되었다. 그것(그리고 모든 이전 버전의 DNF) 대부분은 Python으로 작성되며 GPL v2 License에 따라 출시된다.

## 2. DNF 설치
RHEL/CentOS 7의 기본 저장소에서는 DNF를 사용할 수 없다. 그러나 Fedora 22는 DNF가 공식적으로 구현된 상태로 제공된다.

RHEL/CentOS 시스템에 DNF를 설치하려면 먼저 epel-release 저장소를 설치하고 활성화해야 한다.

```bash
[root@localhost ~]# yum install epel-release
OR
[root@localhost ~]# yum install epel-release -y
```

다음으로 epel-release 저장소 에서 yum 명령을 사용하여 DNF 패키지를 설치 한다.

```bash
[root@localhost ~]# yum install dnf
```

## 3. DNF 명령어

### 1) DNF 버전 확인
시스템에 설치된 DNF의 버전을 확인

```bash
[root@localhost ~]# dnf --version
```

![img001](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img001.gif)

### 2) 활성화 된 DNF 저장소 나열
dnf 명령을 사용하는 _repolist_ 옵션은 시스템에서 활성화된 모든 저장소를 표시

```bash
[root@localhost ~]# dnf repolist
```

![img002](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img002.gif)

### 3) 활성화 및 비활성화된 모든 DNF 저장소 나열
_repolist all_ 옵션 은 시스템에서 활성화/비활성화된 모든 저장소를 나열

```bash
[root@localhost ~]# dnf repolist all
```

![img003](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img003.gif)

### 4) DNF를 사용하여 사용 가능하고 설치된 모든 패키지 나열
_dnf list_ 명령은 모든 저장소에서 사용 가능한 모든 패키지와 Linux 시스템에 설치된 패키지를 나열

```bash
[root@localhost ~]# dnf list
```

![img004](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img004.png)

### 5) DNF를 사용하여 설치된 모든 패키지 나열
_dnf list_ 명령은 모든 저장소에서 사용 가능한/설치된 모든 패키지를 표시합니다. 그러나 아래와 같이 _list installed_ 옵션을 사용하여 설치된 패키지만 나열하는 옵션이 있다.

```bash
[root@localhost ~]# dnf list installed
```

![img005](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img005.png)

### 6) DNF를 사용하여 사용 가능한 모든 패키지 나열
마찬가지로 _list available_ 옵션은 활성화된 모든 저장소에서 설치할 수 있는 모든 패키지를 나열한다.

```bash
[root@localhost ~]# dnf list available
```

![img006](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img006.png)

### 7) DNF를 사용하여 패키지 검색
만약 설치하고자하는 패키지에 대해 전혀 모른다면, 그런 상황에서 dnf 명령과 함께 _search_ 옵션을 사용하여 단어나 문자열 (예: nano)과 일치하는 패키지를 검색 할 수 있다.

```bash
[root@localhost ~]# dnf search nano
```

![img007](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img007.gif)

### 8) 파일/하위 패키지를 제공하는 것을 검색
dnf 옵션 _provides_ 는 특정 파일/하위 패키지를 제공하는 패키지의 이름을 찾는다. 예를 들어, 시스템에서 _/bin/bash_ 를 제공하는 것을 찾고 싶을때

```bash
[root@localhost ~]# dnf provides /bin/bash
```

![img008](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img008.gif)

### 9) DNF를 사용하여 패키지 세부 정보 얻기
패키지를 시스템에 설치하기 전에 패키지 정보를 알고 싶을때, _info_ 를 사용 하여 아래와 같이 패키지 (예: nano)에 대한 자세한 정보를 얻을 수 있다.

```bash
[root@localhost ~]# dnf info nano
```

![img009](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img009.gif)

### 10) DNF로 패키지 설치
nano 라는 패키지를 설치하려면, 아래 명령을 실행하면 패키지 nano에 필요한 모든 종속성이 자동으로 해결되고 설치된다.

![img010](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img010.gif)

```bash
[root@localhost ~]# dnf install nano
```

### 11) DNF를 사용하여 패키지 업데이트
특정 패키지 (예: systemd)만 업데이트 하고 시스템의 모든 것을 그대로 둘 수 있다.

```bash
[root@localhost ~]# dnf update systemd
```

![img011](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img011.gif)

### 12) DNF를 사용하여 시스템 업데이트 확인
간단히 시스템에 설치된 모든 시스템 패키지의 업데이트를 확인한다.

```bash
[root@localhost ~]# dnf check-update
```

![img012](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img012.gif)

### 13) DNF를 사용하여 모든 시스템 패키지 업데이트
다음 명령을 사용하여 설치된 모든 패키지를 포함한 전체 시스템을 업데이트 할 수 있다.

```bash
[root@localhost ~]# dnf update
OR
[root@localhost ~]# dnf upgrade
```

![img013](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img013.gif)

### 14) DNF를 사용하여 패키지 제거/지우기
원하지 않는 패키지 (예: nano)를 제거하거나 지우려면 dnf 명령과 함께 _remove_ 또는 _erase_ 를 사용하여 제거 할 수 있다.

```bash
[root@localhost ~]# dnf remove nano
OR
[root@localhost ~]# dnf erase nano
```

![img014](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img014.gif)

### 15) DNF를 사용하여 Orphan 패키지 제거
종속성을 충족시키기 위해 설치된 패키지는 다른 응용 프로그램에서 사용하지 않으면 쓸모가 없을 수 있다. 이러한 Orphan 패키지를 제거하려면 아래 명령을 실행하면 된다.

```bash
[root@localhost ~]# dnf autoremove
```

![img015](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img015.gif)

### 16) DNF를 사용하여 캐시 된 패키지 제거
많은 시간 동안 dnf를 실행하는 동안 오류가 발생하는 오래된 헤더와 완료되지 않은 트랜잭션이 발생 한다. 간단히 실행하여 원격 패키지 정보를 포함하는 모든 캐시된 패키지와 헤더를 정리할 수 있다.

```bash
[root@localhost ~]# dnf clean all
```

![img016](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img016.gif)

### 17) 특정 DNF 명령에 대한 도움말보기
아래 명령을 실행하면 특정 dnf 명령 (예: clean)에 대한 도움을 받을 수 있다.

```bash
[root@localhost ~]# dnf help clean
```

![img017](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img017.gif)

### 18) 모든 DNF 명령 및 옵션 나열
사용 가능한 모든 dnf 명령 및 옵션에 대한 도움말을 나열

```bash
[root@localhost ~]# dnf help
```

![img018](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img018.gif)

### 19) DNF 내역보기
dnf history를 호출하여 이미 실행 된 dnf 명령 목록을 볼 수 있다. 이렇게 하면 타임 스탬프로 무엇이 설치/제거되었는지 알 수 있다.

```bash
[root@localhost ~]# dnf history
```

![img019](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img019.gif)

### 20) 모든 그룹 패키지 나열
_dnf grouplist_ 명령은 사용 가능하거나 설치된 모든 패키지를 보여준다. 아무 것도 언급되지 않으면 알려진 모든 그룹을 나열한다.

```bash
[root@localhost ~]# dnf grouplist
```

![img020](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img020.gif)

### 21) DNF를 사용하여 그룹 패키지 설치
그룹 패키지 (예: Educational Software)로 함께 번들 된 패키지 그룹을 간단히 설치하려면 다음과 같이한다.

```bash
[root@localhost ~]# dnf groupinstall 'Educational Software'
```

![img021](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img021.gif)

### 22) 그룹 패키지 업데이트
아래 명령을 실행하여 그룹 패키지 (예: Educational Software)를 업데이트를 한다.

```bash
[root@localhost ~]# dnf groupupdate 'Educational Software'
```

![img022](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img022.gif)

### 23) 그룹 패키지 제거
그룹 패키지 (예: Educational Software)를 다음과 같이 제거 할 수 있다.

```bash
[root@localhost ~]# dnf groupremove 'Educational Software'
```

![img023](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img023.gif)

### 24) 특정 저장소에서 패키지 설치
DNF를 사용하면 저장소 (epel)에서 특정 패키지 (예: phpmyadmin)를 다음과 같이 간단하게 설치할 수 있다.

```bash
[root@localhost ~]# dnf --enablerepo=epel install phpmyadmin
```

![img024](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img024.gif)

### 25) 설치된 패키지를 안정적인 릴리스로 동기화
_dnf distro-sync_ 명령은 설치된 모든 패키지를 활성화된 저장소에서 사용할 수 있는 가장 최근의 안정적인 버전으로 동기화하는데 필요한 옵션을 제공한다. 패키지를 선택하지 않으면 설치된 모든 패키지가 동기화된다.

```bash
[root@localhost ~]# dnf distro-sync
```

![img025](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img025.gif)

### 26) 패키지 재설치
_dnf reinstall nano_ 명령은 이미 설치된 패키지 (예: nano)를 다시 설치한다.

```bash
[root@localhost ~]# dnf reinstall nano
```

![img026](/assets/img/2020-11-03-dnf-commands-for-rpm-package-management/img026.gif)

## [출처 및 참고]
* <https://www.tecmint.com/dnf-commands-for-fedora-rpm-package-management/>
