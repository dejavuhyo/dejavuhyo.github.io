---
title: Git Clone과 Git Fork의 차이점
author: dejavuhyo
date: 2021-03-15 06:10:00 +0900
categories: [DevOps, Git]
tags: [clone-vs-fork, git-clone, git-form, clone-fork-difference, clone-fork-차이점]
---

## 1. Forking과 Cloning의 주요 차이점
Forking은 GitHub 계정에서 수행되고 복제는 Git을 사용하여 수행된다. 리포지토리를 포크할 때 원본 리포지토리(업스트림 리포지토리)의 복사본을 생성하지만 리포지토리는 GitHub 계정에 남아 있다. 반면에 저장소를 복제하면 저장소가 Git의 도움으로 로컬 컴퓨터에 복사된다.

![git-clone-and-git-fork](/assets/img/2021-03-15-difference-between-git-clone-and-git-fork/git-clone-and-git-fork.png)

포크된 저장소에 대한 변경 사항은 pull 요청을 통해 원래 저장소와 병합할 수 있다.

Pull request는 저장소 소유자를 노크하고 "일부 변경을 수행했습니다. 원하는 경우 이 변경 내용을 리포지토리에 병합하십시오."라고 말한다. 반면에 로컬 머신(복제된 저장소)에서 변경한 사항은 업스트림 저장소로 직접 푸시할 수 있다. 이를 위해 사용자는 저장소에 대한 쓰기 권한이 있어야 한다. 그렇지 않으면 불가능하다. 사용자에게 쓰기 액세스 권한이 없는 경우 분기된 요청을 통해서만 이동할 수 있다. 따라서 이 경우 복제된 리포지토리에서 변경된 사항이 먼저 포크된 리포지토리로 푸시된 다음 풀 요청이 생성됩니다.

사용자가 기여자로 선언되지 않고 조직이 아닌 타사 저장소인 경우 복제 전에 포크하는 것이 더 나은 옵션이다.

![git-cloning-and-git-forking](/assets/img/2021-03-15-difference-between-git-clone-and-git-fork/git-cloning-and-git-forking.png)

포크는 개념이고 복제는 프로세스입니다. Forking은 저장소의 별도 사본을 포함하며 관련된 명령이 없다. 복제는 'git clone' 명령을 통해 이루어지며 모든 코드 파일을 로컬 머신으로 수신하는 프로세스이다.

## 2. GitHub에서 포크 및 클론을 사용한 흐름 프로세스
분기 및 복제 프로세스는 일반적으로 다음 경로를 따른다.

### 1) 포크없이 Git Repo 복제
복제는 3 단계 프로세스이다.

* 1 단계: 저장소 복제
  - 사용자는 GitHub의 업스트림 저장소에서 시작한다. 사용자가 개념에 관심이 있고 기여하고 싶어서 저장소로 이동했기 때문이다. 이 프로세스는 저장소를 로컬 시스템에 복제할 때 복제에서 시작된다. 이제 시스템에 프로젝트 파일의 정확한 사본이있어 변경을 수행한다.

* 2 단계: 원하는 내용을 변경
  - 복제 후 기여자는 자신의 기여를 저장소에 제공한다. 소스 파일을 편집하여 버그를 수정하거나 기능을 추가하거나 코드를 최적화하는 형태로 기여한다. 이 단계에서 기여자는 단일 커밋 또는 여러 커밋을 저장소에 적용 할 수 있다. 그러나 결론은 모든 것이 로컬 시스템에서 발생한다는 것이다.

* 3 단계: 변경 사항 푸시
  - 변경 사항 또는 커밋이 완료되면 수정 사항을 업스트림 저장소로 푸시 할 수 있다. 

![git-clone](/assets/img/2021-03-15-difference-between-git-clone-and-git-fork/git-clone.png)

### 2) 포킹 후 Git Repo 복제
리포지토리 포크는 5 단계 프로세스이지만 3 단계는 복제와 정확히 동일합니다. 첫 번째 단계와 마지막 단계만 복제와 다르다.

* 1 단계: 리포지토리 포크
  - 다시 사용자는 GitHub의 업스트림 리포지토리에서 시작하지만이 프로세스는 리포지토리를 자신의 GitHub 계정으로 포크 할 때 포크에서 시작됩니다.

* 2 단계: 저장소 복제
  - 복제와 동일하다.

* 3 단계: 원하는대로 변경합니다
  - 복제와 동일하다.

* 4 단계: 변경 사항 푸시
  - 복제와 동일하다.

* 5 단계: 원본 리포지토리로 변경 사항 보내기
  - 이 프로세스 를 Git에서 풀 요청 이라고 한다. 이 단계에서 사용자는 변경 사항을 기본 중앙 저장소에 병합하라는 요청으로 저장소 소유자에게 변경 사항을 보낸다.

![pull-request](/assets/img/2021-03-15-difference-between-git-clone-and-git-fork/pull-request.png)

## 출처 및 참고
* <https://www.toolsqa.com/git/difference-between-git-clone-and-git-fork/>
