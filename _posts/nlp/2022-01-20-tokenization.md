---
title: 토큰화
author: dejavuhyo
date: 2022-01-20 09:45:00 +0900
categories: [Language, NLP]
tags: [tokenization, text-preprocessing, nlp, 토큰화, 텍스트-전처리, 자연어-처리, 자연어]
---

## 1. 토큰화(Tokenization)
자연어 처리에서 크롤링 등으로 얻어낸 코퍼스 데이터가 필요에 맞게 전처리되지 않은 상태라면, 해당 데이터를 사용하고자하는 용도에 맞게 토큰화(tokenization) & 정제(cleaning) & 정규화(normalization)하는 일을 하게 된다.

주어진 코퍼스(corpus)에서 토큰(token)이라 불리는 단위로 나누는 작업을 토큰화(tokenization)라고 한다. 토큰의 단위가 상황에 따라 다르지만, 보통 의미있는 단위로 토큰을 정의한다.

## 2. 단어 토큰화(Word Tokenization)
토큰의 기준을 단어(word)로 하는 경우, 단어 토큰화(word tokenization)라고 한다. 다만, 여기서 단어(word)는 단어 단위 외에도 단어구, 의미를 갖는 문자열로도 간주되기도 한다.

예를 들어보면, 아래의 입력으로부터 구두점(punctuation)과 같은 문자는 제외시키는 간단한 단어 토큰화 작업한다. 구두점이란 마침표(`.`), 콤마(`,`), 물음표(`?`), 세미콜론(`;`), 느낌표(`!`) 등과 같은 기호를 말한다.

```text
Time is an illusion. Lunchtime double so!
```

이러한 입력으로부터 구두점을 제외시킨 토큰화 작업의 결과는 다음과 같다.

```text
"Time", "is", "an", "illustion", "Lunchtime", "double", "so"
```

이 예제에서 토큰화 작업은 굉장히 간단gk하다. 구두점을 지운 뒤에 띄어쓰기(whitespace)를 기준으로 잘라냈다. 하지만 이 예제는 토큰화의 가장 기초적인 예제를 보여준 것에 불과하다.

보통 토큰화 작업은 단순히 구두점이나 특수문자를 전부 제거하는 정제(cleaning) 작업을 수행하는 것만으로 해결되지 않는다. 구두점이나 특수문자를 전부 제거하면 토큰이 의미를 잃어버리는 경우가 발생하기도 한니다. 심지어 띄어쓰기 단위로 자르면 사실상 단어 토큰이 구분되는 영어와 달리, 한국어는 띄어쓰기만으로는 단어 토큰을 구분하기 어렵다.

## 3. 토큰화 중 생기는 선택의 순간
토큰화를 하다보면, 예상하지 못한 경우가 있어서 토큰화의 기준을 생각해봐야 하는 경우가 발생한다. 물론, 이러한 선택은 해당 데이터를 가지고 어떤 용도로 사용할 것인지에 따라서 그 용도에 영향이 없는 기준으로 정하면 된다.

