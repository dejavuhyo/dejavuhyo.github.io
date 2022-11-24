---
title: Git branch 전략
author: dejavuhyo
date: 2022-11-24 21:00:00 +0900
categories: [DevOps, Git]
tags: [git-branch, git-branch-strategy, branch-strategy, git, git-flow, github-flow, gitlab-flow, 깃-브랜치, 깃-브랜치-전략, 브랜치-전략]
---

## 1. 브랜치 전략이란
브랜치 전략이란 여러 개발자가 하나의 저장소를 사용하는 환경에서 저장소를 효과적으로 활용하기 위한 work-flow다.

브랜치의 생성, 삭제, 병합 등 git의 유연한 구조를 활용해서, 각 개발자의 혼란을 최대한 줄이며 다양한 방식으로 소스를 관리하는 역할을 한다.

즉, 브랜치 생성에 규칙을 만들어서 협업을 유연하게 하는 방법론을 말한다.

## 2. Git Flow
Vincent Driessen이 말한 branching model을 구현한 Git 확장 모듈이다.

기본 브런치는 5가지를 이야기한다. feature > develop > release > hotfix > master 브런치가 존재하며, 머지 순서는 앞에서 뒤로 진행된다. release 브런치와 hotfix 브런치의 경우, develop 브런치의 오른쪽에 존재하기에 모두 develop 브런치도 머지를 하도록 구성이 되어있다.

Vincent Driessen은 관련하여 스크립트로 명령을 구성해놨으며, 그냥 설치하여 CLI에서 명령으로 작업을 하여도 되고, GUI 툴들에서 기본 내장 Git-Flow 명령이나 플러그인을 설치하여 작업을 진행할 수 있도록 보편화되어있는 브런치의 모델이다.

![git-flow](/assets/img/2022-11-24-git-branch-strategy/git-flow.png)

### 1) feature
feature 브랜치는 기능의 구현을 담당한다.

브랜치명은 팀마다 컨벤션을 가지고 지을 수 있지만 feature/{구현기능명}과 같은 명칭을 준수하는 것이 일반적이다.

예를 들어, feature/login은 login 기능을 구현하는 브랜치임을 알 수 있다. feature 브랜치는 develop 브랜치에서 생성되며, develop 브랜치로 머지된다. 머지된 후에는 해당 브랜치가 삭제된다.

### 2) develop
develop 브랜치는 말 그대로 개발을 진행하는 브랜치로 중심적인 브랜치이다.

하나의 feature 브랜치가 머지될 때마다 develop 브랜치에 해당 기능이 더해지며 살을 붙여간다. develop 브랜치는 배포할 수준의 기능을 갖추면 release 브랜치로 머지된다.

### 3) release
release 브랜치는 개발된 내용을 배포하기 위해 준비하는 브랜치이다.

브랜치명은 release-1과 같은 방식으로 첫 번째 릴리즈, 두 번째 릴리즈 등을 지정하는 것이 보편적이다. release 브랜치에서 충분한 테스트를 통해 버그를 검사하고 수정해 배포할 준비가 완전히 되었다고 판단되면 master로 머지해 배포한다.

release 브랜치는 develop 브랜치에서 생성되며 버그 수정 내용을 develop 브랜치에도 반영하고, 최종적으로 master 브랜치에 머지한다.

### 4) hotfix
hotfix 브랜치는 배포된 소스에서 버그가 발생하면 생성되는 브랜치이다.

브랜치명은 hotfix-1로 지정된다. release 브랜치를 거쳐 한차례 버그 검사를 했지만 예상치 못하게 배포 후에 발견된 버그들에 대해서 수정한다. hotfix 브랜치는 master 브랜치에서 생성되며, 수정이 완료되면 develop 브랜치, release 브랜치와 master 브랜치에 수정 사항을 반영한다.

### 5) master
master 브랜치는 최종적으로 배포되는 가장 중심의 브랜치이다.

develop 브랜치에서는 개발이 진행되는 와중에도 이전 release 브랜치 내용이 master에 있어 배포되어 있다.

### 6) 브랜치 전략
Git Flow 브랜치 전략은 여러 지점이 존재하고 지점마다 상황이 명확하게 분류되어 있지만, 오히려 이렇게 많은 브랜치가 흐름을 더욱 복잡하게 만들기도 한다.

그뿐만 아니라, release와 master의 구분이 모호하기도 하다. 하지만 프로젝트의 규모가 커지면 커질수록 소스 코드를 관리하기에 용이하다는 장점이 있다.

