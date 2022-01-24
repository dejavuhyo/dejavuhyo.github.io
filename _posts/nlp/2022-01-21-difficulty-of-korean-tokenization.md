---
title: 한국어 토큰화의 어려움
author: dejavuhyo
date: 2022-01-21 09:40:00 +0900
categories: [Language, NLP]
tags: [korean-tokenization, tokenization, difficulty-korean-tokenization, nlp, 토큰화, 한국어-토큰화, 한국어-토큰화-어려움, 자연어-처리, 자연어]
---

## 1. 한국어에서의 토큰화의 어려움
영어는 New York과 같은 합성어나 he's와 같이 줄임말에 대한 예외처리만 한다면, 띄어쓰기(whitespace)를 기준으로 하는 띄어쓰기 토큰화를 수행해도 단어 토큰화가 잘 작동한다.

거의 대부분의 경우에서 단어 단위로 띄어쓰기가 이루어지기 때문에 띄어쓰기 토큰화와 단어 토큰화가 거의 같기 때문이다.

하지만 한국어는 영어와는 달리 띄어쓰기만으로는 토큰화를 하기에 부족하다. 한국어의 경우에는 띄어쓰기 단위가 되는 단위를 '어절'이라고 하는데 어절 토큰화는 한국어 NLP에서 지양되고 있다.

어절 토큰화와 단어 토큰화는 같지 않기 때문이다. 근본적인 이유는 한국어가 영어와는 다른 형태를 가지는 언어인 교착어라는 점에서 기인한다. 교착어란 조사, 어미 등을 붙여서 말을 만드는 언어를 말한다.

### 1) 교착어의 특성
영어와는 달리 한국어에는 조사라는 것이 존재한다.

예를 들어 한국어에 그(he/him)라는 주어나 목적어가 들어간 문장이 있다고 가정한다. 이 경우, 그라는 단어 하나에도 '그가', '그에게', '그를', '그와', '그는'과 같이 다양한 조사가 '그'라는 글자 뒤에 띄어쓰기 없이 바로 붙게된다.

자연어 처리를 하다보면 같은 단어임에도 서로 다른 조사가 붙어서 다른 단어로 인식이 되면 자연어 처리가 힘들고 번거로워지는 경우가 많다. 대부분의 한국어 NLP에서 조사는 분리해줄 필요가 있다.

띄어쓰기 단위가 영어처럼 독립적인 단어라면 띄어쓰기 단위로 토큰화를 하면 되겠지만, 한국어는 어절이 독립적인 단어로 구성되는 것이 아니라 조사 등의 무언가가 붙어있는 경우가 많아서 이를 전부 분리해줘야 한다는 의미이다.

한국어 토큰화에서는 형태소(morpheme)란 개념을 반드시 이해해야 한다. 형태소(morpheme)란 뜻을 가진 가장 작은 말의 단위를 말한다. 이 형태소에는 두 가지 형태소가 있는데 자립 형태소와 의존 형태소이다.

* __자립 형태소:__ 접사, 어미, 조사와 상관없이 자립하여 사용할 수 있는 형태소. 그 자체로 단어가 된다. 체언(명사, 대명사, 수사), 수식언(관형사, 부사), 감탄사 등이 있다.

* __의존 형태소:__ 다른 형태소와 결합하여 사용되는 형태소. 접사, 어미, 조사, 어간을 말한다.

예를 들어 다음과 같은 문장이 있다고 가정한다.

```text
에디가 책을 읽었다.
```

이 문장을 띄어쓰기 단위 토큰화를 수행한다면 다음과 같은 결과를 얻는다.

```text
['에디가', '책을', '읽었다']
```

하지만 이를 형태소 단위로 분해하면 다음과 같다.

```text
자립 형태소: 에디, 책
의존 형태소: -가, -을, 읽-, -었, -다
```

'에디'라는 사람 이름과 '책'이라는 명사를 얻어낼 수 있다.

이를 통해 한국어에서 영어에서의 단어 토큰화와 유사한 형태를 얻으려면 어절 토큰화가 아니라 형태소 토큰화를 수행해야한다는 것이다.

### 2) 한국어는 띄어쓰기가 영어보다 잘 지켜지지 않는다
사용하는 한국어 코퍼스가 뉴스 기사와 같이 띄어쓰기를 철저하게 지키려고 노력하는 글이라면 좋겠지만, 많은 경우에 띄어쓰기가 틀렸거나 지켜지지 않는 코퍼스가 많다.

한국어는 영어권 언어와 비교하여 띄어쓰기가 어렵고 잘 지켜지지 않는 경향이 있다.

그 이유는 여러 견해가 있으나, 가장 기본적인 견해는 한국어의 경우 띄어쓰기가 지켜지지 않아도 글을 쉽게 이해할 수 있는 언어라는 점이다.

띄어쓰기가 없던 한국어에 띄어쓰기가 보편화된 것도 근대(1933년, 한글맞춤법통일안)의 일이다. 띄어쓰기를 전혀 하지 않은 한국어와 영어 두 가지 경우 이다.

* 예1: 제가이렇게띄어쓰기를전혀하지않고글을썼다고하더라도글을이해할수있습니다.

* 예2: Tobeornottobethatisthequestion

영어의 경우에는 띄어쓰기를 하지 않으면 손쉽게 알아보기 어려운 문장들이 생긴다. 이는 한국어(모아쓰기 방식)와 영어(풀어쓰기 방식)라는 언어적 특성의 차이에 기인한다.

결론적으로 한국어는 수많은 코퍼스에서 띄어쓰기가 무시되는 경우가 많아 자연어 처리가 어려워졌다는 것이다.

