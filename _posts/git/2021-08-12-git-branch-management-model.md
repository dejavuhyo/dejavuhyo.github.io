---
title: Git branch 관리 Model
date: 2021-08-12 06:00:00 +0900
categories: [DevOps, Git]
tags: [git-branch, git-branch-management, git-branch, git-branch-관리-model, git-branch-관리, git-branch-관리-모델, git-branch-관리, git-branch-규칙]
---

## 1. 개요
아래 그림은 Git으로 버전 관리하는 것을 한눈에 보여준다.

![git-model](/assets/img/2021-08-12-git-branch-management-model/git-model.png)

## 2. 왜 Git인가
중앙 집중식 소스 코드 제어 시스템(Centralized source code control system)과 Git의 장점 및 단점 비교에 대한 자세한 내용은 [링크](https://git.wiki.kernel.org/index.php/GitSvnComparsion)를 참고한다.

## 3. 분산이지만 중앙집중식
분기 모델에 적합한 저장소 설정은 중앙의 'truth' 저장소이다. 이 저장소는 중앙 저장소로만 간주된다(Git은 DVCS이므로 기술 수준에서 중앙 저장소와 같은 것은 없음). 모든 Git 사용자에게 친숙한 명칭이기 때문에 이 저장소를 origin 이라고 한다.

![decentralized-centralized](/assets/img/2021-08-12-git-branch-management-model/decentralized-centralized.png)

각 개발자는 origin으로 pulls와 pushe를 한다. 그러나 중앙 집중식 push-pull 관계 외에도 각 개발자는 다른 개발자의 변경 사항을 가져와 하위 팀을 구성할 수도 있다.

예를 들어, 진행 중인 작업을 조기에 시작하도록 하기 전에 두 명 이상의 개발자와 함께 새로운 기능을 사용하는 것이 유용할 수 있다.

위의 그림에는 앨리스와 밥, 앨리스와 데이빗, 그리고 클레어와 데이빗의 하위팀이 있다.

엄밀히 말하면, 이것은 앨리스가 밥이라는 이름의 Git 리모콘을 정의하고 밥의 저장소를 가리키고 그 반대의 경우도 마찬가지라는 것을 의미한다.

## 4. 메인 분기(The main branches)
핵심적으로 개발 모델은 기존 모델에서 크게 영감을 받았다. 중앙 저장소에는 수명이 무한한 두 가지 주요 branches가 있다.

* master

* develop

![main-branches](/assets/img/2021-08-12-git-branch-management-model/main-branches.png)

master branch origin은 모든 Git 사용자에게 친숙해야 한다. master branches에 병렬로 develop라는 다른 branches가 있다.

origin/master를 HEAD 소스 코드가 항상 프로덕션 준비 상태를 반영하는 주요 branches로 간주 한다.

HEAD의 소스 코드에 항상 다음 릴리즈에 대한 최신 개발 변경사항이 있는 상태를 반영하는 것이 'origin/develop'이라고 생각한다. 어떤 사람들은 이것을 '통합 지점'이라고 부른다. 여기서 모든 야간 자동 빌드가 작성된다.

develop branches의 소스 코드가 안정적인 지점에 도달하여 릴리스할 준비가 되면 모든 변경 사항을 마스터에 다시 병합한 다음 릴리스 번호로 태그를 지정해야 한다.

변경사항이 마스터로 다시 병합될 때마다 이는 정의에 따라 새로운 생산 릴리즈이다. 우리는 매우 엄격한 경향이 있기 때문에 이론적으로 마스터에 대한 커밋이 있을 때마다 Git hook 스크립트를 사용하여 프로덕션 서버에 소프트웨어를 자동으로 빌드하고 롤아웃할 수 있다.

## 5. 다양한 분기들(Supporting branches)
주요 지점 마스터 및 개발 모델 외에도, 우리의 개발 모델은 다양한 지원 지점을 사용하여 팀원 간의 병렬 개발, 기능 추적, 생산 릴리즈 준비 및 라이브 프로덕션 문제 해결을 지원한다. 본점과 달리, 이 가지들은 결국 제거되기 때문에 항상 수명이 제한되어 있다. main branches와 달리, 이 가지들은 결국 제거되기 때문에 항상 수명이 제한되어 있다.

사용할 수 있는 다른 타입의 branches는 다음과 같다.

* Feature branches

* Release branches

* Hotfix branches

이러한 각 branches에는 특정 용도가 있으며 어떤 branches가 main branches가 될 수 있고 어떤 branches가 병합 대상이 되어야 하는지에 대한 엄격한 규칙이 적용된다.

기술적 관점에서 이 지점들은 결코 '특수'하지 않다. 지점 유형은 사용 방법에 따라 분류된다. 그들은 물론 평범하고 오래된 Git branches이다.

### 1) 기능 분기(Feature branches)

* 다음에서 분기할 수 있음: develop

* 다음에 다시 병합: develop

* 분기 이름 지정 규칙: anything except master, develop, ```release-*```, or ```hotfix-*```

![feature-branches](/assets/img/2021-08-12-git-branch-management-model/feature-branches.png)

Feature branches(또는 주제 분기라고도 함)는 향후 릴리스 또는 먼 미래의 릴리스에 사용할 새로운 기능을 개발하는 데 사용된다. 기능 개발을 시작할 때, 이 기능이 통합될 대상 릴리즈는 해당 시점에서는 알 수 없다.

feature branch의 본질은 피쳐가 개발 중인 한 존재하지만, 결국 다시 통합되거나(새로운 피쳐를 차기 릴리즈에 추가하기 위해) 폐기될 것이라는 것이다(실패한 실험의 경우).

Feature branches는 일반적으로 원본이 아닌 개발자 리포지토리에만 있습니다.

#### (1) Feature branch 작성

```bash
$ git checkout -b myfeature develop
Switched to a new branch "myfeature"
```

#### (2) 개발 완료 기능 통합
완료된 기능을 개발 분기에 병합하여 다음 릴리스에 추가할 수 있다.

````bash
$ git checkout develop
Switched to branch 'develop'

$ git merge --no-ff myfeature
Updating ea1b82a..05e9557
(Summary of changes)

$ git branch -d myfeature
Deleted branch myfeature (was 05e9557).

$ git push origin develop
````

```--no-off``` 플래그를 사용하면 병합을 빠른 회송으로 수행할 수 있더라도 병합에서 항상 새 커밋 개체를 만들 수 있다. 이렇게 하면 피쳐 분기의 과거 존재에 대한 정보가 손실되는 것을 방지하고 피쳐를 추가한 모든 커밋을 함께 그룹화한다.

![merge-without-ff](/assets/img/2021-08-12-git-branch-management-model/merge-without-ff.png)

후자의 경우 Git 기록에서 기능을 함께 구현한 커밋 개체를 확인할 수 없다. 모든 로그 메시지를 수동으로 읽어야 한다. ```--no-off``` 플래그를 사용한 경우 쉽게 수행되는 반면, 전체 기능(즉, 커밋 그룹)을 되돌리는 것은 후자의 경우 진정한 골칫거리이다.

### 2) 출시 분기(Release branches)

* 다음에서 분기할 수 있음: develop

* 다음에 다시 병합: develop and master

* 분기 이름 지정 규칙: ```release-*```

릴리스 지점에서는 새로운 프로덕션 릴리스 준비를 지원합니다. 마지막 순간에 i를 찍고 t를 교차할 수 있게 해준다. 또한 사소한 버그 수정과 릴리스용 메타 데이터(버전 번호, 빌드 날짜 등)를 준비할 수 있다.

이 모든 작업을 release branch에서 수행함으로써 develop branch는 다음 대규모 릴리스에 대한 기능을 수신할 수 있다. 개발에서 새로운 릴리스 분기를 분리하는 가장 중요한 시점은 개발(거의)이 새 릴리스의 원하는 상태를 반영할 때이다.

이 시점에서 개발하려면 적어도 release-to-be-built 대상으로 지정된 모든 기능을 병합해야 한다. 이후 릴리즈를 대상으로 하는 모든 기능은 그렇지 않을 수 있다. release branch가 분기될 때까지 기다려야 한다.

다음 릴리스에는 이전 버전이 아닌 버전 번호가 할당된다. 당시까지는 개발지사에서 '차기 출시'에 대한 변경 사항을 반영했지만, 출시지점이 시작되기 전까지는 '차기 출시'가 결국 0.3이 될지 1.0이 될지 알 수 없다.

이 결정은 릴리스 지점 시작 시 이루어지며 버전 번호 범핑에 대한 프로젝트의 규칙에 따라 수행된다.

#### (1) Release branch 작성
Release branches는 develop branch에서 생성된다. 예를 들어 버전 1.1.5가 현재 생산 릴리즈이고 곧 큰 릴리즈가 있을 예정이다. 개발 상태는 '다음 릴리스'로 준비되었으며, 버전 1.2(1.1.6 또는 2.0이 아님)가 될 것으로 결정했다. 따라서 분기하여 릴리스 지점에 새 버전 번호를 반영하는 이름을 지정한다.

```bash
$ git checkout -b release-1.2 develop
Switched to a new branch "release-1.2"

$ ./bump-version.sh 1.2
Files modified successfully, version bumped to 1.2.

$ git commit -a -m "Bumped version number to 1.2"
[release-1.2 74d9424] Bumped version number to 1.2
1 files changed, 1 insertions(+), 1 deletions(-)
```

새 분기를 만들고 전환한 후 버전 번호를 맞춘다. bump-version.sh는 작업 복사본의 일부 파일을 새 버전을 반영하도록 변경하는 가상 셸 스크립트이다. (물론 수동 변경일 수 있다. 중요한 것은 일부 파일이 변경된다는 것이다.) 그러면 부딪힌 버전 번호가 커밋된다.

이 새로운 브랜치는 출시될 때까지 한동안 그곳에 존재할 수 있다. 이 기간 동안 버그 수정 사항이 개발 분기가 아닌 이 분기에 적용될 수 있다. 여기에 대규모 새 기능을 추가하는 것은 엄격히 금지된다. 이들은 합병되어 개발되어야 하며, 따라서 다음 큰 출시를 기다려야 한다.

#### (2) Release branch 완료
릴리스 분기의 상태가 실제 릴리스가 될 준비가 되면 몇 가지 작업을 수행해야 한다.

먼저 릴리스 분기가 master로 병합된다(마스터에 대한 모든 커밋은 정의에 따라 새 릴리스이므로 기억한다.).

다음으로, 마스터에 대한 커밋을 태그해야 나중에 이 기록 버전을 쉽게 참조할 수 있다.

마지막으로 릴리스 분기에서 변경한 내용을 다시 병합하여 개발해야 향후 릴리스에도 이러한 버그 수정 사항이 포함된다.

Git의 처음 두 단계

```bash
$ git checkout master
Switched to branch 'master'

$ git merge --no-ff release-1.2
Merge made by recursive.
(Summary of changes)

$ git tag -a 1.2
```

이제 릴리스가 완료되었으며 나중에 참조할 수 있도록 태그가 지정되었다.

> ```-s``` 또는 ```-u <key>``` 플래그를 사용하여 암호화된 방식으로 태그에 서명하는 것이 좋다.

출시 지점의 변경 사항을 유지하려면 다시 병합하여 개발해야 한다.

```bash
$ git checkout develop
Switched to branch 'develop'

$ git merge --no-ff release-1.2
Merge made by recursive.
(Summary of changes)
```

이 단계에서는 버전 번호를 변경했더라도 병합 충돌이 발생할 수 있다. 만약 그렇다면, fix and commit 한다.

이제 정말 완료되었고 릴리스 지점이 제거될 수도 있다. 더 이상 필요하지 않기 때문이다.

```bash
$ git branch -d release-1.2
Deleted branch release-1.2 (was ff452fe).
```

### 3) 핫픽스 분기(Hotfix branches)

* 다음에서 분기할 수 있음: master

* 다음에 다시 병합: develop and master

* 분기 이름 지정 규칙: ```hotfix-*```

![hotfix-branches](/assets/img/2021-08-12-git-branch-management-model/hotfix-branches.png)

Hotfix branches는 계획되지 않았지만 새로운 프로덕션 릴리즈를 준비한다는 점에서 릴리즈 지점과 매우 유사하다. 이는 원하지 않는 생산 버전의 상태에 대해 즉시 조치할 필요성에서 발생한다.

프로덕션 버전의 중요한 버그를 즉시 해결해야 하는 경우 운영 버전을 표시하는 마스터 분기의 해당 태그에서 hotfix branch가 분기될 수 있다.

핵심은 (develop branch에서) 팀원들의 작업이 계속될 수 있고, 다른 사람이 빠른 생산 수정을 준비하는 것이다.

#### (1) Hotfix branch 생성
Hotfix branch는 마스터 분기에서 생성된다. 예를 들어 버전 1.2가 현재 실행 중인 프로덕션 릴리스이며 심각한 버그로 인해 문제를 일으킨다고 가정해 보겠다. 그러나 개발상의 변화는 아직 불안정하다. 그런 다음 핫픽스 지점을 분리하여 문제 해결을 시작할 수 있다.

```bash
$ git checkout -b hotfix-1.2.1 master
Switched to a new branch "hotfix-1.2.1"

$ ./bump-version.sh 1.2.1
Files modified successfully, version bumped to 1.2.1.

$ git commit -a -m "Bumped version number to 1.2.1"
[hotfix-1.2.1 41e61bb] Bumped version number to 1.2.1
1 files changed, 1 insertions(+), 1 deletions(-)
```

분기 후 버전 번호를 넘기는 것이 중요하다.

그런 다음 버그를 수정하고 하나 이상의 개별 커밋에서 수정 사항을 커밋한다.

```bash
$ git commit -m "Fixed severe production problem"
[hotfix-1.2.1 abbe5d6] Fixed severe production problem
5 files changed, 32 insertions(+), 17 deletions(-)
```

#### (2) Hotfix branch 완료
완료되면 버그 픽스가 다음 릴리스에도 포함되도록 하려면 버그 픽스를 마스터로 다시 병합해야 하지만 다시 병합하여 개발해야 한다. 이는 릴리스 분기가 완료되는 방식과 완전히 유사하다.

먼저 마스터를 업데이트하고 릴리스에 태그를 지정한다.

```bash
$ git checkout master
Switched to branch 'master'

$ git merge --no-ff hotfix-1.2.1
Merge made by recursive.
(Summary of changes)

$ git tag -a 1.2.1
```

> ```-s``` 또는 ```-u <key>``` 플래그를 사용하여 암호화된 방식으로 태그에 서명하는 것이 좋다.

다음으로, 개발 중인 버그 픽스도 포함한다.

```bash
$ git checkout develop
Switched to branch 'develop'

$ git merge --no-ff hotfix-1.2.1
Merge made by recursive.
(Summary of changes)
```

여기서 한 가지 예외는 릴리스 분기가 현재 존재하는 경우 핫픽스 변경 사항을 개발하지 않고 해당 릴리스 분기에 병합해야 한다는 것이다.

버그픽스를 릴리스 분기에 다시 병합하면 릴리스 분기가 종료될 때 버그픽스도 개발로 병합된다. (개발 중인 작업에서 즉시 이 버그픽스가 필요하고 릴리스 분기가 완료될 때까지 기다릴 수 없는 경우 버그픽스를 안전하게 병합하여 현재 개발 한다.)

마지막으로 임시 분기를 제거한다.

```bash
$ git branch -d hotfix-1.2.1
Deleted branch hotfix-1.2.1 (was abbe5d6).
```

## [출처 및 참고]
* <https://nvie.com/posts/a-successful-git-branching-model/>
* <http://amazingguni.github.io/blog/2016/03/git-branch-%EA%B7%9C%EC%B9%99>
* <https://luckygg.tistory.com/212>