## 3. GitHub Flow
Scott chacon은 GitHub Flow에서 Git Flow가 좋은 방식이긴 하지만, GitHub에서 사용하기에는 복잡하다고 생각하여 사용하지 않고 GitHub Flow라는 내용으로 사용하고 있다. 그리고 자동화의 개념이 들어가 있다. 자동화가 안 되어있는 곳에서는 수동으로 관련 작업을 진행하면 된다.

흐름이 단순한 만큼 룰도 단순하다. master 브런치에 대한 role만 정확하면 나머지 브런치들에는 관여하지 않는다. 그리고 pull request 기능을 사용하도록 권장한다.

![github-flow](/assets/img/2022-11-24-git-branch-strategy/github-flow.png)

### 1) GitHub Flow 과정

① master 브랜치에서 개발이 시작된다.

② 기능 구현이나 버그가 발생하면 issue를 작성한다.

③ 팀원들이 issue 해결을 위해 master 브랜치에서 생성한 feature/{구현기능} 지점에서 개발하고 commit log를 작성한다.

④ push를 하면 pull request를 날릴 수 있다.

⑤ pull request를 통해 팀원들 간의 피드백, 버그 찾는 과정이 진행된다. release 브랜치가 없으므로 이 과정이 탄탄하게 진행되어야 한다.

⑥ 모든 리뷰가 이루어지면, merge 하기 전에 배포를 통해 최종 테스트를 진행한다.

⑦ 테스트까지 진행되면 master 브랜치에 머지한다.

### 2) 브랜치 전략
GitHub Flow는 시시각각 master에 머지될 때마다 배포가 이루어지는 것이 좋다. 따라서 CI/CD를 통한 배포 자동화를 적용하는 것이 좋다.

브랜치 전략이 단순해 master 브랜치에서 pull하고, 기능 구현하고, 머지하는 일의 반복이다. 그러나 pull request에서 팀원 간의 충분한 리뷰와 피드백이 진행되지 않으면 배포된 자체에서 버그가 발생할 수 있으므로 주의해야 한다.

## 4. GitLab Flow
GitLab Flow는 복잡하지 않고 효율을 높이고자 생겨난 브랜치 전략으로 master, feature, production 브랜치가 존재한다.

GitLab Flow는 이슈 트래킹을 연동해 프로세스를 단순화하고자 한다. merge request를 통해 승인되는 이슈만 머지하도록 하는 것이 핵심이다.

![gitlab-flow](/assets/img/2022-11-24-git-branch-strategy/gitlab-flow.png)

### 1) feature
모든 기능 구현은 feature 브랜치에서 진행된다. feature 브랜치는 master 브랜치에서 나와 master 브랜치로 머지된다. 기능 구현이 완료되면 merge request를 보낸다. merge request에서 팀원 간의 협의가 완료되면 master 브랜치로 머지한다.

### 2) master
GitLab Flow의 master 브랜치는 Git Flow의 develop 브랜치와 같다. master 브랜치는 feature 브랜치에서 병합된 기능에 대해 테스트한다. 전체적인 테스트가 진행되어 기능에 대한 보장이 되었다면 production 브랜치로 머지한다.

### 3) production
production 브랜치는 한 마디로 배포 브랜치이다. git flow의 master 브랜치와 같다. 안정된 소스 코드가 되었을 때 production 브랜치에 병합해 배포하도록 한다. 그러나 여기서 견고한 test를 거치고 싶은 경우 pre-production 브랜치를 생성해 production에 병합하기 전에 test server에 배포해 확인할 수도 있다.

### 4) 브랜치 전략
GitLab Flow는 Git Flow처럼 복잡하지 않으면서, GitHub Flow처럼 너무 단순하지 않아 비교적 적용이 쉬우면서도 원활한 운영이 가능하다.

GitLab의 CEO는 [최대한의 효울을 위해 지켜야 할 11가지 규칙](https://about.gitlab.com/topics/version-control/what-are-gitlab-flow-best-practices/)에 관해 서술해두었다. 이 11가지 규칙에 대해서 이해하고 적용한다면 최상의 GitLab Flow 전략을 가져갈 수 있을 것이다.

## [출처 및 참고]
* <https://ujuc.github.io/2015/12/16/git-flow-github-flow-gitlab-flow/>
* <https://tecoble.techcourse.co.kr/post/2021-07-15-git-branch/>
* <https://inpa.tistory.com/entry/GIT-%E2%9A%A1%EF%B8%8F-github-flow-git-flow-%F0%9F%93%88-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EC%A0%84%EB%9E%B5>
