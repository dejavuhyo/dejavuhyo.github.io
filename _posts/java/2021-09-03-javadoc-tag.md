---
title: Javadoc 태그
author: dejavuhyo
date: 2021-09-03 06:00:00 +0900
categories: [Language, Java]
tags: [javadoc-tag, javadoc-태그]
---

## 1. Javadoc 태그
주석에는 설명문과 Javadoc 태그를 작성할 수 있다. 인수와 반환 값, 예외 및 참고하는 곳의 지정 등 해당 Javadoc 태그와 값이 같이 지정된다.

### 1) @author 태그
`@author` 태그는 클래스, 인터페이스 등에 작성하고, 작성자를 지정하는 데 사용한다.

```text
@author name-text

저자에 관하여 지정한다.

작성 위치: 개요, 패키지, 클래스, 인터페이스
중복 작성: 가능
출력 형식: 작성자
기     타: "-author" 옵션 필요
```

주로 클래스 등의 주석을 작성하고, 저자가 누구인지를 지정할 때에 사용한다. 같은 주석 내에서 여러 번 지정할 수 있다.

```text
/**
 * 주석의 설명문
 * @author devkuma
 * @author kimkc
 */
```

`@author` 태그가 중복으로 작성된 경우는 출력될 때 각각의 저자가 쉼표(,)로 구분하여 한 줄에 모아서 출력된다. Javadoc의 -author 옵션을 지정한 경우에만 출력이 된다.

다음과 같이 실행한다.

```shell
$ javadoc -d doc -author FileName.java
```

### 2) @version 태그
`@version` 태그는 클래스, 인터페이스 등에 작성하고 소프트웨어의 버전을 지정하기 위해 사용한다.

```text
@version version-text

소프트웨어 버전을 지정한다.

작성 위치: 개요, 패키지, 클래스, 인터페이스
중복 작성: 가능
출력 형식: 버전
기     타: "-version" 옵션 필요
```

소프트웨어의 현재 버전을 지정할 때에 사용한다. 같은 주석 내에서 여러 번 지정할 수 있다.

```text
/**
 * 주석의 설명문
 * 
 * @version 1.0
 * @version Project 2.0.1
 */
```

다음과 같이 실행한다.

```shell
$ javadoc -d doc -version FileName.java
```

### 3) @see 태그
`@see` 태그는 관련 항목으로 외부 링크 또는 텍스트를 표시하거나, 다른 필드나 메소드에 대한 모든 참조 링크를 나타낼 때 사용한다.

```text
@see reference

관련 항목으로 텍스트와 링크를 표시

작성 위치: 개요, 패키지, 클래스, 인터페이스, 필드, 메소드
중복 작성: 가능
출력 형식: 관련 항목
```

#### (1) 텍스트, 외부 링크
`@see` 태그는 텍스트 및 외부 링크로의 사용한다. 먼저 관련 항목의 위치 정보를 문자열을 표시하는 경우이다.

```text
@see "string"
```

HTML 문장의 `<a>` 태그와 같은 형식으로 링크 및 레이블을 지정한다. 사용하는 방법은 다음과 같다.

```text
/**
 * 주석의 설명문
 * @see <a href="https://dejavuhyo.github.io">공대베짱이</a>
 */
```

`@see` 태그가 중복으로 작성된 경우는 출력될 때 각각의 링크 레이블이 쉼표(,)로 구분하여 한 줄에 모아서 출력된다.

#### (2) 참조 링크
`@see` 태그의 또 다른 사용법으로 다른 필드나 메소드에 대한 참조 링크를 표시하기 위해 사용한다.

다른 필드나 메소드에 대한 참조 링크를 표시하는 경우이다.

```text
@see package.class#member label
```

package.class#member 형식으로 지정한 다른 메소드에 대한 링크를 만든다. 링크 레이블로 label을 표시하고 있지만 'label'은 선택 사항이다. 생략한 경우는 대상 메소드명으로 표시된다.

```text
/**
 * 주석의 설명문
 * @see Sample08_02#setSize(int, int) setSize
 */
```

위에서는 패키지명을 생략하였다. 이와 같이 패키지명을 생략하거나 클래스명까지 생략하여 작성되었으면 생략된 부분을 다음의 순서로 지정된 이름을 검색한다.

* 현재 클래스 또는 인터페이스

* 외부를 둘러싸고 있는 클래스와 인터페이스 (가장 가까운 것으로부터 검색)

* 슈퍼 클래스와 슈퍼 인터페이스 (가장 가까운 것으로부터 검색)

* 현재 패키지

* import 패키지, 클래스 및 인터페이스 (import 문의 순서에 따라 검색)

