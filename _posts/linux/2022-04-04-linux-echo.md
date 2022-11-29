---
title: Linux echo
author: dejavuhyo
date: 2022-04-04 19:50:00 +0900
categories: [DevOps, Linux]
tags: [linux-echo, echo, 리눅스-echo, 리눅스-문자열]
---

## 1. echo
문자열이나 변수를 출력한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:-----:|:-----:|
| -n | 개행을 하지 않음 |
| -e | 이스케이프 문자를 허용 |
| -E | 이스케이프 문자를 허용하지 않음(디폴트) |

## 3. 사용예제

### 1) 개행없이 문자를 출력

```shell
$ echo -n "Hello"
Hello$
```

### 2) 문자를 지정한 색상으로 출력
-e 옵션과 함게 사용하여 문자를 지정한 색상으로 출력할 수 있다. 원래 색상으로 출력하려면 지정한 색상의 마지막에 다시 원래의 색상으로 돌려주면 된다.

색상은 아래 링크를 참고

* <https://gist.github.com/vratiu/9780109>

* <https://gist.github.com/daytonn/8677243>

```shell
RED='\033[0;31m'
GREEN='\033[0;32m'
NC='\033[0m'  # Color off

echo -e ${RED}RED${NC}
echo -e ${GREEN}GREEN${NC}

vText="abc"
echo -e ${RED}${vText}${NC}
```

## [출처 및 참고]
* <https://wikidocs.net/32941>
