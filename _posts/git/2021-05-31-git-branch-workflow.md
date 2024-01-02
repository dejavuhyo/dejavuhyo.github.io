---
title: Git 토픽 브랜치와 통합 브랜치 작업 흐름
author: dejavuhyo
date: 2021-05-31 06:00:00 +0900
categories: [DevOps, Git]
tags: [git-branch-workflow, branch-workflow, branch-workflow, git-브랜치-작업-흐름, 깃-브랜치-작업-흐름, 브랜치-작업-흐름]
---

## 1. 토픽 브랜치와 통합 브랜치 작업 흐름
토픽 브랜치와 통합 브랜치를 사용한 작업순서 예시이다.

토픽 브랜치에서 새로운 기능을 추가하는 작업과 버그 수정 작업을 동시에 진행하는 경우이다.

![add-function](/assets/img/2021-05-31-git-branch-workflow/add-function.png)

통합 브랜치로부터 새롭게 버그 수정용 토픽 브랜치를 만들어, 새로운 기능을 추가하는 작업과는 별개로 버그 수정 작업을 진행할 수 있다.

![error-modification](/assets/img/2021-05-31-git-branch-workflow/error-modification.png)

버그 수정을 완료한 후, 통합 브랜치와 버그 수정용 토픽 브랜치를 병합하여 수정된 버전을 만들어 낼 수 있다.

![complete](/assets/img/2021-05-31-git-branch-workflow/complete.png)

버그 수정 완료 후, 다시 원래 브랜치로 돌아와서 새로운 기능 추가 작업을 계속 진행하려 한다.

![new-function-add](/assets/img/2021-05-31-git-branch-workflow/new-function-add.png)

그러나, 작업을 진행하려고 봤더니 앞서 적용한 커밋 X의 버그가 수정된 버전의 소스코드를 지금의 커밋 O에도 적용해야만 한다는 사실을 알게 되었다. 여기서 커밋 X의 내용을 적용하려면, 직접 merge 하는 방법과 커밋 X를 적용한 통합 브랜치에 rebase 하는 방법이 있다.

통합 브랜치에 rebase 하는 방법을 이용하는 방법이다.

![rebase](/assets/img/2021-05-31-git-branch-workflow/rebase.png)

이런 상황에서는, rebase 를 이용하여 커밋 X의 내용을 적용한 상태로 새로운 기능을 추가하기 위해 아래 그림과 같이 O' 버전으로 만들어 내는 방법을 이용하면 된다.

> 브랜치를 능숙하게 사용하면 상황에 맞게 여러 작업을 동시에 진행할 수 있다.

## 2. 성공적인 Git 브랜칭 모델
[A successful Git branching model](nvie.com/posts/a-successful-git-branching-model/) 모델에서는 크게 4가지 종류의 브랜치를 이용하여 개발을 진행한다.

* 메인 브랜치(Main branch)

* 피처 브랜치(Feature branch) 또는 토픽 브랜치(Topic branch)

* 릴리스 브랜치(Release branch)

* 핫픽스 브랜치(Hotfix branch)

![branching-model](/assets/img/2021-05-31-git-branch-workflow/branching-model.png)

### 1) 메인 브랜치(Main branch)
'master' 브랜치와 'develop' 브랜치, 이 두 종류의 브랜치를 보통 메인 브랜치로 사용한다.

* **master:** 'master' 브랜치에서는, 배포 가능한 상태만을 관리한다. 커밋할 때에는 태그를 사용하여 배포 번호를 기록한다.

* **develop:** 'develop' 브랜치는 앞서 설명한 통합 브랜치의 역할을 하며, 평소에는 이 브랜치를 기반으로 개발을 진행한다.

### 2) 피처 브랜치(Feature branch)
피처 브랜치는, 토픽 브랜치 역할을 담당한다.

이 브랜치는 새로운 기능 개발 및 버그 수정이 필요할 때에 'develop' 브랜치로부터 분기한다. 

피처 브랜치에서의 작업은 기본적으로 공유할 필요가 없기 때문에, 원격으로는 관리하지 않는다. 개발이 완료되면 'develop' 브랜치로 병합하여 다른 사람들과 공유한다.

### 3) 릴리즈 브랜치(Release branch)
릴리즈 브랜치에서는 버그를 수정하거나 새로운 기능을 포함한 상태로 모든 기능이 정상적으로 동작하는지 확인한다.

릴리즈 브랜치의 이름은 관례적으로 브랜치 이름 앞에 'release-' 를 붙인다. 이때, 다음번 릴리즈를 위한 개발 작업은 'develop' 브랜치에서 계속 진행해 나가면 된다.

릴리즈 브랜치에서는 릴리즈를 위한 최종적인 버그 수정 등의 개발을 수행한다. 모든 준비를 마치고 배포 가능한 상태가 되면 'master' 브랜치로 병합시키고, 병합한 커밋에 릴리즈 번호 태그를 추가한다.

릴리즈 브랜치에서 기능을 점검하며 발견한 버그 수정 사항은 'develop' 브랜치에도 적용해 주어야 한다. 그러므로 배포 완료 후 'develop' 브랜치에 대해서도 병합 작업을 수행한다.

### 4) 핫픽스 브랜치(Hotfix branch)
배포한 버전에 긴급하게 수정을 해야 할 필요가 있으면, 'master' 브랜치에서 분기하는 브랜치이다. 관례적으로 브랜치 이름 앞에 'hotfix-'를 붙인다.

예를 들어 'develop' 브랜치에서 개발을 한창 진행하고 있는 도중에 이전에 배포한 소스코드에 큰 버그가 발견되는 경우를 생각해 본다.

문제가 되는 부분을 빠르게 수정해서 안정적으로 다시 배포해야 하는 상황이다. 'develop' 브랜치에서 문제가 되는 부분을 수정하여 배포 가능한 버전을 만들기에는 시간도 많이 소요되고 안정성을 보장하기도 어렵다.

그렇기 때문에 바로 배포가 가능한 'master' 브랜치에서 직접 브랜치를 만들어 필요한 부분만을 수정한 후 다시 'master'브랜치에 병합하여 이를 배포하려고 하는 것이다.

이때 만든 핫픽스 브랜치에서의 변경 사항은 'develop' 브랜치에도 병합하여 문제가 되는 부분을 처리해 주어야 한다.

## [출처 및 참고]
* [https://backlog.com/git-tutorial/kr/stepup/stepup1_5.html](https://backlog.com/git-tutorial/kr/stepup/stepup1_5.html)
* [https://nvie.com/posts/a-successful-git-branching-model/](https://nvie.com/posts/a-successful-git-branching-model/)