항상 전체 이름으로 패키지에서 멤버까지 지정하면 확실하겠지만, 주석이 너무 길어져 버리는 경우가 있다. 생략하게 되면 작성된 주석 부분을 간결하게 작성할 수 있다.

공식 자료에 의하면 다음과 같은 작성 방법이 있다.

현재 클래스의 멤버를 참조한다.

```text
@see #field
@see #method(Type, Type,...)
@see #method(Type argname, Type argname,...)
@see #constructor(Type, Type,...)
@see #constructor(Type argname, Type argname,...)
```

현재 또는 import된 패키지의 다른 클래스를 참조한다.

```text
@see Class#field
@see Class#method(Type, Type,...)
@see Class#method(Type argname, Type argname,...)
@see Class#constructor(Type, Type,...)
@see Class#constructor(Type argname, Type argname,...)
@see Class.NestedClass
@see Class
```

다른 패키지의 요소를 참조한다. (확실)

```text
@see package.Class#field
@see package.Class#method(Type, Type,...)
@see package.Class#method(Type argname, Type argname,...)
@see package.Class#constructor(Type, Type,...)
@see package.Class#constructor(Type argname, Type argname,...)
@see package.Class.NestedClass
@see package.Class
@see package
```

다음과 같이 실행한다.

```shell
$ javadoc -d doc FileName.java
```

### 4) @deprecated 태그
`@deprecated` 태그는 클래스나 메소드 등을 더 이상 사용이 권장하지 않는 경우에 사용한다. 사용이 권장되지 않는다는 것은 사용이 불가능하다는 것은 아니다. 다만 권장을 하지 않고 차후에 없어질 수도 있다는 것을 의미한다.

```text
@deprecated deprecated-text

사용이 권장하지 않는 경우에 지정한다.

작성 위치: 개요, 패키지, 클래스, 인터페이스, 필드, 메소드
중복 작성: 불가능
출력 형식: Deprecated. + 입력한 문자열
```

사용을 권장하지 않게 된 이유 등을 문자열로 작성한다. 사용 방법은 다음과 같다.

```text
/**
 * 주석의 설명문
 * @deprecated 다른 방법으로 대체되었다.
 */
```

또한, 대체하는 메소드나 클래스 등의 링크를 따라 지정한다. 링크 지정은 `{@link}`  태그를 사용한다.

```text
/**
 * 주석의 설명문
 * @deprecated 다른 메소드로 대체되었다 {@link #setScale ()}
 */
```

다음과 같이 실행한다.

```shell
$ javadoc -d doc FileName.java
```

### 5) @since 태그
`@since` 태그 도입된 버전을 표시하는 경우에 사용한다. `@version` 태그는 현재의 버전을 나타내지만 `@since``` 태그는 어떤 버전에서 도입되었는지 나타내는 점이 다르다.

`text
@since since-text

도입 된 버전을 표시한다.

작성 위치: 개요, 패키지, 클래스, 인터페이스, 필드, 메소드
중복 작성: 가능
출력 형식: 도입 된 버전
```

버전 등을 나타내는 문자열을 지정한다. 사용법은 다음과 같다.

```text
/**
 * 주석의 설명문
 * @since 2.5.1
 */
```

`@since` 태그가 중복으로 작성된 경우는 출력될 때 각각의 버전은 쉼표(,)로 구분하여 한 줄에 모아서 출력된다.

다음과 같이 실행한다.

```shell
$ javadoc -d doc FileName.java
```

### 6) @param 태그
`@param` 태그는 매개 변수에 대한 설명을 표시할 때 사용한다.

```text
@param parameter-name description

매개 변수에 대한 설명을 표시한다.

작성 위치: 메소드
중복 작성: 가능
츌력 형식: 매개 변수명 및 설명
```

`@author` 태그가 중복으로 작성된 경우는 출력될 때 각각의 저자가 쉼표(,)로 구분하여 한 줄에 모아서 출력된다.

메소드의 매개 변수명과 매개 변수에 대한 설명을 작성한다. 사용 방법은 다음과 같다.

```text
/**
 * 주석의 설명문
 * @param width 폭
 * @param height 높이
 */
```

설명 부분은 여러 줄에 걸쳐 작성 할 수 있다. 여러 줄에 걸쳐있는 것을 표현하는 방법은 특별한 작성법이 있는 것은 아니다.

```text
/**
 * 댓글의 설명
 * @param width 폭
 * width 대한 설명 2번째 줄
 * width 대한 설명 3번째 줄
 * @param height 높이
 */
```

다음과 같이 실행한다.

```shell
$ javadoc -d doc FileName.java
```

