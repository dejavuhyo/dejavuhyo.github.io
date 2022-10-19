---
title: 더 나은 git 커밋 메시지 작성 패턴
author: dejavuhyo
date: 2022-10-19 10:15:00 +0900
categories: [DevOps, Git]
tags: [git, git-commit, git-commit-message, commitlint, lint-commit-messages, 깃, 깃-커밋, 깃-커밋-메시지, 커밋-메시지]
---

## 1. 좋은 커밋
적절하게 구성된 Git 커밋 제목 줄은 항상 다음 문장을 완성할 수 있어야 한다.

`If applied, this commit <will your subject line here>`

## 2. 커밋 메시지 구조

```text
[type](optional scope): [subject]

[optional body]

[optional footer]
```

### 1) [Subject] scope

* 선택사항이며, 변경된 부분을 직접적으로 표기한다.

* 함수가 변경되었으면 함수명, 메소드가 추가되었으면 클래스 명을 입력한다.

### 2) [Subject] subject

* 첫 글자는 대문자로 입력한다.

* 마지막에는 `.`(period)을 찍지 않으며 영문 기준 최대 50자를 넘지 않는다.

* 제목은 명령문의 형태로 작성한다. (동사원형 사용)

### 3) [Body]

* 각 줄은 최대 72자를 넘지 않도록 한다.

* 어떻게 변경했는지보다, 무엇을 변경했고, 왜 변경했는지를 설명한다.

### 4) [Footer]

* 선택사항이며, 관련된 이슈를 언급한다. 예) Fixes: #1, #2

* 주로 Closes(종료), Fixes(수정), Resolves(해결), Ref(참고), Related to(관련) 키워드를 사용한다.

## 3. 타입

| 타입 | 설명 |
|:-----:|:-----:|
| feat | 새로운 기능 추가 |
| fix | 버그 수정 |
| docs | 문서 수정 |
| style | 코드 formatting, 세미콜론(;) 누락, 코드 변경이 없는 경우 |
| refactor | 코드 리팩터링 |
| test | 테스트 코드, 리팩터링 테스트 코드 추가(프로덕션 코드 변경 X) |
| chore | 빌드 업무 수정, 패키지 매니저 수정(프로덕션 코드 변경 X) |
| design | CSS 등 사용자 UI 디자인 변경 |
| build | 관련 변경 사항 빌드 |
| ci | CI 관련 변경 사항 |
| perf | 성능을 향상시키는 코드 변경 |
| omment | 필요한 주석 추가 및 변경 |
| revert | 되돌리기 |
| rename | 파일 혹은 폴더명을 수정하거나 옮기는 작업만인 경우 |
| remove | 파일을 삭제하는 작업만 수행한 경우 |
| !BREAKING CHANGE | 커다란 API 변경의 경우 |
| !HOTFIX | 급하게 치명적인 버그를 고쳐야 하는 경우 |

## 4. 좋은 Git 커밋 메시지 7가지 규칙

* 본문과 주제를 공백 라인으로 구분한다.

* 제목 줄을 50자 이내로 작성한다.

* 제목 첫 글자를 대문자로 작성한다.

* 제목 줄을 마침표로 끝내지 않는다.

* 제목은 명령조로 작성한다.

* 본문을 72자마다 줄 바꿈 한다.

* 본문은 '어떻게' 보다 '무엇을', '왜'에 맞춰 작성한다.

![ailbaba-fusion-commit](/assets/img/2022-10-19-patterns-for-writing-better-git-commit-messages/ailbaba-fusion-commit.png)

## [출처 및 참고]
* <https://dev.to/helderberto/patterns-for-writing-better-git-commit-messages-4ba0>
* <https://nohack.tistory.com/m/17>
* <https://cocoon1787.tistory.com/m/708>
* <https://github.com/RomuloOliveira/commit-messages-guide>
