---
title: GitLab과 GitHub 지속적 통합(CI) 비교
author: dejavuhyo
date: 2022-04-18 16:00:00 +0900
categories: [DevOps, Git]
tags: [github-vs-gitlab, github, gitlab, continuous-integration-comparison, ci-comparison, github-ci, gitlab-ci, github-cd, gitlab-cd, github-gitlab-비교, github-gitlab-지속적-통합]
---

## 1. 일반적인 기술 의사 결정권자(TDM) 문제에 대한 GitLab vs. GitHub 솔루션

| TDM Problem | The DevOps Solution | GitLab | GitHub |
|-----|-----|-----|-----|
| 코드 빌드 및 테스트 시간 단축, 복잡성 감소 | 파이프라인 오케스트레이션 | 💚 부모-자식 파이프라인이 동시에 실행된다. 구성은 더 작은 하위 파이프라인으로 구분할 수 있다. | 💔 ⚠ 파이프라인 오케스트레이션이 없으므로 파이프라인 실행 시간이 길어진다. |
| 코드 업데이트 오류 및 충돌을 제거한다. | 병합 오케스트레이션 | 💚 고급 병합 트레인 처리 로직은 주요 개발 분기를 녹색으로 유지한다. | 💔 ⚠ 고급 병합 트레인 처리 로직이 없으므로 더 많은 코드 커밋 충돌이 발생한다. |
| 초기 구성 시간과 학습 곡선을 줄인다. | 사전 구성된 파이프라인 | 💚 Auto DevOps는 즉시 사용 가능한 파이프라인 구성을 제공한다. | 💔 ⚠ 미리 구성된 파이프라인이 없으므로 파이프라인 설정 시간이 더 길어진다. |

💚완전지원 💛일부지원 💔지원없음

## 2. GitHub에서 누락된 GitLab CI 기능

