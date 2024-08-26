---
title: Ubuntu apt와 apt-get의 차이점
author: dejavuhyo
date: 2024-08-26 09:42:00 +0900
categories: [DevOps, Linux]
tags: [ubuntu-apt, ubuntu-apt-get, apt, apt-get, apt-apt-get, apt-apt-get-차이점]
---

## 1. 차이점
apt와 apt-get은 모두 명령줄 도구이다. 이를 사용하여 Debian 기반 Linux 서버 및 서버 인스턴스에서 애플리케이션 및 라이브러리와 같은 소프트웨어 패키지를 관리할 수 있다.

Linux는 오픈 소스이므로 요구 사항에 맞게 사용자 지정할 수 있는 여러 패키지 옵션이 있다. Debian 기반 Linux 운영 체제는 고급 패키지 도구(APT)를 사용하여 Linux 소프트웨어 패키지를 관리한다. apt 명령줄 도구는 직관적인 명령어, 예상된 동작 및 보안 기능으로 최종 사용자에게 더 높은 수준의 사용자 인터페이스를 제공한다.

반면 apt-get 명령어는 핵심 Linux 프로세스와 더 긴밀하게 통신하는 낮은 수준의 인터페이스이다. apt 명령어는 apt-get보다 사용자 친화적인 패키지 관리자이다.

## 2. 유사점
apt와 apt-get은 모두 Debian 기반 Linux 배포(예: Debian, Ubuntu, Linux Mint 및 elementary OS)의 패키지 관리자 도구이다. aptitude 및 synaptic과 같은 유사한 도구가 있지만 기본값은 apt-get과 apt이다.

apt와 apt-get의 유사점이다.

### 1) 인터페이스
apt 명령줄 유틸리티 도구와 apt-get 명령줄 유틸리티 도구는 Linux 명령줄 인터페이스(CLI)를 통해 액세스 가능하다. Linux CLI를 사용하려면 터미널 창 또는 가상 터미널 창을 연다.

### 2) 설명서
apt 유틸리티와 apt-get 유틸리티의 공식 개요를 보려면 명령 프롬프트에서 apt 또는 apt-get을 입력한다. 이렇게 하면 가장 일반적인 apt 명령과 apt-get 명령 목록이 나온다.

예를 들어 apt update 또는 apt-get update는 공식 리포지토리에서 사용 가능한 소프트웨어 패키지 목록을 업데이트한다. apt 명령과 apt-get 명령 뒤에는 옵션도 나열된다. 예를 들어 apt update --quiet는 apt update 명령의 덜 상세한 출력 결과를 터미널에 제공한다.

관리자로 로그인하지 않은 경우에는 먼저 sudo 명령을 각 apt 명령 및 apt-get 명령에 추가해야 한다. 예를 들어 sudo apt-get update와 같이 입력한다.

### 3) 사용 사례
apt와 apt-get에서 가장 일반적인 태스크는 소프트웨어 패키지를 설치, 업데이트 및 제거하는 것이다. 소프트웨어 패키지로는 애플리케이션, 라이브러리, 유틸리티 및 도구와 같은 것이 있을 수 있다.

다음 표는 이 둘의 일반적인 명령 구문이다.

| apt | apt-get |
|:-----:|:-----:|
| apt install <package_name> | apt-get install <package_name> |
| apt remove <package_name> | apt-get remove <package_name> |
| apt purge <package_name> | apt-get purge <package_name> |
| apt upgrade <package_name> | apt-get upgrade <package_name> |
| apt update | apt-get update |

## 3. 주요 차이점
apt와 apt-get의 차이점은 apt가 apt-get의 최신 버전이라는 점 외에도 많다. apt 명령은 apt-get보다 사용자 친화적인 대안으로 설계된 것으로, 사용자 편의를 위해 여러 패키지 관리 도구의 기능을 결합한 것이다.

다음으로, apt와 apt-get의 다른 차이점이다.

### 1) 릴리스 날짜
apt-get은 1998년에 Debian 2.0(Hamm) 배포로 릴리스되었고 apt는 Debian 8(Jessie) 배포로 2014년에 릴리스되었다. 2014년 이후 apt는 apt-get를 대체하여 모든 Debian 기반 Linux 배포의 기본 패키지 관리자 도구가 되었다.

