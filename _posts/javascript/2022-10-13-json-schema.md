---
title: JSON 스키마
author: dejavuhyo
date: 2022-10-13 09:15:00 +0900
categories: [Language, JavaScript]
tags: [json-schema, json, json-validation, json-validation-keywords, json-스키마, json-검증, json-검증-키워드]
---

## 1. JSON 스키마(schema)
JSON은 좀 더 쉽게 데이터를 교환하고 저장하기 위하여 만들어진 데이터 교환 표준이다. 이때 JSON 데이터를 전송받는 측에서는 전송받은 데이터가 적법한 형식의 데이터인지를 확인할 방법이 필요하다.

따라서 적법한 JSON 데이터의 형식을 기술한 문서를 JSON 스키마(schema)라고 한다.

JSON 스키마에 대한 더 자세한 사항은 [링크](http://json-schema.org)를 참고한다.

## 2. JSON 스키마 검증(validation)
JSON 스키마는 다음과 같은 세 가지 검증 과정을 거친다.

① 데이터의 타입이 정확한가?

② 필수로 받아와야 하는 데이터가 포함되어 있는가?

③ 데이터가 원하는 범위 안에 있는가?

JSON 스키마에서는 위와 같은 검증 기준을 모두 키워드(keyword)를 이용하여 직접 명시할 수 있다.

## 3. 검증 키워드(validation keywords)
JSON 스키마에서는 검증 기준을 명시하기 위해 여러 키워드를 사용할 수 있다. 이러한 키워드를 사용하여 데이터에 여러 가지 조건을 걸어줄 수 있다.

JSON 스키마에서 사용할 수 있는 대표적인 검증 키워드는 다음과 같다.

| 검증 키워드 | 설명 |
|:-----:|:-----:|
| type | 유효한 데이터의 타입을 명시함 |
| properties | 유효한 데이터 이름과 값의 쌍들을 명시함 |
| required | 명시한 배열의 모든 요소를 프로퍼티로 가지고 있어야만 유효함 |
| minimum | 최솟값 이상의 숫자만 유효함 |
| maximum | 최댓값 이하의 숫자만 유효함 |
| multipleOf | 명시한 숫자의 배수만 유효함 |
| maxLength | 명시한 최대 길이 이하의 문자열만 유효함 |
| minLength | 명시한 최소 길이 이상의 문자열만 유효함 |
| pattern | 명시한 정규 표현식에 해당하는 문자열만 유효함 |

또한, 스키마에 대한 정보를 나타내는 메타 데이터(metadata) 키워드는 다음과 같다.

* title

* description

* default

다음 예제는 검증 키워드와 메타 데이터 키워드를 이용한 JSON 스키마 예제이다.

```json
{
    "title": "강아지 스키마",
    "description": "이 스키마는 강아지에 관한 데이터를 검증하기 위해 작성된 스키마임.",
    "type": "object",
    "properties": {
        "name": {
            "type": "string"
        },
        "family": {
            "type": "string"
        },
        "age": {
            "type": "integer"
        },
        "weight": {
            "type": "number"
        },
        "owner": {
            "type": "object",
            "properties": {
                "ownerName": {
                    "type": "string"
                },
                "phone": {
                    "type": "string"
                }
            }
        }
    }
}
```

type 키워드를 사용하여 유효한 타입을 명시하면, 해당 데이터가 유효한지를 검사할 수 있다. 이때 사용할 수 있는 타입에는 JSON의 기본 타입을 모두 사용할 수 있다.

properties 키워드를 사용하면, 해당 객체가 가지는 프로퍼티가 유효한지를 검사할 수 있다.

## [출처 및 참고]
* [http://www.tcpschool.com/json/json_schema_schema](http://www.tcpschool.com/json/json_schema_schema)
