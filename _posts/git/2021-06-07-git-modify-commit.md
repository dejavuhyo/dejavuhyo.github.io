---
title: Git 커밋 수정
author: dejavuhyo
date: 2021-06-07 06:20:00 +0900
categories: [DevOps, Git]
tags: [git-modify-commit, modify-commit, git-커밋-수정, 커밋-수정]
---

## 1. 이전에 작성한 커밋 수정
`--amend` 옵션을 지정하여 커밋을 수행하면, 같은 브랜치 상에서 이전에 커밋했던 내용에 새로운 내용을 추가하거나 설명을 수정할 수 있다.

* 사용
  - 누락된 파일을 새로 추가하거나 기존의 파일을 업데이트 해야 할 때
  - 이전 커밋의 설명을 변경하고 싶을 때

## 2. 이전에 작성한 커밋 삭제
revert 명령어를 이용하면, 특정 커밋의 내용을 삭제할 수 있다.

rebase -i 명령어나 reset 명령어를 통해 커밋을 삭제할 수도 있지만, 해당 커밋이 이미 공개된 상태인 경우에는 이러한 삭제 작업을 함부로 하기 어렵다. 이러한 경우에는 revert 명령어를 이용해서 특정 커밋의 내용을 지우는 새로운 커밋(B')을 만들어 보다 안전하게 처리할 수 있다.

아래 그림에서 보면, revert 명령어를 사용하여 커밋 B의 내용을 뒤집어 원래의 커밋 A의 상태로 돌아갈 수 있는 새로운 커밋 B'를 만들 수 있음을 알 수 있다.

* 사용
  - 이전에 작성한 커밋을 안전하게 지우고 싶을 때

![delete](/assets/img/2021-06-07-git-modify-commit/delete.png)

## 3. 커밋을 버리고 특정 버전으로 다시 되돌아가기
reset 명령어를 이용하면 더 필요 없어진 커밋들을 버릴 수 있다. 명령어 실행 시 어떤 모드로 실행할지 지정하여 'HEAD' 위치와 인덱스, 작업 트리 내용을 함께 되돌릴지를 선택할 수 있다.

![reset](/assets/img/2021-06-07-git-modify-commit/reset.png)

모드는 기본적으로 'mixed'가 지정되며, 아래의 표에서와같이 'soft'와 'hard' 중에서 선택할 수도 있다.

| 모드명 | HEAD의 위치 | 인덱스 | 작업 트리 |
|:-----:|:-----:|:-----:|:-----:|
| soft | 변경함 | 변경 안 함 | 변경 안 함 |
| mixed | 변경함 | 변경함 | 변경 안 함 |
| hard | 변경함 | 변경함 | 변경함 |

* 사용
  - 커밋만 되돌리고 싶을 때 (soft)
  - 변경한 인덱스의 상태를 원래대로 되돌리고 싶을 때 (mixed)
  - 최근의 커밋을 완전히 버리고 이전의 상태로 되돌리고 싶을 때 (hard)

## 4. 다른 브랜치로부터 특정 커밋을 가져와서 내 브랜치에 넣기
cherry-pick을 이용하면 다른 브랜치에서 지정한 커밋을 복사하여 현재 브랜치로 가져올 수 있다.

![cherry-pick](/assets/img/2021-06-07-git-modify-commit/cherry-pick.png)

* 사용
  - 특정 브랜치에 잘못 추가한 커밋을 올바른 브랜치로 옮기려고 할 때
  - 다른 브랜치의 커밋을 현재 브랜치에도 추가하고 싶을 때

## 5. 커밋 이력 편집
rebase 명령어에 'i' 옵션을 지정하면 커밋을 다시 쓰거나 다른 커밋과 바꿔 넣을 수 있으며 특정 위치의 커밋을 삭제하거나 여러 커밋을 하나로 통합하는 작업을 할 수 있다.

![rewrite](/assets/img/2021-06-07-git-modify-commit/rewrite.png)

![integration](/assets/img/2021-06-07-git-modify-commit/integration.png)

* 사용
  - push 하기 전에 이전의 커밋 내용을 정리하고자 할 때
  - 그룹으로 묶을 수 있는 커밋들을 알기 쉽게 하나로 통합하려고 할 때
  - 이전 커밋에 누락된 파일들을 나중에 추가하고자 할 때

## 6. 브랜치상의 커밋을 하나로 모아 병합
`--squash` 옵션을 지정하여 브랜치를 병합하면 해당 브랜치의 커밋 전체를 통합한 커밋이 추가된다.

![squash](/assets/img/2021-06-07-git-modify-commit/squash.png)

* 사용
  - 토픽 브랜치 안의 커밋을 한꺼번에 모아서 통합 브랜치에 병합하고자 할 때

## [출처 및 참고]
* <https://backlog.com/git-tutorial/kr/stepup/stepup6_1.html>
* <https://backlog.com/git-tutorial/kr/stepup/stepup6_2.html>
* <https://backlog.com/git-tutorial/kr/stepup/stepup6_3.html>
* <https://backlog.com/git-tutorial/kr/stepup/stepup6_4.html>
* <https://backlog.com/git-tutorial/kr/stepup/stepup6_5.html>
* <https://backlog.com/git-tutorial/kr/stepup/stepup6_6.html>
