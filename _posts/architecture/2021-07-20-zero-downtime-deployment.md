---
title: 무중단 배포 아키텍처
author: dejavuhyo
date: 2021-07-20 06:00:00 +0900
categories: [DevOps, Architecture]
tags: [zero-downtime-deployment, rolling-deployment, blue-green-deployment, canary-deployment, 무중단-배포, 롤링-배포, 블루-그린-배포, 카나리-배포]
---

## 1. 소프트웨어 개발과 배포
개발 결과물을 고객에게 제공하기 위해서는 서버에 배포해야 한다. 최신 애플리케이션들은 클라우드 기반으로 구성되어 트래픽에 따라 탄력적인 확장과 고가용성을 보장하지만 배포 시 서비스를 멈춰야 하는 중단 배포 방식의 경우 다운타임(Downtime)이 발생한다.

물론 특정 애플리케이션의 경우 계획적으로 다운타임을 허용할 수 있지만 전 세계 사용자를 대상으로 하거나 24시간 운영이 필요한 서비스는 항상 가동되어야 하는 경우가 많다.

일반적으로 중단 배포 방식은 더 나은 서비스를 제공하기 위해 오히려 고객의 불편과 손실을 야기할 수 있다. 새로운 배포로 인해 문제가 발생하는 경우도 배제할 수 없기 때문에 운영자 입장에서 부담은 늘어나게 된다.

반면 무중단 배포 방식은 서비스와 고객에게 부정적인 영향을 최소화하는 좋은 대안이 될 수 있다. 안정적인 배포 체계를 갖추는 것은 다양한 비즈니스 요구사항에도 개발에 집중할 수 있게 만들며 이는 곧 서비스의 경쟁력이 된다.

## 2. 무중단 배포
무중단 배포는 서비스 장애와 배포의 부담을 최소화하기 위해 운영 중인 서비스를 중단하지 않고 신규 소프트웨어를 배포하는 기술이다.

무중단 배포의 핵심은 로드밸런서(Load Balancer)를 통해 연결된 두 개 이상의 (서로 다른 IP, 포트를 가진) 인스턴스에 트래픽을 제어해 배포하는 것이다.

배포 작업이 서비스에 영향을 주지 않도록 하기 위해 고객의 이용량에 따라 인스턴스는 물론 로드밸런서도 다중화를 고려해야 한다. 즉, 무중단 배포를 하기 위해서는 고가용성의 시스템 인프라가 구성되어 있어야 한다.

![zero-downtime-deployment](/assets/img/2021-07-20-zero-downtime-deployment/zero-downtime-deployment.png)

무중단 배포는 크게 두 종류로 나뉜다. 제한된 자원에서 하나씩 배포하여 변경해 나가는 롤링 배포 방식과 현재 사용 중인 버전의 인스턴스 수만큼 새 버전의 인스턴스를 준비해 로드밸런서가 스위칭해주는 블루-그린 배포 방식 여기에 더해 새 버전 소프트웨어의 모니터링과 검증에 초점을 맞춘 카나리 배포 방식이다.

### 1) 롤링 배포(Rolling Deployment)
롤링 배포는 사용 중인 인스턴스 내에서 새 버전을 점진적으로 교체하는 것으로 무중단 배포의 가장 기본적인 방식이다.

서비스 중인 인스턴스 하나를 로드밸런서에서 라우팅하지 않도록 한 뒤, 새 버전을 적용하여 다시 라우팅하도록 한다. 이를 반복하여 모든 인스턴스에 새 버전의 애플리케이션을 배포한다.

인스턴스마다 차례로 배포를 진행하기 때문에 상황에 따라 손쉽게 롤백(Roll Back)이 가능한 장점이 있다.

롤링 배포 방식은 가용 자원(인스턴스)이 제한적일 경우에 사용되며 새 버전을 배포할 때 인스턴스 수가 감소하기 때문에 서비스 처리 용량을 고려해야 한다. 또한 배포가 진행되는 동안 구버전과 신버전이 공존하기 때문에 호환성 문제가 발생할 수 있다.

![rolling-deployment](/assets/img/2021-07-20-zero-downtime-deployment/rolling-deployment.png)

### 2) 블루-그린 배포(Blue-Green Deployment)
블루를 구버전, 그린을 신버전으로 지칭하여 붙여진 이름으로 운영 환경에 구버전과 동일하게 신버전의 인스턴스를 구성한 후, 로드밸런서를 통해 신버전으로 모든 트래픽을 전환하는 배포 방식이다.

구버전과 동일한 운영 환경으로 신버전의 인스턴스를 구성하기 때문에 실제 서비스 환경에서 신버전을 미리 테스트 할 수 있는 장점이 있고 롤링 배포와 마찬가지로 빠른 롤백이 가능하다.

![blue-green-deployment-blue](/assets/img/2021-07-20-zero-downtime-deployment/blue-green-deployment-blue.png)

배포가 완료된 후 남아 있는 기존 버전의 환경을 다음 배포에 재사용할 수 있다. 하지만 블루-그린 배포를 위해서는 시스템 자원이 두 배로 필요하며 새로운 환경에 대한 테스트가 전제되어야 한다.

![blue-green-deployment-green](/assets/img/2021-07-20-zero-downtime-deployment/blue-green-deployment-green.png)

### 3) 카나리 배포(Canary Deployment)
옛날 광부들이 유독 가스에 민감한 카나리아 새를 이용해 가스 누출 위험을 감지했던 것에서 유래한 것으로 잠재적 문제 상황을 미리 발견하기 위한 방식이다.

신버전의 제공 범위를 늘려가면서 모니터링 및 피드백 과정을 거칠 수 있다. 로드밸런서를 통해 신버전의 제품을 경험하는 사용자를 조절할 수 있는 것이 특징으로 신버전을 특정 사용자(예: 모바일 이용자) 혹은 단순 비율에 따라 구분해 제공할 수 있다.

이 방식은 신버전의 배포 전에 실제 운영 환경에서 미리 테스트한다는 점이 블루-그린 배포와 비슷하다. 하지만 카나리 배포는 단계적인 전환 방식을 통해 부정적 영향을 최소화하고 상황에 따라 트래픽 양을 늘리거나 롤백할 수 있다. 단, 롤링 배포와 마찬가지로 신·구 두 버전이 운영되기 때문에 버전 관리가 필요하다.

![canary-deployment](/assets/img/2021-07-20-zero-downtime-deployment/canary-deployment.png)

## [출처 및 참고]
* [https://www.samsungsds.com/kr/insights/1256264_4627.html?moreCnt=1&backTypeId=insight&category=all](https://www.samsungsds.com/kr/insights/1256264_4627.html?moreCnt=1&backTypeId=insight&category=all)
* [https://dev.to/mostlyjason/intro-to-deployment-strategies-blue-green-canary-and-more-3a3](https://dev.to/mostlyjason/intro-to-deployment-strategies-blue-green-canary-and-more-3a3)