예를 들어 영어권 언어에서 아포스트로피를(')가 들어가있는 단어는 어떻게 토큰으로 분류해야 하는지에 대한 선택의 문제이다.

다음과 같은 문장이 있다.

```text
Don't be fooled by the dark sounding name, Mr. Jone's Orphanage is as cheery as cheery goes for a pastry shop.
```

아포스트로피가 들어간 상황에서 Don't와 Jone's는 어떻게 토큰화할 수 있는지 다양한 선택지가 있다.

* Don't

* Don t

* Dont

* Do n't

* Jone's

* Jone s

* Jone

* Jones

이 중 사용자가 원하는 결과가 나오도록 토큰화 도구를 직접 설계할 수도 있겠지만, 기존에 공개된 도구들을 사용하였을 때의 결과가 사용자의 목적과 일치한다면 해당 도구를 사용할 수도 있을 것이다.

NLTK는 영어 코퍼스를 토큰화하기 위한 도구들을 제공한다. 그 중 word_tokenize와 WordPunctTokenizer를 사용해서 아포스트로피를 어떻게 처리하는지 확인해본다.

* word_tokenize

```text
['Do', "n't", 'be', 'fooled', 'by', 'the', 'dark', 'sounding', 'name', ',', 'Mr.', 'Jone', "'s", 'Orphanage', 'is', 'as', 'cheery', 'as', 'cheery', 'goes', 'for', 'a', 'pastry', 'shop', '.']
```

word_tokenize는 Don't를 Do와 n't로 분리하였으며, 반면 Jone's는 Jone과 's로 분리한 것을 확인할 수 있다.

* wordPunctTokenizer

```text
['Don', "'", 't', 'be', 'fooled', 'by', 'the', 'dark', 'sounding', 'name', ',', 'Mr', '.', 'Jone', "'", 's', 'Orphanage', 'is', 'as', 'cheery', 'as', 'cheery', 'goes', 'for', 'a', 'pastry', 'shop', '.']
```

WordPunctTokenizer는 구두점을 별도로 분류하는 특징을 갖고 있기 때문에, 앞서 확인했던 word_tokenize와는 달리 Don't를 Don과 '와 t로 분리하였으며, 이와 마찬가지로 Jone's를 Jone과 '와 s로 분리한 것을 확인할 수 있다.

* 케라스 text_to_word_sequence

```text
["don't", 'be', 'fooled', 'by', 'the', 'dark', 'sounding', 'name', 'mr', "jone's", 'orphanage', 'is', 'as', 'cheery', 'as', 'cheery', 'goes', 'for', 'a', 'pastry', 'shop']
```

케라스의 text_to_word_sequence는 기본적으로 모든 알파벳을 소문자로 바꾸면서 마침표나 컴마, 느낌표 등의 구두점을 제거한다. 하지만 don't나 jone's와 같은 경우 아포스트로피는 보존하는 것을 볼 수 있다.

## 4. 토큰화에서 고려해야할 사항
토큰화 작업을 단순하게 코퍼스에서 구두점을 제외하고 공백 기준으로 잘라내는 작업이라고 간주할 수는 없다. 이러한 일은 보다 섬세한 알고리즘이 필요하다.

### 1) 구두점이나 특수 문자를 단순 제외해서는 안 된다.
갖고있는 코퍼스에서 단어들을 걸러낼 때, 구두점이나 특수 문자를 단순히 제외하는 것은 옳지 않다.

코퍼스에 대한 정제 작업을 진행하다보면, 구두점조차도 하나의 토큰으로 분류하기도 한다. 가장 기본적인 예를 들어보자면, 마침표(`.`)와 같은 경우는 문장의 경계를 알 수 있는데 도움이 되므로 단어를 뽑아낼 때, 마침표(`.`)를 제외하지 않을 수 있다.

또 다른 예로 단어 자체에 구두점을 갖고 있는 경우도 있는데, m.p.h나 Ph.D나 AT&T 같은 경우가 있다.

또 특수 문자의 달러(`$`)나 슬래시(`/`)로 예를 들어보면, `$45.55`와 같은 가격을 의미 하기도 하고, `01/02/06`은 날짜를 의미하기도 한다. 보통 이런 경우 45.55를 하나로 취급하고 45와 55로 따로 분류하고 싶지는 않을 수 있다.

숫자 사이에 콤마(`,`)가 들어가는 경우도 있다. 보통 수치를 표현할 때는 123,456,789와 같이 세 자리 단위로 콤마가 있다.

### 2) 줄임말과 단어 내에 띄어쓰기가 있는 경우
토큰화 작업에서 종종 영어권 언어의 아포스트로피(`'`)는 압축된 단어를 다시 펼치는 역할을 하기도 한다.

예를 들어 what're는 what are의 줄임말이며, we're는 we are의 줄임말이다. 위의 예에서 re를 접어(clitic)이라고 한다. 즉, 단어가 줄임말로 쓰일 때 생기는 형태를 말한다. 가령 I am을 줄인 I'm이 있을 때, m을 접어라고 한다.

New York이라는 단어나 rock 'n' roll 이라는 단어를 보면, 이 단어들은 하나의 단어이지만 중간에 띄어쓰기가 존재한다. 사용 용도에 따라서, 하나의 단어 사이에 띄어쓰기가 있는 경우에도 하나의 토큰으로 봐야하는 경우도 있을 수 있으므로, 토큰화 작업은 저러한 단어를 하나로 인식할 수 있는 능력도 가져야한다.

### 3) 표준 토큰화 예제
이해를 돕기 위해 표준으로 쓰이고 있는 토큰화 방법 중 하나인 Penn Treebank Tokenization를 사용하여 토큰화의 결과를 확인해본다.

* 규칙1: 하이푼으로 구성된 단어는 하나로 유지한다.

* 규칙2: doesn't와 같이 아포스트로피로 '접어'가 함께하는 단어는 분리해준다.

해당 표준에 아래의 문장을 입력으로 넣어본다.

* 입력

```text
"Starting a home-based restaurant may be an ideal. it doesn't have a food chain or restaurant of their own."
```

* 트리뱅크 워드토크나이저

```text
['Starting', 'a', 'home-based', 'restaurant', 'may', 'be', 'an', 'ideal.', 'it', 'does', "n't", 'have', 'a', 'food', 'chain', 'or', 'restaurant', 'of', 'their', 'own', '.']
```

결과를 보면, 각각 규칙1과 규칙2에 따라서 home-based는 하나의 토큰으로 취급하고 있으며, dosen't의 경우 does와 n't는 분리되었음을 볼 수 있다.

## 5. 문장 토큰화(Sentence Tokenization)
토큰의 단위가 문장(sentence)일 경우이다. 이 작업은 갖고있는 코퍼스 내에서 문장 단위로 구분하는 작업으로 때로는 문장 분류(sentence segmentation)라고도 부른다.

보통 갖고있는 코퍼스가 정제되지 않은 상태라면, 코퍼스는 문장 단위로 구분되어 있지 않아서 이를 사용하고자 하는 용도에 맞게 문장 토큰화가 필요할 수 있다.

직관적으로 생각해봤을 때는 물음표(`?`)나 마침표(`.`)나 느낌표(`!`) 기준으로 문장을 잘라내면 되지 않을까라고 생각할 수 있지만, 꼭 그렇지만은 않다. `!`나 `?`는 문장의 구분을 위한 꽤 명확한 구분자(boundary) 역할을 하지만 마침표는 그렇지 않기 때문이다. 마침표는 문장의 끝이 아니더라도 등장할 수 있다.

* 예1: IP 192.168.56.31 서버에 들어가서 로그 파일 저장해서 aaa@gmail.com로 결과 좀 보내줘. 그 후 점심 먹으러 가자.

* 예2: Since I'm actively looking for Ph.D. students, I get the same question a dozen times every year.

예를 들어 위의 예제에 마침표를 기준으로 문장 토큰화를 적용해본다.

첫번째 예제에서는 보내줘.에서 그리고 두번째 예제에서는 year.에서 처음으로 문장이 끝난 것으로 인식하는 것이 제대로 문장의 끝을 예측했다고 볼 수 있다. 하지만 단순히 마침표(.)로 문장을 구분짓는다고 가정하면, 문장의 끝이 나오기 전에 이미 마침표가 여러번 등장하여 예상한 결과가 나오지 않게 된다.

사용하는 코퍼스가 어떤 국적의 언어인지, 또는 해당 코퍼스 내에서 특수문자들이 어떻게 사용되고 있는지에 따라서 직접 규칙들을 정의해볼 수 있다.

100% 정확도를 얻는 일은 쉬운 일이 아닌데, 갖고있는 코퍼스 데이터에 오타나, 문장의 구성이 엉망이라면 정해놓은 규칙이 소용이 없을 수 있기 때문이다.

NLTK에서는 영어 문장의 토큰화를 수행하는 sent_tokenize를 지원하고 있다.

* 입력

```text
"His barber kept his word. But keeping such a huge secret to himself was driving him crazy. Finally, the barber went up a mountain and almost to the edge of a cliff. He dug a hole in the midst of some reeds. He looked about, to make sure no one was near."
```

* 토큰화

```text
['His barber kept his word.', 'But keeping such a huge secret to himself was driving him crazy.', 'Finally, the barber went up a mountain and almost to the edge of a cliff.', 'He dug a hole in the midst of some reeds.', 'He looked about, to make sure no one was near.']
```

위 코드는 text에 저장된 여러 개의 문장들로부터 문장을 구분하는 코드이다. 출력 결과를 보면 성공적으로 모든 문장을 구분해내었음을 볼 수 있다.

이번에는 문장 중간에 마침표가 다수 등장하는 경우이다.

* 입력

```text
"I am actively looking for Ph.D. students. and you are a Ph.D student."
```

* 토큰화

```text
['I am actively looking for Ph.D. students.', 'and you are a Ph.D student.']
```

NLTK는 단순히 마침표를 구분자로 하여 문장을 구분하지 않았기 때문에, Ph.D.를 문장 내의 단어로 인식하여 성공적으로 인식하는 것을 볼 수 있다.

한국어에 대한 문장 토큰화 도구 또한 존재한다. 한국어의 경우에는 박상길님이 개발한 KSS(Korean Sentence Splitter)를 추천한다.

KSS를 통해서 문장 토큰화를 진행한다.

* 입력

```text
'딥 러닝 자연어 처리가 재미있기는 합니다. 그런데 문제는 영어보다 한국어로 할 때 너무 어렵습니다. 이제 해보면 알걸요?'
```

* 토큰화

```text
['딥 러닝 자연어 처리가 재미있기는 합니다.', '그런데 문제는 영어보다 한국어로 할 때 너무 어렵습니다.', '이제 해보면 알걸요?']
```

출력 결과는 정상적으로 모든 문장이 분리된 결과를 보여준다.

## [출처 및 참고]
* [https://wikidocs.net/21698](https://wikidocs.net/21698)
