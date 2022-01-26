---
title: indexOf를 사용하여 문자열에서 단어의 모든 발생 찾기
author: dejavuhyo
date: 2021-12-28 09:25:00 +0900
categories: [Language, Java]
tags: [indexof-find-string, java-find-string, find-string-occurrences, java-indexof, indexof, string-occurrences, java-string-occurrences, 자바-indexof, 문자열-단어-발생, 단어-발생-찾기, 자바-단어-발생-찾기, 자바-단어-찾기]
---

## 1. indexOf
더 큰 텍스트 문자열에서 문자 패턴 또는 단어를 검색하는 일은 다양한 분야에서 수행된다. 예를 들어 생물정보학에서는 염색체에서 DNA 조각을 찾아야 할 수도 있다.

미디어에서 편집자는 방대한 텍스트에서 특정 문구를 찾는다. 데이터 감시는 데이터에 포함된 의심스러운 단어를 찾아 사기 또는 스팸을 감지한다.

어떤 맥락에서든 검색은 너무 잘 알려져 있고 벅찬 일이기 때문에 일반적으로 "건초 더미 문제의 바늘" 이라고 불린다. 문자열 내에서 단어의 모든 발생을 찾기 위해 Java String 클래스의 indexOf(String str, int fromIndex) 메서드를 사용한다.

## 2. 간단한 알고리즘
단순히 더 큰 텍스트에서 단어의 출현을 계산하는 대신, 알고리즘은 텍스트에서 특정 단어가 존재하는 모든 위치를 찾아 식별한다. 문제에 대한 접근 방식은 다음과 같다.

* 검색은 텍스트의 단어 내에서도 단어를 찾는다. 따라서 "able"이라는 단어를 검색하면 "comfortable" 및 "tablet"에서 찾을 수 있다.

* 검색은 대소문자를 구분하지 않는다.

* 알고리즘은 순진한 문자열 검색 접근 방식을 기반으로 한다. 이것은 단어와 텍스트 문자열에 있는 문자의 특성에 대해 순진하기 때문에 무차별 대입을 사용하여 검색 단어의 인스턴스에 대해 텍스트의 모든 위치를 확인한다는 것을 의미한다.

### 1) 구현
검색 매개변수를 정의했으므로 간단한 솔루션을 작성한다.

```java
public class WordIndexer {

    public List<Integer> findWord(String textString, String word) {
        List<Integer> indexes = new ArrayList<Integer>();
        String lowerCaseTextString = textString.toLowerCase();
        String lowerCaseWord = word.toLowerCase();

        int index = 0;
        while(index != -1){
            index = lowerCaseTextString.indexOf(lowerCaseWord, index);
            if (index != -1) {
                indexes.add(index);
                index++;
            }
        }
        return indexes;
    }
}
```

### 2) 솔루션 테스트
알고리즘을 테스트하기 위해 셰익스피어의 햄릿에 나오는 유명한 구절의 스니펫을 사용하고 다섯 번 나타나는 "or"라는 단어를 검색한다.

```java
@Test
public void givenWord_whenSearching_thenFindAllIndexedLocations() {
    String theString;
    WordIndexer wordIndexer = new WordIndexer();

    theString = "To be, or not to be: that is the question: "
      + "Whether 'tis nobler in the mind to suffer "
      + "The slings and arrows of outrageous fortune, "
      + "Or to take arms against a sea of troubles, "
      + "And by opposing end them? To die: to sleep; "
      + "No more; and by a sleep to say we end "
      + "The heart-ache and the thousand natural shocks "
      + "That flesh is heir to, 'tis a consummation "
      + "Devoutly to be wish'd. To die, to sleep; "
      + "To sleep: perchance to dream: ay, there's the rub: "
      + "For in that sleep of death what dreams may come,";

    List<Integer> expectedResult = Arrays.asList(7, 122, 130, 221, 438);
    List<Integer> actualResult = wordIndexer.findWord(theString, "or");
    assertEquals(expectedResult, actualResult);
}
```

테스트를 실행할 때 예상한 결과를 얻는다. "or"을 검색하면 텍스트 문자열에 다양한 방식으로 포함된 5개의 인스턴스가 생성된다.

```text
index of 7, in "or"
index of 122, in "fortune"
index of 130, in "Or"
index of 221, in "more"
index of 438, in "For"
```

수학적으로 알고리즘은 O(`m*(nm)`) 의 Big-O 표기법을 사용한다. 여기서 m은 단어의 길이 이고 n은 텍스트 문자열의 길이이다. 이 접근 방식은 수천 자의 haystack 텍스트 문자열에 적합할 수 있지만 수십억 개의 문자가 있는 경우 견딜 수 없을 정도로 느리다.

## 3. 향상된 알고리즘
위의 간단한 예는 텍스트 문자열에서 주어진 단어를 검색하는 순진하고 무차별적인 접근 방식을 보여준다. 따라서 모든 검색어와 텍스트에 대해 작동한다.

검색 단어에 ""aa"와 같이 반복적인 문자 패턴이 포함되어 있지 않다는 것을 미리 알면 조금 더 효율적인 알고리즘을 작성할 수 있다.

이 경우 잠재적인 시작 위치로 텍스트 문자열의 모든 위치를 다시 확인하기 위해 백업을 수행하는 것을 안전하게 피할 수 있다. indexOf() 메서드를 호출 한 후 가장 최근에 발견된 항목이 끝난 직후 위치로 이동한다. 이 간단한 조정으로 O(n)의 최상의 시나리오를 얻을 수 있다.

이전 findWord() 메서드의 향상된 버전을 살펴본다.

```java
public List<Integer> findWordUpgrade(String textString, String word) {
    List<Integer> indexes = new ArrayList<Integer>();
    StringBuilder output = new StringBuilder();
    String lowerCaseTextString = textString.toLowerCase();
    String lowerCaseWord = word.toLowerCase();
    int wordLength = 0;

    int index = 0;
    while(index != -1){
        index = lowerCaseTextString.indexOf(lowerCaseWord, index + wordLength);  // Slight improvement
        if (index != -1) {
            indexes.add(index);
        }
        wordLength = word.length();
    }
    return indexes;
}
```

> Java String 클래스의 indexOf() 메소드가 본질적으로 대소문자를 구분한다. 예를 들어 "Bob"과 "bob"를 구별할 수 있다.

> 전체적으로 indexOf()는 하위 문자열 조작에 대한 코딩을 수행하지 않고 텍스트 문자열에 묻힌 문자 시퀀스를 찾는 편리한 방법이다.

## [출처 및 참고]
* <https://www.baeldung.com/java-indexof-find-string-occurrences>