## 2. 품사 태깅(Part-of-speech tagging)
단어는 표기는 같지만 품사에 따라서 단어의 의미가 달라지기도 한다. 예를 들어서 영어 단어 'fly'는 동사로는 '날다'라는 의미를 갖지만, 명사로는 '파리'라는 의미를 갖고있다.

한국어도 마찬가지이다. '못'이라는 단어는 명사로서는 망치를 사용해서 목재 따위를 고정하는 물건을 의미한다. 하지만 부사로서의 '못'은 '먹는다', '달린다'와 같은 동작 동사를 할 수 없다는 의미로 쓰인다.

결국 단어의 의미를 제대로 파악하기 위해서는 해당 단어가 어떤 품사로 쓰였는지 보는 것이 주요 지표가 될 수도 있다. 그에 따라 단어 토큰화 과정에서 각 단어가 어떤 품사로 쓰였는지를 구분해놓기도 하는데, 이 작업을 품사 태깅(part-of-speech tagging)이라고 한다.

## 3. NLTK와 KoNLPy를 이용한 영어, 한국어 토큰화
NLTK에서는 Penn Treebank POS Tags라는 기준을 사용하여 품사를 태깅한다.

* 입력

```text
"I am actively looking for Ph.D. students. and you are a Ph.D. student."
```

* 단어 토큰화

```text
['I', 'am', 'actively', 'looking', 'for', 'Ph.D.', 'students', '.', 'and', 'you', 'are', 'a', 'Ph.D.', 'student', '.']
```

* 품사 태깅

```text
[('I', 'PRP'), ('am', 'VBP'), ('actively', 'RB'), ('looking', 'VBG'), ('for', 'IN'), ('Ph.D.', 'NNP'), ('students', 'NNS'), ('.', '.'), ('and', 'CC'), ('you', 'PRP'), ('are', 'VBP'), ('a', 'DT'), ('Ph.D.', 'NNP'), ('student', 'NN'), ('.', '.')]
```

영어 문장에 대해서 토큰화를 수행한 결과를 입력으로 품사 태깅을 수행하였다.

Penn Treebank POG Tags에서 PRP는 인칭 대명사, VBP는 동사, RB는 부사, VBG는 현재부사, IN은 전치사, NNP는 고유 명사, NNS는 복수형 명사, CC는 접속사, DT는 관사를 의미한다.

한국어 자연어 처리를 위해서는 KoNLPy(코엔엘파이)라는 파이썬 패키지를 사용할 수 있다. 코엔엘파이를 통해서 사용할 수 있는 형태소 분석기로 Okt(Open Korea Text), 메캅(Mecab), 코모란(Komoran), 한나눔(Hannanum), 꼬꼬마(Kkma)가 있다.

한국어 NLP에서 형태소 분석기를 사용하여 단어 토큰화, 더 정확히는 형태소 토큰화(morpheme tokenization)를 수행한다. 여기서는 Okt와 꼬꼬마 두 개의 형태소 분석기를 사용하였다.

* 입력

```text
"열심히 코딩한 당신, 연휴에는 여행을 가봐요"
```

* OKT 형태소 분석

```text
['열심히', '코딩', '한', '당신', ',', '연휴', '에는', '여행', '을', '가봐요']
```

* OKT 품사 태깅

```text
[('열심히', 'Adverb'), ('코딩', 'Noun'), ('한', 'Josa'), ('당신', 'Noun'), (',', 'Punctuation'), ('연휴', 'Noun'), ('에는', 'Josa'), ('여행', 'Noun'), ('을', 'Josa'), ('가봐요', 'Verb')]
```

* OKT 명사 추출

```text
['코딩', '당신', '연휴', '여행']
```

위 예제는 Okt 형태소 분석기로 토큰화를 시도해본 예제이다. 각각의 메소드는 아래와 같은 기능을 갖고 있다.

* morphs: 형태소 추출

* pos: 품사 태깅(Part-of-speech tagging)

* nouns: 명사 추출

코엔엘파이의 형태소 분석기들은 공통적으로 이 메소드들을 제공하고 있다. 위 예제에서 형태소 추출과 품사 태깅 메소드의 결과를 보면 조사를 기본적으로 분리하고 있음을 확인할 수 있다.

한국어 NLP에서 전처리에 형태소 분석기를 사용하는 것은 굉장히 유용하다.

이번에는 꼬꼬마 형태소 분석기를 사용하여 같은 문장에 대해서 토큰화를 진행한다.

* 입력

```text
"열심히 코딩한 당신, 연휴에는 여행을 가봐요"
```

* 꼬꼬마 형태소 분석

```text
['열심히', '코딩', '하', 'ㄴ', '당신', ',', '연휴', '에', '는', '여행', '을', '가보', '아요']
```

* 꼬꼬마 품사 태깅

```text
[('열심히', 'MAG'), ('코딩', 'NNG'), ('하', 'XSV'), ('ㄴ', 'ETD'), ('당신', 'NP'), (',', 'SP'), ('연휴', 'NNG'), ('에', 'JKM'), ('는', 'JX'), ('여행', 'NNG'), ('을', 'JKO'), ('가보', 'VV'), ('아요', 'EFN')]
```

* 꼬꼬마 명사 추출

```text
['코딩', '당신', '연휴', '여행']
```

앞서 사용한 Okt 형태소 분석기와 결과가 다른 것을 볼 수 있다. 각 형태소 분석기는 성능과 결과가 다르게 나오기 때문에, 형태소 분석기의 선택은 사용하고자 하는 필요 용도에 어떤 형태소 분석기가 가장 적절한지를 판단하고 사용하면 된다.

## [출처 및 참고]
* <https://wikidocs.net/21698>