### 2) 검색 기능
`apt search <package_name>` 명령을 사용하여 이름으로 패키지를 검색한다. 이 명령은 검색된 package_name을 포함하는 모든 패키지에 대한 자세한 설명을 제공한다. apt-get으로는 이 작업을 수행할 수 없었다. 대신 apt-cache 명령을 사용해야 했다.

### 3) 종속성 확인
모든 소프트웨어 패키지에는 일반적으로 올바른 작동에 필요한 라이브러리 또는 도구와 같은 소프트웨어 종속성 목록이 함께 제공된다. 패키지를 설치하려면 먼저 모든 종속성을 설치해야 한다.

apt와 apt-get 모두 종속성 확인을 처리하지만 apt가 훨씬 우수하다. 패키지를 올바른 순서로 설치하는 복잡한 종속성 체인을 확인하고 제안된 패키지를 설치하도록 권장한다.

### 4) 파일 시스템의 패키지 버전
기본적으로 apt upgrade 명령은 업그레이드 시 시스템에 설치된 이전 버전의 패키지 또는 업그레이드 가능한 패키지 중에서 더 이상 필요하지 않은 패키지를 제거한다.

반면 apt-get upgrade 명령은 그렇지 않다. 이러한 효율성 덕에 apt upgrade는 시스템 메모리를 확보하는데 있어서 더 우수하다.

### 5) 터미널에 출력되는 결과
apt-get과 apt 모두 입력 명령 후 시스템의 상태에 대한 인사이트를 제공하는 상태 정보를 터미널에 출력한다. apt 명령은 각 작업에 대한 진행률 표시줄을 포함하여 사용자에게 조금 더 자세한 정보를 제공한다.

## 4. apt가 apt-get 대체 방법
apt 도구는 Debian 기반 Linux 배포의 기본 패키지 관리 유틸리티이며 apt-get과 apt-cache 모두를 대체합니다. 대부분의 apt-get 명령은 새로운 각 apt 명령에서 동일하게 작성됩니다.

그러나 과거에는 특정 명령에서 apt-get을 apt-cache와 함께 사용해야 했습니다. 다음 표는 이 몇 가지 명령이 apt로 포트되는 방식을 보여줍니다.

| apt-cache | apt-get | apt |
|:-----:|:-----:|:-----:|
| apt-cache search | 지원되지 않음 | apt search |
| apt-cache show | 지원되지 않음 | apt show |
| apt-cache policy | 지원되지 않음 | apt policy |

기존 Linux 스크립트의 apt-get 명령을 apt 명령으로 바꾸지 않아도 된다. 여전히 예상대로 작동하며 apt-get이 계속 지원된다. 이전 apt-get 명령의 일부 기능은 apt에서 약간만 변경되었기 때문에 apt-get을 스크립트에 유지하면 올바른 작동을 보장하는데 도움이 된다.

평균적인 Linux 사용자는 프로젝트나 환경에서 필요하지 않은 한 apt-get 대신 apt를 사용해야 한다.

## 5. 차이점 요약

| 범주 | apt-get | apt |
|:-----:|:-----:|:-----:|
| 무엇인가 | Debian 기반 Linux 시스템에서 소프트웨어 패키지를 관리하기 위한 CLI 도구이다. | Debian 기반 Linux 시스템에서 소프트웨어 패키지를 관리하기 위한 CLI 도구이다. |
| 릴리스 연도 및 초기 Debian 배포 | 1998, Debian 2.0(Hamm) 배포 | 2014, Debian 8(Jessie) 배포 |
| 검색 기능 | 아니요. 사용자는 검색 대신 apt-cache 패키지 관리 명령을 사용해야 한다. | 예 |
| 종속성 확인 | 종속성 확인이 간단히다. | 종속성 확인이 복잡하며 제안된 소프트웨어를 설치해야 한다. |
| 파일 시스템의 패키지 버전 | apt-get upgrade를 사용하면 패키지의 이전 버전이 파일 시스템에 남는다. | apt upgrade를 사용하면 패키지의 이전 버전이 파일 시스템에서 삭제된다. |
| 작업의 출력 결과 | 각 apt-get 명령의 기본적인 결과가 세부 정보 없이 출력된다. | 각 apt 명령의 상세한 결과가 출력되며 태스크 진행률 표시줄을 포함한 더 많은 작업 정보가 제공된다. |

## [출처 및 참고]
* [https://aws.amazon.com/ko/compare/the-difference-between-apt-and-apt-get/](https://aws.amazon.com/ko/compare/the-difference-between-apt-and-apt-get/)
