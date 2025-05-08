---
title: IntelliJ 저평가된 단축키
author: dejavuhyo
date: 2024-01-22 13:00:00 +0900
categories: [DevOps, IDE]
tags: [intellij-shortcuts, intellij-keymap, 인텔리제이-단축키, 인텔리제이-키맵]
---

## 1. 사용하지 않는 모든 탭 닫기

* `⌥ x` 또는 `Alt+x`

에디터에 열린 탭이 너무 많을 경우 단축키 하나와 클릭 한 번만으로 해결할 수 있다. 작업 중인 탭에서 `⌥`(macOS) 또는 `Alt`(Windows/Linux)를 누르고 `x`를 클릭하면 된다. 이 액션을 통해 IDE에서 사용하지 않는 모든 탭을 한 번에 닫고 현재 작업 중인 탭은 열린 상태로 남겨둘 수 있다.

![close-tabs](/assets/img/2024-01-22-intellij-underrated-shortcuts/close-tabs.gif)

## 2. 붙여넣을 콘텐츠 선택

* `⇧⌘V` 또는 `Shift+Ctrl+V`

IntelliJ IDEA를 사용하면 최근 복사한 여러 항목을 클립보드에 보관하고 `⇧⌘V`(macOS) 또는 `Shift+Ctrl+V`(Windows/Linux)를 눌러 붙여넣을 수 있다. 즉, 최근에 복사한 코드 조각이 사라질 염려가 없다.

![copy-recent](/assets/img/2024-01-22-intellij-underrated-shortcuts/copy-recent.gif)

## 3. Hippie 코드 완성 사용

* `⌥/` 또는 `Alt+/`

컨텍스트에 따라 적절한 코드를 제안하는 IntelliJ IDEA의 코드 완성 메커니즘을 잘 알고 있을 것이다. 하지만 컨텍스트에 관계없이 현재 파일이나 프로젝트에서 이전에 사용된 단어를 다시 사용해야 하는 경우도 있다.

