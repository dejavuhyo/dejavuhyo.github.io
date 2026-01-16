---
title: Git 병합 방법 장단점 및 팀 운영 관점 비교
author: dejavuhyo
date: 2026-01-16 14:30:00 +0900
categories: [DevOps, Git]
tags: [git, git-merge, squash-and-merge, merge-commit, rebase-and-merge, 깃-병합, 깃-병합-비교]
---

## 1. Feature → dev 병합: Squash and Merge (압축 병합)
Git 이력을 기능(Feature) 또는 PR(Pull Request) 단위로 단순화하여, 추후 이력 검색이나 롤백을 용이하게 한다.

### 1) 작동 원리

* PR 생성: 개발자가 feature/A 브랜치에서 작업 후 dev로 PR을 올린다. 이 브랜치에는 C1: WIP, C2: 테스트, C3: 오타 수정, C4: 기능 완료 등의 커밋이 있다.

* 압축: GitHub에서 `Squash and Merge`를 선택하면, 이 네 개의 커밋(C1~C4)의 변경 내용이 하나의 새로운 커밋(C_Squashed)으로 합쳐진다.

* 병합: 이 C_Squashed 커밋만 dev 브랜치에 추가된다.

### 2) 팀 운영 관점의 장점

* 클린 릴리즈 노트: 배포 시 "WIP" 같은 불필요한 커밋 메시지 대신, 'C_Squashed'의 정제된 커밋 메시지 (예: "로그인 기능 구현 및 테스트 완료")를 그대로 사용할 수 있어 릴리즈 노트 작성이 매우 쉽다.

* 간편한 롤백: 만약 이 기능에 문제가 있다면, dev 브랜치에서 C_Squashed 커밋 하나만 git revert하면 기능 전체를 깔끔하게 되돌릴 수 있다.

* PR 단위 관리: PR 단위로 이력을 관리하게 되므로, "이 PR은 언제 병합되었지?"를 찾기가 "이 커밋은 어느 PR에 속하지?"를 찾는 것보다 훨씬 쉽다.

```text
* --- * --- C_SQUASHED <-- main
```

## 2. Dev → stg → prod 병합: Merge Commit (기본 병합)
배포 이력(Release History)을 정확하고 안전하게 보존하여, 감사(Audit)나 문제 발생 시 복구에 대비한다.

### 1) 자세한 작동 원리

* PR 생성: stg 브랜치로 dev의 내용을 병합하는 PR을 생성한다.

* 병합 커밋 생성: 'Merge'를 선택하면, 두 브랜치의 역사를 잇는 새로운 커밋(C_Merge)이 stg 브랜치에 생긴다.

* 이력 보존: C_Merge는 dev의 최신 커밋과 stg의 최신 커밋을 부모로 가진다. 이를 통해 "이 시점에 dev 브랜치의 모든 내용이 stg로 들어왔다"는 공식적인 기록이 남는다.

### 2) 팀 운영 관점의 장점

* 안전한 릴리즈 추적: 이 Merge Commit 자체가 릴리즈 이벤트를 상징한다. 문제가 생기면 "특정 Merge Commit 이후부터 문제가 발생했다"는 명확한 이정표가 된다.

* Revert 안정성: git revert C_Merge 명령 하나로, 이 Merge Commit이 포함한 모든 변경 사항을 원상 복구할 수 있다. 이는 `Rebase and Merge`나 `Squash and Merge`에서는 보장되지 않는 가장 안전한 롤백 방법이다.

* 비파괴적: 원본 커밋 이력을 재작성하지 않으므로, 여러 사람이 동시에 dev 브랜치에 기여해도 충돌이나 이력 손실의 위험이 없다.

```text
* --- * --- C4 (Merge Commit) <-- main
 \           /
  C1 --- C2 --- C3            <-- feature
```

## 3. Rebase and Merge가 적합한 경우
완벽하게 선형적인(Linear) 이력을 만들고, 상세한 커밋 이력도 보존하고 싶을 때 이다.

### 1) 적합한 시나리오

* 단일 개발자 프로젝트: 혼자 개발하거나, 이력 재작성(History Rewriting)의 위험을 충분히 감수할 수 있는 소규모 팀에서 사용한다.

* git bisect 활용: 버그를 유발한 커밋을 찾기 위해 git bisect와 같은 고급 도구를 자주 사용하는 팀은 선형 이력을 선호할 수 있다.

* 작업 브랜치 정리: 최종 병합이 아닌, feature/A 브랜치에 다른 feature/B 브랜치의 내용을 가져와 자신의 커밋을 최신 상태 위로 정리할 때 로컬에서 git rebase를 적극적으로 활용한다.

> 주의: Rebase는 커밋 해시를 바꾸므로, 주요 브랜치(Dev, Stg, Prod)에 직접 적용하는 것은 신중해야 한다. GitHub의 Rebase and Merge 기능은 안전하게 처리하지만, 팀이 이력 재작성을 허용하는지에 대한 합의가 필요하다.

```text
* --- * --- C1' --- C2' --- C3' <-- main/feature
```
