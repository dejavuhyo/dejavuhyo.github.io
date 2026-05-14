---
title: Ubuntu에 Helm 설치
author: dejavuhyo
date: 2026-05-14 11:00:00 +0900
categories: [GitOps, Helm]
tags: [helm, helm-install, 헬름, 헬름-설치]
---

![kubernetes-logo](/assets/img/2026-05-14-helm-install-ubuntu/helm-logo.svg)

## 1. 방법1 - APT를 이용한 설치
업데이트 관리가 용이하여 가장 많이 사용되는 방식이다.

### 1) Helm GPG 키 가져오기

```bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
```

### 2) apt-transport-https 설치

```bash
sudo apt-get install apt-transport-https --yes
```

### 3) 저장소 추가

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
```

### 4) 설치 진행

```bash
sudo apt-get update
sudo apt-get install helm
```

## 2. 방법2 - 스크립트를 이용한 자동 설치
GPG 키 등록 단계에서 계속 막힌다면, 시스템 복잡도가 낮은 스크립트 방식을 추천한다. 이 방식은 키 등록 과정을 내부적으로 알아서 처리한다.

복잡한 과정 없이 한 줄의 명령어로 최신 버전을 설치하고 싶을 때 유용하다.

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

## 3. 설치 확인
설치가 완료되었다면 아래 명령어를 입력하여 버전을 확인한다.

```bash
helm version
```

## 4. Heml 삭제

### 1) APT 패키지 매니저로 설치한 경우
가장 일반적인 삭제 방법이다.

* Helm만 삭제

```bash
sudo apt-get remove helm
```

*설정 파일까지 모두 삭제

```bash
sudo apt-get purge helm
sudo apt-get autoremove
```

### 2) Snap으로 설치한 경우

```bash
sudo snap remove helm
```

### 3) 바이너리 파일을 직접 설치한 경우
스크립트나 직접 다운로드로 설치했다면, 실행 파일인 helm 위치를 찾아 직접 삭제해야 한다.

```bash
which helm
```

> 보통 `/usr/local/bin/helm`에 위치한다.

```bash
sudo rm $(which helm)
```

### 4) 로컬 설정 및 캐시 데이터 삭제 (공통)
Helm 소프트웨어를 지워도 사용자가 사용했던 차트 캐시, 저장소 정보, 설정 파일들은 그대로 남아 있다.

이를 완전히 지우려면 아래 디렉토리들을 삭제한다.

```bash
# Helm 관련 설정 및 데이터 삭제
rm -rf ~/.config/helm
rm -rf ~/.cache/helm
rm -rf ~/.local/share/helm
```

### 5) 삭제 확인
`-bash: /usr/local/bin/helm: No such file or directory` 메시지가 나오면 성공적으로 삭제된 것이다.

```bash
helm version
```

## [출처 및 참고]
* [https://helm.sh/docs/intro/install](https://helm.sh/docs/intro/install)
* [https://github.com/helm/helm](https://github.com/helm/helm)
