---
title: Git 브랜치 통합
date: 2021-05-28 06:00:00 +0900
categories: [DevOps, Git]
tags: [git-branch-integration, branch-integration, branch-merge, branch-rebase, git-브랜치-통합, 깃-브랜치-통합, 브랜치-통합, 브랜치-병합]
---

## 1. 브랜치 통합
작업이 완료된 토픽 브랜치는 최종적으로 통합 브랜치에 병합된다. 브랜치 통합에는 'merge'를 사용하는 방법과 'rebase'를 사용하는 방법의 2가지 종류가 있다. 어느 쪽을 사용하느냐에 따라 통합 후의 브랜치의 이력이 크게 달라진다.

## 2. merge
merge를 사용하면, 여러 개의 브랜치를 하나로 모을 수 있다.

예를 들어, 아래 그림과 같이 'master' 브랜치에서 분기하는 'bugfix'라는 브랜치가 있다고 가정한다.

![bugfix-branch](/assets/img/2021-05-28-integration-git-branch/bugfix-branch.png)

이 'bugfix' 브랜치를 'master' 브랜치로 병합할 때, 'master' 브랜치의 상태가 이전부터 변경되어 있지만 않으면 매우 쉽게 병합할 수 있다.

'bugfix' 브랜치의 이력은 'master' 브랜치의 이력을 모두 포함하고 있기 때문에, 'master' 브랜치는 단순히 이동하기만 해도 'bugfix' 브랜치의 내용을 적용할 수 있다.

또한 이 같은 병합은 'fast-forward(빨리 감기) 병합'이라고 부른다.

![fast-forward](/assets/img/2021-05-28-integration-git-branch/fast-forward.png)

하지만 'bugfix' 브랜치를 분기한 이후에 'master' 브랜치에 여러 가지 변경 사항이 적용되는 경우도 있다. 이 경우에는 'master' 브랜치 내의 변경 내용과 'bugfix' 브랜치 내의 변경 내용을 하나로 통합할 필요가 있다.

![integration-master-bugfix](/assets/img/2021-05-28-integration-git-branch/integration-master-bugfix.png)

따라서 양쪽의 변경을 가져온 'merge commit(병합 커밋)'을 실행하게 된다. 병합 완료 후, 통합 브랜치인 'master' 브랜치로 통합된 이력이 아래 그림과 같이 생기게 된다.

![merge-commit](/assets/img/2021-05-28-integration-git-branch/merge-commit.png)

> 병합 실행 시에 'fast-forward 병합'이 가능한 경우라도 'non fast-forward 병합' 옵션을 지정하여 아래 그림과 같이 만들어 낼 수도 있다.

![non-fast-forward](/assets/img/2021-05-28-integration-git-branch/non-fast-forward.png)

## 3. rebase
위와 마찬가지로, 'master' 브랜치에서 분기하는 'bugfix' 브랜치가 있다고 가정한다.

![master-bugfix](/assets/img/2021-05-28-integration-git-branch/master-bugfix.png)

아래 그림과 같이 'non fast-forward 병합' 방식으로 진행되는 시나리오를 만든다.

![non-fast-forward-integration](/assets/img/2021-05-28-integration-git-branch/non-fast-forward-integration.png)

'bugfix' 브랜치를 'master' 브랜치에 rebase 하면, 'bugfix' 브랜치의 이력이 'master' 브랜치 뒤로 이동하게 된다. 그 때문에 그림과 같이 이력이 하나의 줄기로 이어지게 된다.

이때 이동하는 커밋 X와 Y 내에 포함되는 내용이 'master'의 커밋된 버전들과 충돌하는 부분이 생길 수 있다. 그때는 각각의 커밋에서 발생한 충돌 내용을 수정할 필요가 있다.

![rebase](/assets/img/2021-05-28-integration-git-branch/rebase.png)

'rebase'만 하면 아래 그림에서와같이, 'master'의 위치는 그대로 유지된다.

'master' 브랜치의 위치를 변경하기 위해서는 'master' 브랜치에서 'bugfix' 브랜치를 fast-forward(빨리감기) 병합하면 된다.

![master-bugfix-fast-forward](/assets/img/2021-05-28-integration-git-branch/master-bugfix-fast-forward.png)

## [출처 및 참고]
* <https://backlog.com/git-tutorial/kr/stepup/stepup1_4.html>
