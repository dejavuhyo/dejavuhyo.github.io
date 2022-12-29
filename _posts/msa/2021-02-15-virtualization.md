---
title: 가상화
author: dejavuhyo
date: 2021-02-15 06:30:00 +0900
categories: [DevOps, MSA]
tags: [virtualization, 가상화, 가상화-기술, 서버-가상화, 가상화-종류, 운영체제-가상화, 데스크탑-가상화, 네트워크-가상화, 데이터-가상화]
---

## 1. 가상화(Virtualization)란
가상화 기술이란 하드웨어에 종속된 컴퓨터 리소스를 추상화하여 서버, 스토리지, 네트워크 등의 소프트웨어 IT 서비스를 생성하는 솔루션을 뜻한다. 가상화를 사용하면 물리적 머신의 기능을 여러 사용자 또는 환경에 배포해 물리적 머신을 최대한 활용할 수 있다.

구체적으로, 각각 용도가 다른 3개의 물리 서버가 있다고 가정해 보자. 하나는 메일 서버이고, 다른 하나는 웹 서버이고, 나머지 하나는 내부 레거시 애플리케이션을 실행하는 서버이다. 각 서버는 잠재적인 실행 용량의 일부에 불과한 30% 용량만 사용되고 있다. 그러나 내부 운영을 위해서는 레거시 애플리케이션이 계속 필요하므로 레거시 애플리케이션과 이를 호스팅하는 세 번째 서버를 유지해야 한다.

![server-usage](/assets/img/2021-02-15-virtualization/server-usage.png)

전통적으로는 위의 이론을 따른다. 1개의 서버와 1개의 운영 체제, 1개의 태스크와 같이 개별 서버에서 개별 태스크를 실행하는 것이 더 쉽고 안정적인 경우가 많다. 1개의 서버로 여러 개의 태스크를 처리하기란 쉽지 않았다. 그러나 가상화를 사용하면 메일 서버를 2개의 고유한 서버로 분할해 독립적인 태스크를 처리하고 레거시 애플리케이션을 마이그레이션할 수 있다. 마찬가지로 하드웨어도 더 효율적으로 사용할 수 있다.

![server-usage-for-virtualization](/assets/img/2021-02-15-virtualization/server-usage-for-virtualization.png)

보안을 고려하여 첫 번째 서버를 다시 분할해 다른 태스크를 처리하면 사용률을 30%에서 60% 또는 90%까지도 높일 수 있다. 이렇게 하고 나면 이제 빈 서버를 재사용해 다른 태스크를 처리하거나 모든 서버를 사용 중지해 냉각 및 유지관리 비용을 줄일 수 있다.

## 2. 가상화 기술의 변화 과정
가상화 기술은 1960년대부터 시작되었으나 널리 도입된 것은 2000년대 초이다. 하이퍼바이저와 같은 가상화 지원 기술이 수십 년 전에 개발되어 일괄 처리를 수행하는 컴퓨터에 여러 명의 사용자가 동시에 액세스할 수 있게 되었다. 일괄 처리는 급여 처리와 같은 일상적인 태스크를 빠르게 수천 번 반복해야 하는 사업 부문에서 널리 사용되는 컴퓨팅 방식이었다.

그러나 이후 몇십 년간 단일 머신에서 여러 사용자가 작업할 수 있게 하는 솔루션은 점차 인기를 얻은 데 비해, 가상화는 빛을 보지 못했다. 즉, 시분할 (time-sharing) 시스템 솔루션으로 인해 운영 체제 내에서 격리된 사용자들은 UNIX와 같은 운영 체제로 옮겨가게 되었고 결국 Linux가 주류로 떠올랐다. 그러는 동안 가상화는 대중적인 인기를 얻지 못하는 틈새 기술로 남아 있었다.

이제 시간을 뛰어넘어 1990년대로 가 보겠다. 대부분의 기업이 물리 서버와 단일 벤더 IT 스택을 사용하고 있었기 때문에 다른 벤더의 하드웨어에서 레거시 애플리케이션을 실행할 수 없었다. 다양한 벤더가 제공하는 저렴한 상용 서버, 운영 체제, 애플리케이션으로 IT 환경을 업데이트하면서 기업은 사용률이 낮은 물리 하드웨어에 종속되었으며 각 서버에서 벤더별 태스크를 1개만 실행할 수 있었다.

가상화는 이러한 문제를 해결할 수 있었다. 이 2가지 문제에 대해 적절한 해결책을 제시하는 가상화를 통해 기업은 서버를 파티셔닝하고 여러 유형 및 버전의 운영 체제에서 레거시 애플리케이션을 실행할 수 있게 되었습니다. 서버를 더 효율적으로 사용하거나 아예 사용하지 않아도 되었으므로 구매, 셋업, 냉각, 유지관리와 관련된 비용이 줄어들었다.

또한 가상화는 광범위하게 적용할 수 있어 벤더 종속을 줄이고 클라우드 컴퓨팅의 기반을 다지는 데 도움이 되었다. 오늘날에는 모든 사항을 기록하는 데 전문적인 가상화 관리 소프트웨어를 필요로 하는 여러 기업에서 일반적으로 가상화를 활용하고 있다.