| GitLab 기능 | 특징 |
|-----|-----|
| 내장 CI | 플러그인이나 타사 구성 요소가 없다. |
| 새 팀원을 위한 DevOps 프로세스 간소화 | AutoDevOps는 코드를 인식하고 엔드 투 엔드 devops 템플릿을 자동으로 설정한다. |
| 결함을 줄이고 개발 시간을 단축하기 위해 병합 전에 앱 미리보기 | [리뷰 앱](https://docs.gitlab.com/ee/ci/review_apps/)으로 변경 사항을 미리 본다. 리뷰 앱을 위한 환경 Autostop |
| 크고 복잡한 프로젝트 및 팀 모델링 및 관리 | 대규모 프로젝트를 관리하기 위한 그룹 내의 하위 그룹, 그룹 파일 템플릿(표준화를 추진하기 위한 그룹 수준의 템플릿), 그룹을 쉽게 찾을 수 있는 탐색의 그룹 드롭다운 |

## 3. GitHub 작업 격차
다음 표는 GitHub Actions의 주요 차이 및 관련 영향이 나열되어 있다. 이 정보는 GitHub Actions Community Group의 사용자 피드백에서 파생되었으며 2020년 4월 10일에 채택되었다.

### 1) 지속적인 통합

* 운영 비용을 증가시키는 문제

| 설명 | 영향 | 참조 |
|-----|-----|-----|
| 리포지토리가 동일한 조직 내에 있는 경우에도 다른 비공개 리포지토리에서 작업에 액세스할 수 없다. | 코드를 재사용하기 어렵다. 이 제한을 극복하기 위해 해결 방법을 구현하기가 어렵다. | [GitHub 커뮤니티 토론](https://github.community/t/github-action-action-in-private-repository/16063) |
| UI에서 개별 워크플로 실행을 삭제할 수 없다. | 실패한 워크플로 실행이 지속되면 혼란이 가중되고 관리 효율성이 떨어진다. | [GitHub 커뮤니티 토론](https://github.community/t/deletion-of-individual-workflow-runs/17082) |

### 2) 지속적 전달

* 배송 시간을 늘리는 문제

| 설명 | 영향 | 참조 |
|-----|-----|-----|
| 워크플로에서 단일 작업만 다시 실행할 수 없음 | 예를 들어, 실패한 테스트 중 일부만 다시 실행할 수 없다. 사용자는 전체 워크플로를 다시 실행해야 하므로 시간 낭비/생산성 손실이 발생한다. 10개의 작업이 있는 워크플로에서는 10개를 모두 다시 실행해야 한다. | [GitHub 커뮤니티 토론](https://github.community/t/ability-to-rerun-just-a-single-job-in-a-workflow/17234) |

* 운영 비용을 증가시키는 문제

| 설명 | 영향 | 참조 |
|-----|-----|-----|
| 스테이징 및 프로덕션 환경에 대해 서로 다른 워크플로를 쉽게 트리거할 수 없음 | 조건부 워크플로는 GitHub 작업 이벤트에 연결된 유형이 없기 때문에 구현 및 유지 관리하기 어렵다. 워크플로에서 일련의 if 조건을 구현해야 한다. | [GitHub 커뮤니티 토론](https://github.community/t/understanding-github-actions-and-deployments/17575) |
| 리포지토리의 GITHUB_TOKEN을 사용하여 다른 워크플로에서 새 워크플로를 트리거할 수 없다. 옵션은 1) 봇 사용자를 만들고 개인 액세스 토큰을 사용하거나 2) github 앱을 만드는 것이다. | 코드 푸시 후 테스트를 쉽게 시작할 수 없다. 규정된 해결 방법 중 하나를 사용해야 한다. | [GitHub 커뮤니티 토론](https://github.community/t/triggering-a-new-workflow-from-another-workflow/16250) |
| 풀 요청 메시지/댓글을 통해 작업을 트리거할 수 없습니다. | 예를 들어 사용자는 모든 pull 요청에서 실행되는 데 오랜 시간이 걸리는 일부 작업을 원하지 않는다. 대신 PR에 주석을 추가하여 특정 작업이 실행되지 않도록 한다. | [GitHub 커뮤니티 토론](https://github.community/t/trigger-action-via-pull-request-message/17630) |
| YAML 앵커 및 별칭에 대한 지원 부족 | 코드 재사용을 방지한다. 이 제한을 극복하기 위해 사용자는 코드를 잘라내고 붙여넣어야 하며, 워크플로 크기의 5배이며 유지 관리가 어렵다. | [GitHub 커뮤니티 토론](https://github.community/t/support-for-yaml-anchors/16128) |

### 3) 기업 준비

* 위험을 증가시키는 문제

| 설명 | 영향 | 참조 |
|-----|-----|-----|
| 포크 빌드에 사용 가능한 비밀 만들기 | 워크플로의 일반 텍스트에 토큰을 포함하는 등의 해결 방법으로 인해 보안 취약성이 증가했다. | [GitHub 커뮤니티 토론](https://github.community/t/make-secrets-available-to-builds-of-forks/16166) |
| GitHub 작업에는 공개 리포지토리에서 Docker 이미지에 액세스하기 위한 자격 증명이 필요하다. UI를 통해 수동으로 작동한다. | 해결 방법으로 별도의 Docker 레지스트리가 필요하다. 자격 증명 없이 작업을 통해 GitHub 패키지에 액세스할 수 없다. | [GitHub 커뮤니티 토론](https://github.community/t/docker-pull-from-public-github-package-registry-fail-with-no-basic-auth-credentials-error/16358) |

## 4. GitLab CI 규정 준수 차별화 요소

| GitLab 기능 |특징 |
|-----|-----|
| PCI 규정 준수 | GitLab은 PCI 호환을 원하는 기업에 중요한 요소인 애플리케이션 보안을 다룬다. |
| HIPAA | 위험 및 취약성 식별 및 관리, 개발 표준 및 프로세스 정의 및 시행 |
| GDPR | 회원 잠금, 서명되지 않은 커밋 거부, 사용자 권한, 푸시 규칙 등은 민감한 파일이 실수로 프로덕션으로 푸시되는 것을 방지한다. |
| IEC 62304:2006, ISO 13485:2016, ISO 26262-6:2018 | 계획 및 프로세스 작성 및 문서화, 종단 간 추적성 유지 등은 이러한 규정 준수 요구 사항을 지원하는 데 도움이 된다. |

## 5. GitLab과 GitHub CI 구성 비교

![gitlab-vs-github-ci-config](/assets/img/2022-04-18-github-gitlab-continuous-integration-comparison/gitlab-vs-github-ci-config.png)

## 6. GitLab과 GitHub 패킷 레지스트리 비교

![gitlab-vs-github-pr](/assets/img/2022-04-18-github-gitlab-continuous-integration-comparison/gitlab-vs-github-pr.png)

## 7. GitLab과 GitHub CI/CD 보안 비교

![gitlab-vs-github-cicd-security](/assets/img/2022-04-18-github-gitlab-continuous-integration-comparison/gitlab-vs-github-cicd-security.png)

## 8. GitLab 대 GitHub CI/CD 요약 비교

![gitlab-vs-github-cicd-summary](/assets/img/2022-04-18-github-gitlab-continuous-integration-comparison/gitlab-vs-github-cicd-summary.png)

## 9. GitLab과 GitHub Container Registry 비교

| 특징 | 설명 | GitHub | GitLab |
|-----|-----|-----|-----|
| Docker 이미지 지원 | Docker 스타일 컨테이너의 저장 및 검색 지원 | Yes | Yes |
| 컨테이너 레지스트리 웹훅 레지스트리 | 레지스트리에 성공적으로 푸시된 후 작업을 트리거하여 Docker Hub를 다른 서비스와 통합한다. | Yes | Yes |
| 컨테이너 레지스트리 고가용성 | 모든 컨테이너 및 메타데이터의 여러 복제본을 사용하여 고가용성을 제공하므로 시스템에 장애가 발생해도 레지스트리가 계속 작동하고 복구할 수 있다. | No | Yes |
| Container Registry 지리적 복제 | 여러 지역에서 여러 레지스트리 인스턴스를 실행하고 데이터 센터 간에 동기화하여 분산된 팀을 지원한다. | No | Yes |
| 프라이빗 컨테이너 레지스트리 지원 프라이빗 컨테이너 | 레지스트리 및 리포지토리를 보유할 수 있는 기능 제공 | Yes | Yes |
| 이미지 만료 정책 | 프로젝트 수준 정책을 쉽게 정의, 관리 및 업데이트하여 제거하고 보존해야 하는 이미지를 정의한다. 이 기능은 저장 비용을 줄이고 중요한 이미지가 삭제되는 것을 방지하도록 설계되었다. | No | Yes |
| 자체 관리 컨테이너 레지스트리 오퍼링 | 조직의 데이터 센터, 공동 호스트 또는 선택된 클라우드 공급자에 자가 설치 및 자가 관리가 가능한 컨테이너 레지스트리이다. | Yes | Yes |
| REST API를 통해 컨테이너 레지스트리 사용 | REST API를 통한 컨테이너 레지스트리 자동화 및 통합을 지원한다. | No | Yes |
| 가비지 컬렉션을 실행하여 GitLab Container Registry의 스토리지 비용 절감 | Docker 레지스트리 컨텍스트에서 가비지 수집은 매니페스트에서 더 이상 참조하지 않는 Blob을 파일 시스템에서 제거하는 프로세스이다. | No | Yes |
| 검색을 사용하여 컨테이너 이미지 찾기 | 찾기 이미지 이름으로 그룹 및 프로젝트의 Container Registry 검색 | Yes | Yes |
| Helm 차트 저장소 지원 | Helm 차트 저장 및 검색을 지원한다. | Yes | Yes |

## [출처 및 참고]
* <https://about.gitlab.com/devops-tools/github-vs-gitlab/ci-missing-github-capabilities/#github-actions-gaps>