### 7) @return 태그
`@return` 태그는 반환 값에 대한 설명(데이터 유형 및 범위 등)을 표시할 때 사용한다.

```text
@return description

반환 값에 대한 설명을 표시

기술 위치: 메소드
복수 작성: 불가능
출력 형식: 반환 값
```

메소드의 반환 값에 대한 설명을 작성한다. 사용법은 다음과 같다.

```text
/**
 * 주석의 설명문
 * @return 계산한 면적
 */
```

다음과 같이 실행한다.

```shell
$ javadoc -d doc FileName.java
```

### 8) @throws, @exception 태그
`@throws` 태그는 발생할 수 있는 예외에 대한 설명을 표시할 때 사용한다. 그리고 `@exception` 태그와 `@throws` 태그는 태그 이름이 다를 뿐 동일하게 사용한다.

```text
@throws class-name description

예외에 대한 설명을 표시

작성 위치: 메소드
중복 작성: 가능
출력 형식: Throws
```

메소드 내에서 발생하는 예외에 대한 예외 클래스 이름과 설명을 입력한다. 사용 방법은 다음과 같다.

```text
/ **
 * 주석의 설명문
 * 
 * @throws java.io.FileNotFoundException 지정된 파일을 찾을 수 없습니다
 * /
```

클래스 이름은 전체 패키지명 까지 모두 지정되지만, 패키지 이름이 생략된 경우에는 다음의 순서에 따라 검색한다.

* 현재의 클래스 또는 인터페이스

* 외부를 둘러싸고 있는 클래스와 인터페이스 (가장 가까운 것으로부터 검색)

* 슈퍼 클래스와 슈퍼 인터페이스 (가장 가까운 것으로부터 검색)

* 현재 패키지

* import 패키지, 클래스 및 인터페이스 (import 문장의 순서에 따라 검색)

여러 @throws 태그가 지정되는 경우에는 각각이 다른 행으로 표시된다.

다음과 같이 실행한다.

```shell
$ javadoc -d doc FileName.java
```

### 9) {@link}, {@linkplain} 태그
`{@link}` 태그는 다른 Javadoc 태그 중에 참조 링크를 표시할 경우에 사용한다. 지금까지의 태그들은 모두 블록 태그라고 불리는 반면에 이 태그는 인라인 태그라고 한다. 인라인 태그는 `{}`로 묶어 사용하여 주석을 설명문 안이나 다른 블록 태그 안의 문자열의 부분에 사용할 수 있다.

`{@linkplain}` 태그는 `{@link}` 태그와 기본적인 사용법은 동일하다. 다른 Javadoc 태그에서 문자열을 표시할 위치에 참조 링크를 표시할 경우에 사용한다. 다른 점은 `{@link}` 태그를 사용하는 경우 연결 문자열은 코드 텍스트로 표시되는 반면, `{@linkplain}` 태그의 경우는 링크가 된 문자열을 일반 텍스트로 표시되는 점뿐이다.

```text
{@link package.class#member label}

블록 태그 안의 설명문이나 문자열 부분에서 참조 링크 표시

작성 위치: 개요, 패키지, 클래스, 인터페이스, 필드, 메소드
중복 작성: 가능
```

'package.class#member' 형식으로 지정하는 다른 메소드에 대한 링크를 만든다. 링크 레이블에 지정된 'label'이 표시되지만, 'label'은 선택 사항이다. 생략한 경우는 대상 메소드명으로 표시된다. 기본적인 사용법은 `@see` 태그와 동일하다.

```text
/ **
 * 주석의 설명문
 * 다음 메소드 {@link Sample14#setSize(int, int) setSize}를 참조
 * /
```

다음과 같이 실행한다.

```shell
$ javadoc -d doc FileName.java
```

### 10) {@code} 태그
`{@code}` 태그는 Javadoc에 예제 코드 작성시 사용된다.

```text
{@code source-code}

예제 코드 표시

작성 위치: 개요, 패키지, 클래스, 인터페이스, 필드, 메소드
중복 작성: 가능
```

설명에 예제 코드를 첨부할 영에 사용한다. 사용 방법 다음과 같다.

```text
/**
 * 주석의 설명문
 * 
 * <pre>
 * {@code
 * Foo foo = new Foo();
 * foo.foo();
 * }
 * </pre>
 */
```

설명문에 예제 코드를 첨부할 경우 `{@code}` 태그만 사용해도 되지만, 줄 바꿈이 필요할 시에는 HTML `<pre>` 태그를 같이 사용해야 한다.

다음과 같이 실행한다.

```shell
$ javadoc -d doc FileName.java
```

## [출처 및 참고]
* [https://www.devkuma.com/books/pages/1239](https://www.devkuma.com/books/pages/1239)
