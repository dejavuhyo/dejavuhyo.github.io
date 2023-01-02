---
title: IntelliJ Javadoc 생성
author: dejavuhyo
date: 2021-09-06 06:00:00 +0900
categories: [Application, IDE]
tags: [intellij-javadoc, intellij-generate-javadoc, generate-javadoc, intellij-javadoc-생성, javadoc-생성]
---

## 1. JavaDoc 메뉴 선택

* Tools → Generate JavaDoc

![menu](/assets/img/2021-09-06-intellij-generate-javadoc/menu.png)

참조를 생성할 파일 또는 디렉토리 세트인 범위를 선택하고 생성된 문서를 저장할 출력 디렉토리를 설정한다.

## 2. 옵션 선택
슬라이더를 사용하여 생성된 설명서에 포함될 구성원의 가시성 수준을 정의한다. 다음 옵션 중 하나를 선택한다.

* **Private:** 모든 클래스 및 멤버를 참조에 포함한다.

* **Package:** private을 제외한 모든 클래스와 멤버를 포함한다.

* **Protected:** public 및 protected 클래스와 멤버를 포함한다.

* **Public:** public 클래스와 멤버만 포함할 수 있다.

## 3. 인코딩 설정 및 생성

* Other command line arguments에 추가: `-encoding UTF-8 -docencoding UTF-8 -charset UTF-8`

![generate-javadoc](/assets/img/2021-09-06-intellij-generate-javadoc/generate-javadoc.png)

## 4. Javadoc 생성 옵션 설명
JavaDoc 생성 대화상자는 JavaDoc 유틸리티를 호출한다. 대화상자의 컨트롤은 이 유틸리티의 옵션 및 태그에 해당한다.

* **Generate Javadoc scope:** 이 영역에서는 JavaDoc을 생성할 파일, 폴더 및 패키지의 하위 집합을 지정합니다. 이 범위는 전체 프로젝트, 최근에 수정된 파일, 현재 파일, 사용자 지정 범위 등이 될 수 있습니다.

* **Include test sources:** 생성된 JavaDoc에 대한 테스트를 위한 문서 주석을 포함한다.

* **Include JDK and library sources in -sourcepath:** 이 확인란을 선택하면 JDK 및 라이브러리 원본에 대한 경로가 JavaDoc 유틸리티로 전달된다.

* **Include link to JDK documentation:** 이 확인란을 선택하면 JDK의 클래스 및 패키지에 대한 참조가 연결로 바뀌며, 이는 JavaDoc 유틸리티의 `-link` 옵션을 사용하는 것과 일치한다. 이 확인란은 SDK 설정의 Documentation Paths 탭에 온라인 설명서에 대한 링크가 지정된 경우에만 사용할 수 있다.

* **Output directory:** 생성된 문서가 저장될 디렉토리의 정규화된 경로를 지정한다. 경로를 수동으로 입력하거나 찾아보기 버튼을 클릭하고 대화 상자에서 위치를 선택한다. 지정된 값은 JavaDoc 유틸리티의 `-d` 매개변수로 전달된다. 지정한 디렉토리가 시스템에 없는 경우 작성하라는 메시지가 표시된다.

  > 출력 디렉터리를 지정하지 않으면 OK(확인) 버튼을 사용할 수 없다.

* **Slider:** 생성된 문서에 포함할 구성원의 가시성 수준을 지정한다.
  - Private: 모든 클래스 및 멤버를 포함하려면 이 수준을 선택한다. 이 수준은 `-private` JavaDoc 매개변수에 해당한다.
  - Package: 비공개 클래스를 제외한 모든 클래스 및 멤버를 포함하려면 이 수준을 선택한다. 수준은 -package JavaDoc 매개변수에 해당한다.
  - Protected: public 및 protected 클래스와 멤버를 포함하려면 이 수준을 선택한다. 수준은 -protected JavaDoc 매개변수에 해당한다.
  - Public: public 클래스 및 멤버만 포함하려면 이 수준을 선택한다. 수준은 -public JavaDoc 매개변수에 해당한다.

* **Generate hierarchy tree:** 클래스 계층을 생성한다. 이 확인란의 선택을 취소하면 `-notree` 매개변수가 JavaDoc으로 전달된다.

* **Generate navigator bar:** 네비게이터 막대를 생성한다. 이 확인란의 선택을 취소하면 `-nonavbar` 매개변수가 JavaDoc으로 전달된다.

* **Generate index:** 문서 색인을 생성한다. 이 확인란의 선택을 취소하면 `-noindex` 매개변수가 JavaDoc으로 전달된다.

* **Separate index per letter:** 각 문자에 대해 별도의 인덱스 파일을 생성한다. 이 확인란의 선택을 취소하면 `-splitindex` 매개변수가 JavaDoc으로 전달된다. 이 확인란은 인덱스 생성 확인란을 선택한 경우에만 사용할 수 있다.

* **@use:** 클래스 및 패키지의 사용을 문서화한다. 이 확인란을 선택하면 `-use` JavaDoc 매개변수에 해당한다.

* **@author:** @author 단락을 포함한다. 이 확인란을 선택하면 `-author` JavaDoc 매개변수에 해당한다.

* **@version:** @version 문단을 포함한다. 이 항목을 선택하면 `-version` JavaDoc 매개변수에 해당한다.

* **@deprecated:** @deprecated 정보를 포함한다. 확인란의 선택을 취소하면 `-nodeprecated` 매개변수가 JavaDoc으로 전달된다.

* **deprecated list:** 사용되지 않는 목록을 생성한다. 확인란의 선택을 취소하면 `-nodeprecatedlist` 매개변수가 JavaDoc으로 전달된다. 이 확인란은 @deprecated 확인란을 선택한 경우에만 사용할 수 있다.

* **Locale:** 원하는 로케일을 입력한다..

* **Other command line arguments:** JavaDoc에 전달할 추가 인수를 입력한다. 명령줄 구문을 사용한다.

* **Maximum heap size (Mb):** JavaDoc을 실행하기 위해 Java VM에서 사용할 최대 힙 크기(Mb)를 입력한다.

* **Open generated documentation in browser:** 생성된 JavaDoc을 브라우저에서 자동으로 연다.

## [출처 및 참고]
* [https://www.jetbrains.com/help/idea/working-with-code-documentation.html#view-javadocs](https://www.jetbrains.com/help/idea/working-with-code-documentation.html#view-javadocs)
