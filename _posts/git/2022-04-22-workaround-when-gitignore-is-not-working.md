---
title: gitignore가 동작하지 않을 때 대처법
author: dejavuhyo
date: 2022-04-22 09:10:00 +0900
categories: [DevOps, Git]
tags: [gitignore, gitignore-not-working, git, gitignore-오류]
---

## 1. 모든 파일을 git이 추적 중인 경우

* `.gitignore` 파일을 제대로 작성했는지 확인한다.

* 무시하는 파일을 제외하고 commit을 한다.

* 루트 폴더(최상위 폴더)에서 캐쉬를 삭제한다.

```shell
git rm -r --cached .
```

작업 중인 디렉터리 모든 파일들의 git index가 초기화되어 `.gitignore`가 다시 적용된다.

```shell
git add -A
git commit -m "gitignore 다시 적용"
```

## 2. 이미 추적 중인 파일 몇 개만 무시하고 싶은 경우

* `.gitignore` 파일을 제대로 작성했는지 확인한다.

* 무시하는 파일을 제외하고 commit을 한다.

* 루트 폴더(최상위 폴더)에서 캐쉬를 삭제한다.

```shell
git rm -r --cached A.txt B.txt
```  

선택한 파일의 git index가 초기화되어 `.gitignore`가 다시 적용된다.

```shell
git add -A
git commit -m "gitignore 다시 적용"
```

## [출처 및 참고]
* <https://coding-groot.tistory.com/59>
