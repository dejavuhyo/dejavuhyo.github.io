---
title: GitHub Repository에 Secrets 등록 방법
author: dejavuhyo
date: 2026-05-21 09:20:00 +0900
categories: [GitOps, Git]
tags: [github, github-secrets, github-repository, github-repository-secrets]
---

![github-logo](/assets/img/2026-05-21-github-repository-register-secrets/github-logo.png)

## 1. Secrets 등록
GitHub Repository에 보안 데이터(API Key, 비밀번호 등)를 안전하게 저장하고 GitHub Actions 등에서 사용할 수 있도록 Secrets(시크릿)를 등록하는 방법이다.

### 1) GitHub 저장소 이동
Secrets를 등록할 GitHub Repository 화면으로 이동한다.

### 2) Settings(설정) 메뉴
Repository 상단 메뉴 탭의 `Settings`를 클릭한다.

### 3) Secrets and variables 메뉴

* `Secrets and variables` → `Actions`

> `GitHub Actions`가 아니라 `Dependabot`이나 `Codespaces`에서 쓸 것이라면 해당 메뉴를 선택한다.

### 4) New repository secret 생성
`New repository secret`을 클릭한다.

![github-settings](/assets/img/2026-05-21-github-repository-register-secrets/github-settings.png)

### 5) Name 및 Secret 입력

* Name: 변수명을 입력 (GitHub Actions에서 호출할 이름)
  - 예) `DATABASE_PASSWORD`, `AWS_ACCESS_KEY_ID`
  - 규칙: 영문 대문자와 언더바(_)를 조합해 직관적으로 짓는 것이 관례이다. 숫자로 시작할 수 없다.

* Value: 노출되면 안 되는 비밀번호나 API 키 값을 입력

## 2. 사용 방법
등록한 Secret은 Workflow YAML 파일(`.github/workflows/*.yml`)에서 `${{ secrets.변수명 }}` 형태로 가져와 사용할 수 있다.

* 사용 예제

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Run a script using Secret
      env:
        # Repository에 등록한 Name을 그대로 적어준다.
        MY_API_KEY: ${{ secrets.MY_API_KEY }} 
      run: |
        echo "API 키를 환경변수로 전달하여 스크립트를 실행한다."
        python my_script.py
```

## 3. 주의사항

### 1) 한 번 저장하면 값 확인 불가
보안을 위해 한 번 저장된 Secret 값은 GitHub UI에서 다시 확인할 수 없다. (수정하거나 삭제하는 것만 가능)

값을 분실했다면 기존 Secret을 삭제하고 새 값으로 업데이트해야 한다.

### 2) 로그 마스킹
GitHub Actions 실행 로그에 이 Secret 값이 출력되려고 하면 GitHub가 자동으로 `***` 형태로 마스킹 처리한다.

완벽하지 않을 수 있으므로 로그에 의도적으로 출력하는 코드는 피하는 것이 좋다.

## [출처 및 참고]
* [https://docs.github.com/ko/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets](https://docs.github.com/ko/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets)