이러한 경우 [Hippie 코드 완성](https://www.jetbrains.com/help/idea/auto-completing-code.html?_gl=1*1oz11rb*_ga*NDYyMzE0MDI3LjE3MDQ5NDk0NjI.*_ga_9J976DJZ68*MTcwNTA0NTg0NC4zLjEuMTcwNTA0NjU1NC4wLjAuMA..&_ga=2.189157035.1848338358.1705041703-462314027.1704949462#hippie_completion) 기능이 유용하다. 캐럿을 원하는 위치에 놓고 `⌥/`(macOS) 또는 `Alt+/`(Windows/Linux)를 눌러 이 기능을 사용할 수 있다.

![hippie-completion](/assets/img/2024-01-22-intellij-underrated-shortcuts/hippie-completion.gif)

## 4. 코드 서식 다시 지정

* `⌥⌘L` 또는 `Ctrl+Alt+L`

팀에서 업무를 진행할 때 일반적으로 통일된 코드 서식을 공유한다. 따라서 사용하는 코딩 규칙이 프로젝트의 서식과 일치하지 않을 경우 설정에 따라 다시 서식을 지정하려는 코드를 선택하고 `⌥⌘L`(macOS) 또는 `Ctrl+Alt+L`(Windows/Linux)을 누르면 된다.

![format-code](/assets/img/2024-01-22-intellij-underrated-shortcuts/format-code.gif)

## 5. 파일 구조 탐색

* `⌘F12` 또는 `Ctrl+F12`

이 단축키는 파일에 사용된 메서드에 빠르게 액세스하기 위한 다른 방식을 찾는 경우 적합하다. 텍스트를 검색하는 대신 `⌘F12`(macOS) 또는 `Ctrl+F12`(Windows/Linux)를 눌러 파일 구조를 열 수 있다. 다음으로 구조 창이 표시되면 메서드 이름을 입력하고 위/아래 화살표를 사용하여 적절한 메서드 항목으로 이동한 후 Enter 키를 누르면 된다.

![navigate-file-structure](/assets/img/2024-01-22-intellij-underrated-shortcuts/navigate-file-structure.gif)

## 6. 찾은 항목 여러 개 선택

* `⌃G` 또는 `Alt+J`

이 단축키는 동일한 코드 조각을 빠르게 선택하고 편집해야 하는 경우 적합한 옵션이다. 필요한 심볼에 캐럿을 두고 `⌃G`(macOS) 또는 `Alt+J`(Windows/Linux)를 누른다. 다음으로 코드 요소가 강조 표시되면 해당 단축키를 계속 눌러 파일에서 이동할 수 있다. 이렇게 하면 캐럿이 여러 곳에 위치하게 되고, 강조 표시된 코드 요소의 모든 인스턴스 옆에 캐럿이 놓여진 것을 알 수 있다. 강조 표시된 코드를 편집하면 다른 모든 인스턴스에서도 해당 코드가 자동으로 변경된다.

![multiple-pieces-of-code](/assets/img/2024-01-22-intellij-underrated-shortcuts/multiple-pieces-of-code.gif)

## 7. 줄 이동

* `⌘⇧↑/↓` 또는 `Alt+Shift+↑/↓`

이 단축키는 간단하지만 매우 효과적이다. `⌘⇧`(macOS) 또는 `Alt+Shift`(Windows/Linux)를 누르고 `위/아래 화살표 키`를 사용하여 현재 캐럿이 표시된 줄을 원하는 위치로 이동할 수 있다.

그러나 이 단축키 사용 시 줄이 구문 범위를 벗어날 수 있으므로 주의해야 한다.

실수로 줄이 구문 외부로 이동하는 것이 염려되는 경우 구문 이동 단축키인 `⇧⌘↑/↓`(macOS) 또는 `Ctrl+Shift+↑/↓`(Windows/Linux)를 사용한다.

![move-lines](/assets/img/2024-01-22-intellij-underrated-shortcuts/move-lines.gif)

## 8. 줄 복제

* `⌘D` 또는 `Ctrl+D`

이 단축키는 매개변수가 다르지만 비슷한 줄을 여러 개 추가해야 할 경우 유용하다. 단축키를 사용하려면 복제하려는 줄에 캐럿을 놓고 `⌘D`(macOS) 또는 `Ctrl+D`(Windows/Linux)를 누르면 된다.

![dublicate-line](/assets/img/2024-01-22-intellij-underrated-shortcuts/dublicate-line.gif)

## 9. 연상 기호 북마크 추가

* `⌥ F3` 또는 `Ctrl+F11`

폴더, 파일, 프로젝트 항목 및 코드 줄에 빠르게 액세스하기 위해 숫자(0~9) 또는 문자(A~Z)로 북마크를 지정할 수 있다. 북마크가 지정되면 IDE 여백에 연상 기호가 표시된다.

코드 줄에 북마크를 추가하려면 해당 줄에 캐럿을 놓고 `⌥ F3`(macOs) 또는 `Ctrl+F11`(Windows)을 누른다. 파일, 패키지, 폴더 또는 모듈을 북마크로 추가하려면 *Project(프로젝트)* 목록에서 필요한 항목을 선택하고 동일한 단축키를 누르면 된다.

다음으로 팝업에서 북마크 식별자로 사용할 숫자나 문자를 선택하면 북마크가 추가된다.

`^`(MacOs) 또는 `Ctrl`(Windows)을 누른 상태에서 키보드에 지정된 숫자나 문자를 누르면 북마크로 이동할 수 있다.

![mnemonic-bookmark](/assets/img/2024-01-22-intellij-underrated-shortcuts/mnemonic-bookmark.gif)

## 10. 뷰 모드 변경

* ```text ^` ``` 또는 ```text Ctrl+` ```

IntelliJ IDEA는 코딩 중 개인의 요구사항에 맞는 다양한 뷰 모드를 제공한다. *Presentation Mode(프레젠테이션 모드)*, *Distraction Free Mode(집중력 분산 방지 모드)*, *Full Screen(전체 화면)* 및 *Zen Mode(Zen 모드)* 등의 뷰 모드가 있다. 뷰 모드 활성화 방법은 다음과 같다.

* ```text ^` ```(macOS) 또는 ```text Ctrl+` ```(Windows/Linux)를 누른다.

* *Switch(전환) 대화상자*가 표시되면 `5`를 누른다.

* 위/아래 화살표 키를 사용하여 원하는 모드를 선택하거나 모드에 해당되는 번호를 누른다.

디폴트 뷰로 돌아가려면 동일한 단계를 반복한다.

저희 팀에서 자주 사용하는 모드는 Distraction Free Mode(집중력 분산 방지 모드)이다. 이 모드를 사용하면 모든 UI 요소가 숨김 처리되며 소스 코드가 중앙에 유지되어 업무에 100% 집중할 수 있다.

![distraction-free](/assets/img/2024-01-22-intellij-underrated-shortcuts/distraction-free.gif)

## 11. 줄:열로 이동

* `⌘L` 또는 `Ctrl+G`

이 단축키는 코드에서 빠르게 이동해야 할 정확한 위치를 알고 있을 경우 적합하다. `⌘L`(macOS) 또는 `Ctrl+G`(Windows/Linux)를 누르면 *Go to Line:Column(줄:열로 이동)* 대화상자가 열린다. 원하는 줄 또는 열을 숫자로 입력하거나 콜론(:)으로 구분하여 줄과 열을 모두 입력할 수도 있다. OK(확인)을 클릭하면 원하는 위치로 이동한다.

![go-to-line](/assets/img/2024-01-22-intellij-underrated-shortcuts/go-to-line.gif)

## 12. 코드 블록 접기 및 펼치기

* `⌘-/+` 또는 `Ctrl -/+`

이 단축키를 사용하면 파일 구조가 더욱 명확하게 표시되며, 클래스의 특정 부분으로 빠르게 이동할 수 있다. `⌘-`(macOS) 또는 `Ctrl + –`(Windows/Linux)를 누르면 확장 가능한 모든 코드 블록을 접을 수 있다. 코드 블록을 다시 펼치려면 `⌘` 또는 `Ctrl`과 함께 `+` 키를 누르면 된다.

![collapse-code](/assets/img/2024-01-22-intellij-underrated-shortcuts/collapse-code.gif)

## 13. 사진을 배경으로 추가하는 액션 찾기

* `⌘⇧A` 또는 `Ctrl+Shift+A`

이 단축키를 누르면 IDE에서 다양하게 활용할 수 있는 여러 액션을 불러올 수 있다. IDE를 개인 취향에 맞게 귀엽게 꾸밀 수 있는 한 가지 액션을 소개한다.

코딩 작업 중 좋아하는 사람, 동물, 사물 또는 풍경을 보고 싶다면 간단하게 배경 사진을 설정 해본다.

`⌘⇧A`(macOS) 또는 `Ctrl+Shift+A`(Windows 및 Linux)를 눌러 `Find Actions(액션 찾기)` 대화상자가 표시되면 'background image'를 입력한다. 다음으로 표시된 대화상자에서 사진의 경로를 선택하고 기타 설정을 원하는 대로 구성한다. 예를 들어, 이미지의 불투명도도 바꿀 수 있다.

일반 에디터 배경으로 돌아가려면 이전 대화상자에서 `Clear and Close(지운 후 닫기)` 버튼을 클릭하면 된다.

![set-photo](/assets/img/2024-01-22-intellij-underrated-shortcuts/set-photo.gif)

## [출처 및 참고]
* [https://blog.jetbrains.com/ko/idea/2022/11/top-underrated-shortcuts/](https://blog.jetbrains.com/ko/idea/2022/11/top-underrated-shortcuts/)
