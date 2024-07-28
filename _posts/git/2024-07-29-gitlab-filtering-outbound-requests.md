---
title: GitLab Filtering outbound requests
author: dejavuhyo
date: 2024-07-29 08:27:00 +0900
categories: [DevOps, Git]
tags: [gitlab, gitlab-filtering, filtering-outbound-requests, gitlab-webhooks, 깃랩-웹훅, 깃랩-필터링, 아웃바운드-요청-필터링]
---

gitlab-filtering-outbound-requests.md

## 1. 아웃바운드 요청 필터링
데이터 손실 및 노출 위험을 방지하기 위해 GitLab 관리자는 이제 아웃바운드 요청 필터링 컨트롤을 사용하여 GitLab 인스턴스에서 이루어지는 특정 아웃바운드 요청을 제한할 수 있다.

## 2. Secure webhooks and integrations
최소한 유지 관리자 역할을 가진 사용자는 프로젝트나 그룹에서 특정 변경 사항이 발생할 때 트리거되는 웹훅을 설정할 수 있다. 트리거되면 `POST` HTTP 요청이 URL로 전송된다. 웹훅은 일반적으로 데이터를 특정 외부 웹 서비스로 보내도록 구성되며, 이 웹 서비스는 적절한 방식으로 데이터를 처리한다.

그러나 웹훅은 외부 웹 서비스 대신 내부 웹 서비스의 URL로 구성될 수 있다. 웹훅이 트리거되면 GitLab 서버 또는 로컬 네트워크에서 실행되는 비 GitLab 웹 서비스가 악용될 수 있다.

웹훅 요청은 GitLab 서버 자체에서 이루어지며 인증을 위해 다음 대신 후크당 하나의 선택적 비밀 토큰을 사용한다.

* 사용자 토큰

* 저장소별 토큰

결과적으로 이러한 요청은 의도한 것보다 더 광범위한 액세스 권한을 가질 수 있으며, 여기에는 웹훅을 호스팅하는 서버에서 실행되는 모든 항목에 대한 액세스가 포함된다.

* GitLab 서버

* API 자체

* 일부 웹훅의 경우, 해당 웹훅 서버의 로컬 네트워크에 있는 다른 서버에 대한 네트워크 액세스가 가능하다. 이러한 서비스가 외부에서 보호되고 접근할 수 없는 경우에도 마찬가지이다.

웹훅은 인증이 필요 없는 웹 서비스를 사용하여 파괴적인 명령을 트리거하는데 사용할 수 있다. 이러한 웹훅은 GitLab 서버가 POST리소스를 삭제하는 엔드포인트에 HTTP 요청을 하도록 할 수 있다.

## 3. Prevent requests to the local network from system hooks
필수 조건:

* 인스턴스에 관리자 액세스 권한이 있어야 한다.

안전하지 않은 내부 웹 서비스의 악용을 방지하기 위해 다음 로컬 네트워크 주소에 대한 모든 웹훅 및 통합 요청은 허용되지 않는다.

* 현재 GitLab 인스턴스 서버 주소이다.

* 개인 네트워크 주소에는 `127.0.0.1`, `::1`, `0.0.0.0`, `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, 및 IPv6 사이트 로컬(`ffc0::/10`) 주소가 포함된다.

이 주소에 대한 액세스를 허용하려면:

* 왼쪽 사이드바 하단에서 `Admin area`을 선택한다.

* `Settings > Network`를 선택한다.

* `Outbound requests`을 확장한다.

* `Allow requests to the local network from webhooks and integrations`를 선택한다.

![filtering-outbound-requests](/assets/img/2024-07-29-gitlab-filtering-outbound-requests/filtering-outbound-requests.png)

## 4. Enforce DNS rebinding attack protection
필수 조건:

* 인스턴스에 관리자 액세스 권한이 있어야 한다.

[DNS 리바인딩](https://en.wikipedia.org/wiki/DNS_rebinding)은 로컬 네트워크 액세스 제한을 우회하기 위해 악성 도메인 이름을 내부 네트워크 리소스로 리졸브하는 기술이다. GitLab은 이 공격에 대한 보호 기능을 기본적으로 활성화했다. 이 보호 기능을 비활성화하려면:

* 왼쪽 사이드바 하단에서 `Admin area`을 선택한다.

* `Settings > Network`를 선택한다.

* `Outbound requests`을 확장한다.

* `Enforce DNS-rebinding attack protection`을 선택 취소한다.

## [출처 및 참고]
* [https://docs.gitlab.com/ee/security/webhooks.html](https://docs.gitlab.com/ee/security/webhooks.html)
