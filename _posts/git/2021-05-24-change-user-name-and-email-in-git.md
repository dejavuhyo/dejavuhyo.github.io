---
title: Git에서 사용자 name과 email 변경
date: 2021-05-24 06:00:00 +0900
categories: [DevOps, Git]
tags: [git-change-name, git-change-email, git-config, 깃-계정-이름-변경, 깃-계정-이메일-변경, 깃-설정]
---

## 1. Git Bash 실행

![git-bash](/assets/img/2021-05-24-change-user-name-and-email-in-git/git-bash.png)

## 2. 설정 정보 확인

![git-config](/assets/img/2021-05-24-change-user-name-and-email-in-git/git-config.png)

### 1) System 설정 정보

* name

```bash
$ git config --system user.name
```

* email

```bash
$ git config --system user.email
```

### 2) Global 설정 정보

* name

```bash
$ git config --global user.name
```

* email

```bash
$ git config --global user.email
```

### 3) Local 설정 정보

* name

```bash
$ git config --local user.name
```

* email

```bash
$ git config --local user.email
```

## 3. 정보 변경

### 1) System 정보 변경

* name

```bash
$ git config --system user.name 계정명
```

* email

```bash
$ git config --system user.email 이메일
```

### 2) Global 정보 변경

* name

```bash
$ git config --global user.name 계정명
```

* email

```bash
$ git config --global user.email 이메일
```

### 3) Local 정보 변경

* name

```bash
$ git config --local user.name 계정명
```

* email

```bash
$ git config --local user.email 이메일
```

## [출처 및 참고]
* <https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration>
* <https://somjang.tistory.com/entry/Git-Git-Bash-%ED%84%B0%EB%AF%B8%EB%84%90-%EA%B3%84%EC%A0%95-%EB%B3%80%EA%B2%BD-%EB%B0%A9%EB%B2%95>
* <https://velog.io/@hanblueblue/Git-user-%EC%A0%95%EB%B3%B4-%EB%B3%80%EA%B2%BD%ED%95%98%EA%B8%B0>