## 3. 가상화 기술 및 서버 가상화
하이퍼바이저라는 소프트웨어가 물리 리소스를 필요로 하는 가상 환경으로부터 물리 리소스를 분리한다. 하이퍼바이저는 노트북 등의 운영 체제에 배포하거나 서버 등의 하드웨어에 직접 설치할 수 있으며 대부분의 기업들은 이러한 방식으로 가상화한다. 하이퍼바이저가 물리 리소스를 분할하여 가상 환경에서 사용할 수 있도록 한다.

리소스는 필요에 따라 물리 환경에서 여러 가상 환경으로 파티셔닝된다. 사용자가 가상 환경(일반적으로 게스트 머신 또는 가상 머신이라고 함)과 상호 작용하고 가상 환경 내에서 계산을 실행한다. 가상 머신은 단일 데이터 파일과 같이 기능한다. 디지털 파일과 같이 한 컴퓨터에서 다른 컴퓨터로 이동할 수 있고 어느 쪽 컴퓨터에서든 열어 동일하게 작동할 수 있다.

가상 환경이 실행 중이고 사용자 또는 프로그램이 물리 환경에서 추가 리소스를 요구하는 명령을 내리면 하이퍼바이저가 그 요청을 물리 시스템에 전달하고 변경사항을 캐시한다. 특히 요청이 커널 기반 가상 머신(KVM)에서 오픈소스 하이버파이저를 통해 전송되는 경우, 이 모든 작업은 네이티브 속도와 비슷하게 이루어진다.

![how-virtualization-works](/assets/img/2021-02-15-virtualization/how-virtualization-works.png)

## 4. 가상화 종류

### 1) 데이터 가상화
여러 곳에 분산되어 있는 데이터를 단일 소스로 통합할 수 있다. 데이터 가상화를 통해 기업은 데이터를 동적 공급 요소로 다루어 여러 소스에서 데이터를 동시에 가져오고 새로운 데이터 소스를 손쉽게 통합하며 사용자의 요구에 따라 데이터를 변환할 수 있는 처리 역량을 확보할 수 있다. 데이터 가상화 툴은 여러 데이터 소스를 대표하여 이와 같이 다양한 데이터 소스가 단일 소스로 처리될 수 있도록 함으로써 필요한 데이터를 필요한 형식으로 적시에 애플리케이션 또는 사용자에게 제공한다.

![data-virtualization](/assets/img/2021-02-15-virtualization/data-virtualization.png)

### 2) 데스크탑 가상화
데스크탑 가상화는 단일 머신에서 여러 운영 체제를 배포할 수 있는 운영 체제 가상화와 혼동하기 쉬우며, 시뮬레이션된 데스크탑 환경이 중앙 관리자 또는 자동화된 관리 툴을 통해 수백 개의 물리 머신에 동시 배포되도록 지원한다. 각 머신에서 물리적으로 설치, 설정, 업데이트되는 기존의 데스크탑 환경과 달리 데스크탑 가상화는 관리자가 모든 가상 데스크탑에서 설정, 업데이트, 보안 점검을 대규모로 수행할 수 있다.

![desktop-virtualization](/assets/img/2021-02-15-virtualization/desktop-virtualization.png)

### 3) 서버 가상화
서버는 대량의 특정 태스크를 매우 효과적으로 처리해 노트북 및 데스크탑 등의 다른 컴퓨터가 다양한 태스크를 처리할 수 있도록 하는 컴퓨터이다. 서버를 가상화하면 서버가 이러한 특정 기능을 더 많이 수행할 수 있으며 서버 파티셔닝을 통해 구성 요소로 여러 기능을 지원할 수 있다.

![server-virtualization](/assets/img/2021-02-15-virtualization/server-virtualization.png)

### 4) 운영 체제 가상화
운영 체제 가상화는 운영 체제의 중앙 태스크 관리자인 커널에서 이루어진다. 이렇게 하면 Linux 환경과 Windows 환경을 함께 실행할 수 있다. 또한 기업은 가상 운영 체제를 컴퓨터에 푸시해 다음과 같은 이점을 얻을 수 있다.

* 컴퓨터에 고도의 OOTB(Out Of The Box) 기능이 필요하지 않으므로 하드웨어에 많은 비용이 소모되지 않는다.

* 모든 가상 인스턴스를 모니터링하고 격리할 수 있으므로 보안이 강화된다.

* 소프트웨어 업데이트와 같은 IT 서비스에 소요되는 시간이 절약된다.

![operating-system-virtualization](/assets/img/2021-02-15-virtualization/operating-system-virtualization.png)

### 5) 네트워크 기능 가상화
네트워크 기능 가상화(Network Functions Virtualization, NFV)는 디렉터리 서비스, 파일 공유, IP 설정과 같은 네트워크의 주요 기능을 분리하여 이러한 기능을 환경에 배포한다. 소프트웨어 기능이 속해 있는 물리 머신으로부터 기능을 분리하면 특정 기능을 새 네트워크에 함께 패키징하고 이를 환경에 할당할 수 있다. 네트워크를 가상화하면 스위치, 라우터, 서버, 케이블, 허브 등 여러 개의 독립적인 네트워크를 생성하는 데 필요하며 특히 통신 산업에서 일반적으로 사용되는 물리 구성 요소의 수가 줄어든다.

![network-function-virtualization](/assets/img/2021-02-15-virtualization/network-function-virtualization.png)

## [출처 및 참고]
* <https://www.redhat.com/ko/topics/virtualization/what-is-virtualization>