---
title: Linux 컨트롤 그룹 (Cgroups)
author: dejavuhyo
date: 2022-11-02 10:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-cgroup, cgroup, linux-control-group, linux-kernel, kernel, 리눅스-cgroup, 리눅스-control-group, 리눅스-커널, 커널]
---

## 1. 컨트롤 그룹 (Cgroups) 이란
cgroups(control groups)는 프로세스들의 자원의 사용(CPU, 메모리, 디스크 입출력, 네트워크 등)을 제한하고 격리시키는 리눅스 커널 기능이다.

cgroup을 통해 사용자는 CPU 시간, 시스템 메모리, 네트워크 대역폭과 같은 자원이나 이러한 자원의 조합을 시스템에서 실행 중인 사용자 정의 작업 그룹 (프로세스) 간에 할당할 수 있다. 또한 설정한 cgroup을 모니터링하거나 특정 자원으로의 cgroup 액세스를 거부하는 것 이외에 실행 중인 시스템에서 cgroup을 동적으로 다시 구성할 수 있다.

cgconfig (control group config) 서비스는 부팅시 시작 및 사전 정의된 cgroup을 다시 구성하도록 설정하여 재부팅 후에도 구성된 사항이 지속되도록 설정할 수 있다.

cgroup을 사용하여 시스템 관리자는 시스템 자원 할당, 우선 순위 지정, 거부, 관리, 모니터링과 같은 세밀한 제어가 가능하다. 하드웨어 자원은 작업 및 사용자 간을 신속하게 분배하여 전체적인 효율성을 향상시킬 수 있다.

## 2. 컨트롤 그룹의 구성 방법
프로세스와 마찬가지로 cgroup은 계층적으로 구성되어 있으며 하위 cgroup 부모 cgroup 속성의 일부를 상속하도록되어 있다. 그러나 이 두 모델 사이에는 차이점이 있다.

### 1) Linux 프로세스 모델
Linux 시스템의 모든 프로세스는 일반적인 부모 프로세스의 자식 프로세스이다. init 프로세스는 부팅시 커널에 의해 실행되어 다른 프로세스를 시작한다 (그 결과 자식의 자식 프로세스를 시작하는 경우도 있다).

모든 프로세스는 하나의 부모 프로세스의 하위 프로세스이기 때문에 Linux 프로세스 모델은 단일 계층 또는 트리로 되어 있다. 또한 init을 제외한 모든 Linux 프로세스는 환경 (예: PATH 변수) 및 부모 프로세스의 기타 다른 속성 (예: 파일 열기 설명자)을 상속한다.

### 2) Cgroup 모델
Cgroup은 다음과 같은 점에서 프로세스와 유사하다.

* 계층적이다.

* 자식 cgroup은 부모 cgroup에서 특정 속성을 상속한다.

근본적인 차이점은 여러 다른 cgroup 계층이 시스템에 동시에 존재할 수 있다는 것이다. Linux 프로세스 모델이 단일 프로세스 트리일 경우, cgroup 모델은 하나의 또는 여러 다른 연결되지 않은 작업 트리 (즉 프로세스)라는 것이다.

각 계층에는 하나 이상의 서브시스템에 연결되므로 cgroup에 대해 여러 분리된 계층이 필요하다. 서브 시스템은 CPU 시간 또는 메모리와 같은 단일 자원을 말한다.

### 3) Linux에서 사용 가능한 서브시스템

* `blkio`: 이 서브시스템은 물리 드라이브 (예: 디스크, 솔리드 스테이트, USB 등)와 같은 블록 장치에 대한 입력/출력 액세스에 제한을 설정한다.

* `cpu`: 이 서브시스템은 CPU에 cgroup 작업 액세스를 제공하기 위해 스케줄러를 사용한다.

* `cpuacct`: 이 하위 시스템은 cgroup의 작업에 사용된 CPU 자원에 대한 보고서를 자동으로 생성한다.

* `cpuset`: 이 서브시스템은 개별 CPU (멀티코어 시스템에서) 및 메모리 노드를 cgroup의 작업에 할당한다.

* `devices`: 이 서브시스템은 cgroup의 작업 단위로 장치에 대한 액세스를 허용하거나 거부한다.

* `freezer`: 이 서브시스템은 cgroup의 작업을 일시 중지하거나 다시 시작한다.

* `memory`: 이 서브시스템은 cgroup의 작업에서 사용되는 메모리에 대한 제한을 설정하고 이러한 작업에서 사용되는 메모리 자원에 대한 보고서를 자동으로 생성한다.

* `net_cls`: 이 서브시스템은 Linux 트래픽 컨트롤러 (tc)가 특정 cgroup 작업에서 발생하는 패킷을 식별하게 하는 클래식 식별자 (classid)를 사용하여 네트워크 패킷에 태그를 지정한다.

* `ns`: namespace 서브시스템

## [출처 및 참고]
* [https://access.redhat.com/documentation/ko-kr/red_hat_enterprise_linux/6/html/resource_management_guide/ch01](https://access.redhat.com/documentation/ko-kr/red_hat_enterprise_linux/6/html/resource_management_guide/ch01)
