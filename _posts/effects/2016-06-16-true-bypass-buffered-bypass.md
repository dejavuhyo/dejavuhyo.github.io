---
title: 트루 바이패스와 버퍼 바이패스
author: dejavuhyo
date: 2016-06-16 18:29:00 +0900
categories: [Hobby, Effects]
tags: [true-bypass, buffered-bypass, 트루-바이패스, 버퍼-바이패스]
---

## 1. True Bypass
트루 바이패스는 페달이 바이패스되거나 '꺼졌을'때 신호 경로가 입력 잭에서 출력 잭으로 직접 전달되는 것을 의미하는 모든 배선 방식이다. 따라서 잡음이 발생하지 않고 건전지가 다 소모되었어도 Off 상태에서는 아무 영향이 없다.

![true-bypass](/assets/img/2016-06-16-true-bypass-buffered-bypass/true-bypass.png)

## 2. Buffered Bypass
버퍼드 바이패스는 페달의 메인 회로가 활성화되어 있든 아니든 회로의 일부가 신호 경로에 남아 있는 것이다.

이 경우 Board를 통해 흐르는 신호가 Hum으로 나타나게 되며, 여러 이펙터를 직렬 연결하였을 경우 Hum이 증폭되어 잡음이 많이 들어가게 된다. 또한 건전지가 다 소모되었을 경우 Board 를 통해 흐르는 신호가 잡음이 되어 연주 중 갑작스러운 잡음의 증가가 일어 날 수 있다.

![buffered-bypass](/assets/img/2016-06-16-true-bypass-buffered-bypass/buffered-bypass.png)

만약,

* 사용하고 있는 모든 이펙터가 True Bypass 이고

* 이펙터를 10개 사용하고 있으며

* 기타에서 이펙터 보드까지 5m 선

* 이펙터 사이에는 각각 30cm 선

* 그리고 이펙터 보드에서 앰프까지 5m 선을 사용하고 있다고 가정할 경우,

기타의 낮은 출력이 12.7m 길이의 케이블과 + 수m의 이펙터 내부 전선 길이를 지나야 하고 또한 22개의 Connector를 지나야 하는데, 임피던스가 너무 낮아 지는 문제가 발생한다.

## 3. 해결책
5m 간격으로 True Bypass가 아닌 이펙터를 사용하는 방법이 있다.

True Bypass 방식이 아닌 경우 출력은 On 상태이든 Off 상태이든 적정 출력으로 이펙터에서 만들어 주므로 임피던스가 높아져서 신호의 열화가 적다. 반면 True Bypass만 있을 경우 작은 신호는 긴 케이블과 커넥터들의 저항에 의해서 사라지고 잡음으로 변하게 되는 것이다.

4개째 마다 True Bypass가 아닌 이펙터를 사용하면서 나머지는 모두 True Bypass 제품을 사용하면 잡음을 최소화 하면서 임피던스 문제를 해결하실 수 있다.

다른 방법은 True Bypass만 사용하되 직렬하여 사용하는 이펙터의 수를 4개 이하로 줄이고, 더 많이 사용할 경우 Channel을 분리하여 사용하며, Cable 길이를 최소화 (예를 들면 3m 짜리를 사용) 하는 것이다.

## [출처 및 참고]
* [https://www.schoolmusic.co.kr/kin/?var=view&question_no=3543](https://www.schoolmusic.co.kr/kin/?var=view&question_no=3543)
* [https://www.guitarworld.com/features/true-bypass-vs-buffered-bypass](https://www.guitarworld.com/features/true-bypass-vs-buffered-bypass)
