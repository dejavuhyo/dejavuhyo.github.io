---
title: Spring YML, YAML
author: dejavuhyo
date: 2021-11-15 06:00:00 +0900
categories: [Framework, Spring]
tags: [yaml, yaml-syntax, spring-yml, yml, spring-yaml, yml-syntax, spring-yaml, 야믈, 야믈-문법, 야믈-사용법]
---

## 1. YML, YAML이란
YAML은 XML, C, 파이썬, 펄, RFC2822에서 정의된 e-mail 양식에서 개념을 얻어 만들어진 '사람이 쉽게 읽을 수 있는' 데이터 직렬화 양식이다. 2001년에 클라크 에반스가 고안했고, Ingy dot Net 및 Oren Ben-Kiki와 함께 디자인했다.

YAML이라는 이름은 "YAML은 마크업 언어가 아니다 (YAML Ain't Markup Language)" 라는 재귀적인 이름에서 유래되었다. 원래 YAML의 뜻은 "또 다른 마크업 언어 (Yet Another Markup Language)"였으나, YAML의 핵심은 문서 마크업이 아닌 데이터 중심에 있다는 것을 보여주기 위해 이름을 바꾸었다. 오늘날 XML과 JSON이 데이터 직렬화에 주로 쓰이기 시작하면서, 많은 사람들이 YAML을 '가벼운 마크업 언어'로 사용하려 하고 있다.

## 2. 요소
YAML은 모든 데이터를 리스트, 해쉬, 스칼라 데이터의 조합으로 적절히 표현할 수 있다는 믿음을 가지고 만들어졌다. 문법은 상대적으로 이해하기 쉽고, 가독성이 좋도록 디자인되었으며, 고급 컴퓨터 언어에 적합하다. 또한 들여쓰기 및 XML의 특수기호를 사용하기 때문에, XML과 거의 비슷하다.

* YAML 문자열은 UTF-8 또는 UTF-16과 같이 출력 가능한 유니코드 문자집합을 이용한다.

* 공백 문자를 이용한 들여쓰기로 구조체를 구분한다. 그러나 탭문자를 들여쓰기에 사용하지 않는다.

* 리스트 요소는 여러 줄에 쓸 때에는 하이픈(`-`)으로 시작하는 한 줄에 하나의 요소를 표현하며, 한 줄에 모아 쓸 때에는 대괄호(`[]`)를 이용하며 쉼표로 각 요소를 구분한다.

* 해쉬는 콜론 기호를 이용해서 키:값의 형태로 한 줄에 하나를 표현하거나, 한 줄에 모아 쓸 때에는 중괄호(`{}`)를 이용하며 쉼표로 각 요소를 구분한다.

* 간단한 값(스칼라 값)은 보통 아무 표시를 하지 않으나 따옴표(`""`)나 작은 따옴표(`''`)를 이용해 둘러쌀 수 있다.
따옴표 안에서 특수 문자는 C언어 스타일(역슬래쉬키와 함께쓰이는 제어문자 예. `\n`)로 표시한다.

* 블록 값은 보존(`|`) 또는 접기(`>`)의 선택 지시자로 나눈다.

* 하나의 스트림에 있는 여러 개의 문서는 하이픈 3개(`---`)로 나누며, 마침표 세개(`...`)로 스트림의 끝을 나타낸다.
반복되는 노드는 기본적으로 `&`를 통해 나타내며, `*` 문자 이후의 내용을 참조한다.

* 주석은 `#`으로 표시하며, 한 줄이 끝날 때까지 유효하다.

* 노드들은 타입과 느낌표로 시작해 URI 주소를 지시하는 태그를 통해 라벨이 붙는다.

* YAML 문서는 `%` 문자로 시작되는 몇 개의 지시자를 통해 특정 작업을 수행한다. YAML 1.1에서는 두 개의 지시자가 정의되어 있다.
  - `%YAML` 지시자는 주어진 문서의 YAML 버전을 나타내는데 사용한다.
  - `%TAG` 지시자는 URI 주소를 나타내는데 주로 사용하며, 이들 주소는 노드 타입 태그에 사용한다.

## 3. 기본문법

### 1) 들여쓰기 (indent)
들여쓰기는 기본적으로 2칸 또는 4칸을 지원한다.

* 2칸 들여쓰기 (추천)

```yml
person:
  name: Chungsub Kim
  job: Developer
  skills:
    - docker
    - kubernetes
```

* 4칸 들여쓰기

```yml
person:
    name: Chungsub Kim
    job: Developer
    skills:
        - docker
        - kubernetes
```

### 2) 데이터 정의 (map)
데이터는 `key: value` 형식으로 정의한다.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: echo
  labels:
    type: app
```

### 3) 배열 정의 (array)
배열은 `-`로 표시한다.

```yml
person:
  name: Chungsub Kim
  job: Developer
  skills:
    - docker
    - kubernetes
```

### 4) 주석 (comment)
주석은 `#`으로 표시한다.

```yml
# comment
person:
  name: Chungsub Kim
  job: Developer
  skills:
    - docker
    - kubernetes
```

### 5) 참/거짓, 숫자표현
참/거짓은 true, false외에 yes, no를 지원한다.

* 참/거짓

```yml
study_hard: yes
give_up: no
hello: True
world: TRUE
manual: false
```

* 숫자

```yml
# number
version: 1.2

# string
version: "1.2"
```

### 6) 줄바꿈 (newline)
여러 줄을 표현하는 방법이다.

* `|` 지시어는 마지막 줄바꿈이 포함

```yml
newlines_sample: |
            number one line

            second line

            last line
```

* `|-` 지시어는 마지막 줄바꿈을 제외

```yml
newlines_sample: |-
            number one line

            second line

            last line
```

* `>` 지시어는 중간에 들어간 빈줄을 제외

```yml
newlines_sample: >
            number one line

            second line

            last line
```

## 4. 주의사항

### 1) 띄어쓰기
key와 value 사이에는 반드시 빈칸이 필요하다.

```yml
# error (not key-value, string)
key:value

# ok
key: value
```

### 2) 문자열 따옴표
대부분의 문자열을 따옴표 없이 사용할 수 있지만 `:`가 들어간 경우는 반드시 따옴표가 필요하다.

```yml
# error
windows_drive: c:

# ok
windows_drive: "c:"
windows_drive: 'c:'
```

## 5. 참고 사이트

### 1) JSON to YAML

* <https://www.json2yaml.com/>

![json2yaml](/assets/img/2021-11-15-yaml/json2yaml.png)


### 2) YAML 문법 체크

* <http://www.yamllint.com/>

![yaml-lint](/assets/img/2021-11-15-yaml/yaml-lint.png)

## [출처 및 참고]
* [https://ko.wikipedia.org/wiki/YAML](https://ko.wikipedia.org/wiki/YAML)
* [https://yaml.org/](https://yaml.org/)
* [https://www.json2yaml.com/](https://www.json2yaml.com/)
* [http://www.yamllint.com/](http://www.yamllint.com/)
