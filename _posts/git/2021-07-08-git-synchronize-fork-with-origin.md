---
title: Git 포크 저장소와 원본 저장소 동기화
author: dejavuhyo
date: 2021-07-08 05:00:00 +0900
categories: [DevOps, Git]
tags: [git-sync, git-synchronize-fork-with-origin, synchronize-fork-with-origin, git-fork-origin, 포크-저장소-원본 저장소-동기화, git-동기화]
---

## 1. 개념
원본 저장소의 변경 내용을 포크(fork)한 저장소에 반영

* 포크 저장소: https://github.com/dejavuhyo/jekyll-theme-chirpy.git

* 원본 저장소: https://github.com/cotes2020/jekyll-theme-chirpy.git

## 2. 동기화

### 1) 원본 저장소 등록

* 등록된 저장소 확인

```shell
$ git remote -v
origin https://github.com/dejavuhyo/jekyll-theme-chirpy.git (fetch)
origin https://github.com/dejavuhyo/jekyll-theme-chirpy.git (push)
```

* 원격 저장소 upstream 등록

```shell
$ git remote add upstream https://github.com/cotes2020/jekyll-theme-chirpy.git
```

* 원본 저장소 upstream 등록 확인

```shell
$ git remote -v
origin https://github.com/dejavuhyo/jekyll-theme-chirpy.git (fetch)
origin https://github.com/dejavuhyo/jekyll-theme-chirpy.git (push)
upstream https://github.com/cotes2020/jekyll-theme-chirpy.git (fetch)
upstream https://github.com/cotes2020/jekyll-theme-chirpy.git (push)
```

### 2) 원본 저장소 변경내용 로컬로 가져오기

* 원본 저장소 변경 파일 로컬로 가져오기

```shell
$ git fetch upstream master
```

### 3) 원본 저장소와 포크한 저장소 병합

* 변경내용 병합

```shell
$ git merge upstream/master
```

### 4) commit 메시지 작성

* commit 메시지 작성

```shell
$ git commit -am commit 메시지
```

### 5) 포크한 저장소를 Git 서버에 적용

* 포크 저장소 Git 서버에 적용

```shell
$ git push origin master
```

## [출처 및 참고]
* [https://ondemand.tistory.com/264](https://ondemand.tistory.com/264)
* [https://hyunjun19.github.io/2018/03/09/github-fork-syncing/](https://hyunjun19.github.io/2018/03/09/github-fork-syncing/)
