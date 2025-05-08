---
title: 가장 많이 사용되는 IntelliJ 단축키
author: dejavuhyo
date: 2020-01-20 17:00:00 +0900
categories: [DevOps, IDE]
tags: [intellij-shortcuts, intellij-keymap, 인텔리제이-단축키, 인텔리제이-키맵]
---

IntelliJ IDEA는 편집, 탐색, 리팩토링, 디버깅 등 일상적으로 수행하는 대부분의 작업에 대하여 해당 명령어의 키보드 단축키를 제공한다. 이러한 단축키를 배우고 연습하여 기억해 두면 작업 흐름을 원활하게 유지하는데 도움이 된다.

## 1. Alt + Enter
가장 많이 알려진 것부터 시작한다. 이 키보드 단축키는 현재 컨텍스트에 대한 액션을 표시하여 뭐든지 수정하는데 사용할 수 있다.

코드에서 오류가 발견된 경우 해당 오류 위에 커서를 놓고 Alt+Enter를 누르면 문제에 대한 수정 제안 목록이 표시된다.

경고와 제안이 표시된 곳에서도 Alt+Enter를 사용하여, 제안 중 하나를 수락할 수 있다.

심지어 오류, 경고 또는 제안이 없는 코드에서도 Alt+Enter를 사용할 수 있다. 이 경우 인텐션 액션(개인적으로 static import를 추가할 때 자주 사용함) 및 활성화되었지만 경고 설정은 안 된 검사가 표시된다.

![img001](/assets/img/2020-01-20-intellij-keymap/img001.gif)

## 2. F2
에디터에서 오류와 경고 사이를 이동할 때 마우스를 사용하고 싶지 않다면, F2를 사용하여 다음 오류, 경고 또는 제안으로 점프할 수 있다. 이것과 Alt+Enter를 함께 사용하여 모든 제안을 표시하고 하나를 선택하거나, Shift+Alt+Enter를 사용해 첫 번째 제안을 적용할 수 있다.

![img002](/assets/img/2020-01-20-intellij-keymap/img002.gif)

## 3. ⌘1 또는 Alt+1
도구 창을 열 때도 마우스가 필요하지 않다. ⌘1(MacOS) 또는 Alt+1(Windows/Linux)를 사용하면 프로젝트 창이 열리고 거기에 포커스가 위치한다. 방향 키를 사용해 트리를 탐색하거나 입력하여 검색할 수 있다.

![img003](/assets/img/2020-01-20-intellij-keymap/img003.gif)

## 4. Esc
에디터에 다시 포커스가 위치하게 하려면 Esc를 누른다. 열린 도구 창과 상관 없이 에디터에 다시 포커스가 위치하게 되어 코드 작업을 계속 수행할 수 있다. 사실, Esc는 변경 사항을 적용하지 않고 팝업 창을 닫을 때도 유용하다.

에디터 안팎으로 이동하는 법에 대한 자세한 팁은 에디터의 기본을 확인한다.

![img004](/assets/img/2020-01-20-intellij-keymap/img004.gif)

## 5. ⌘E 또는 Ctrl+E
프로젝트 창은 원하는 파일로 이동하는 최선의 방법이 아닐 수 있다. ⌘E(MacOS) 또는 Ctrl+E(Windows/Linux)를 사용해 최근 실행했던 파일을 확인할 수 있다. 그러면, 방향 키를 사용해 이동할 수 있는 최근 파일 상자가 표시된다. 또한, 여기서 키보드 단축키가 없는 것을 포함하여 도구 창을 열 수 있다. IntelliJ IDEA의 다른 창에서처럼 여기에서도 입력하여 특정 항목을 검색할 수 있다.

![img005](/assets/img/2020-01-20-intellij-keymap/img005.gif)

## 6. ⌘B 또는 Ctrl+B
코드 안에서 다른 코드로 이동하고 싶을 때가 자주 있다. ⌘B(MacOS) 또는 Ctrl+B(Windows/Linux)를 사용하면 심볼의 선언으로 이동할 수 있다. 예를 들어, 필드 위에서 이 단축키를 누르면 커서가 해당 필드의 선언으로 이동한다. 클래스 이름 위에서 누르면 해당 클래스 파일로 이동한다. ⌥⌘B(MacOS) 또는 Ctrl+Alt+B(Windows/Linux)를 누르면 구현으로 이동할 수 있다.

마지막 두 개의 단축키와 같은 팁을 더 많이 보려면 소스 코드 탐색을 확인한다.

![img006](/assets/img/2020-01-20-intellij-keymap/img006.gif)

## 7. Alt+F7
선언을 검색하는 대신 어떤 항목이 사용된 위치를 검색하고 싶을 때가 자주 있다. Alt+F7을 누르면 어떤 항목이 사용된 모든 곳이 표시된다. 예를 들어, 이 인터페이스 이름 위에서 Alt+F7을 누르면 이 인터페이스를 구현하는 것이 필드 선언이든 클래스이든 상관 없이 해당 인터페이스가 사용된 모든 곳이 검색 창에 표시된다.

![img007](/assets/img/2020-01-20-intellij-keymap/img007.gif)

## 8. Ctrl Ctrl
어디에서든 Ctrl 키를 두 번 눌러서 어떤 항목이든 실행할 수 있다. IDE 안에 있는지의 여부나 열린 파일과 상관 없이 Ctrl을 두 번 누르면 Run Anything 창이 열린다. 기본적으로 이 창에는 최근 실행 구성 목록이 표시된다. 하지만, 항목의 이름을 입력하여 다른 실행 구성을 검색하고 실행할 수도 있다.

![img008](/assets/img/2020-01-20-intellij-keymap/img008.gif)

## 9. ⌥↑ / ⌥↓ 또는 Ctrl + W / Shift + Ctrl + W
⌥ 와 위 또는 아래 화살표(MacOS) 그리고 Ctrl+W 또는 Ctrl+Shift+W(Windows/Linux)를 사용해 커서 근처의 코드 선택 영역을 확대하거나 축소할 수 있다. 선택 영역을 확대할 경우 IntelliJ IDEA가 확대된 영역에서 유효한 다음 식을 자동으로 선택한다. ⌥↓(MacOS) 또는 Ctrl+Shift+W(Windows/Linux)를 사용하면 선택 영역이 다시 커서 위치로 축소된다.

![img009](/assets/img/2020-01-20-intellij-keymap/img009.gif)

## 10. ⌘/ 또는 Ctrl + /
줄 위의 아무 곳에서나 ⌘/(MacOS) 또는 Ctrl+/(Windows/Linux)를 누르면 해당 코드 줄이 줄 주석으로 처리된다. 이미 주석으로 처리된 경우 동일한 단축키를 누르면 해당 줄의 주석 처리가 제거된다.

전체 코드 블록을 선택하면 ⌥⌘/(MacOS) 또는 Shift+Ctrl+/(Windows/Linux)를 사용해 블록 주석을 추가할 수 있다. 코드 블록 안의 아무 곳에서나 커서를 사용해 이 단축키를 다시 누르면 블록 주석이 제거된다.

![img010](/assets/img/2020-01-20-intellij-keymap/img010.gif)

## 11. ⇧⌘⏎ 또는 Shift+Ctrl+Enter
Complete Current Statement(현재 구문 완성), ⇧⌘⏎(MacOS) 또는 Shift+Ctrl+Enter(Windows/Linux)는 코딩을 할 때 가장 유용한 단축키 중 하나이다. 코딩을 할 때 현재 구문 완성을 자주 사용한다면 대부분의 경우 단순히 코드 끝에 세미콜론을 추가하는 것이다. 하지만, 더 복잡한 코드에 대해서도 작동하는데, 예를 들어, "for" 루프를 작성하는 중 이 단축키를 누르면 IntelliJ IDEA가 중괄호를 추가하고 블록 안에 커서를 배치한다. "if" 문에서는 괄호 및 중괄호를 추가하고 역시 적당한 위치에 커서를 배치한다. IDE가 문을 완성하기 위해 더 이상 코드를 추가할 필요가 없을 경우에도 이 단축키를 사용해 적당한 다음 위치에 커서를 배치할 수 있어서 매우 유용하다.

![img011](/assets/img/2020-01-20-intellij-keymap/img011.gif)

## 12. ⌥⌘L 또는 Ctrl+Alt+L
⌥⌘L(MacOS) 또는 Ctrl+Alt+L(Windows/Linux)을 사용하여 해당 프로젝트의 표준에 맞도록 현재 파일의 서식을 지정할 수 있다. 파일에서 변경된 줄 또는 전체 파일의 서식을 지정하도록 설정할 수 있다. 서식 지정 시 표준에 따라 필요한 경우 중괄호가 추가될 수도 있다. 다른 범위를 선택하여 서식을 지정하고 싶으면 ⇧⌥⌘L(MacOS) 또는 Shift+Ctrl+Alt+L(Windows/Linux)을 사용한 후 예들 들어, 전체 파일의 서식을 다시 지정하도록 선택할 수 있다.

![img012](/assets/img/2020-01-20-intellij-keymap/img012.gif)

## 13. ⌃T 또는 Shift+Ctrl+Alt+T
IntelliJ IDEA에서 대부분의 자동화된 리팩토링은 자체적인 단축키가 있지만, 하나의 단축키 ⌃T(MacOS) 또는 Shift+Ctrl+Alt+T(Windows/Linux)를 사용해 전체에 액세스할 수 있다. 심볼 또는 선택 영역 위에서 이 단축키를 누르면 사용 가능한 리팩토링 옵션이 표시된다. 그러면, 방향 키를 사용해 하나를 선택하여 입력하거나, 리팩토링 왼쪽에 있는 숫자를 사용해 선택할 수 있다. 특정 리팩토링의 키보드 단축키가 존재할 경우 대화 상자에도 표시되어 다음 번에 바로 사용할 수 있다.

![img013](/assets/img/2020-01-20-intellij-keymap/img013.gif)

## 14. ⇧⌘A 또는 Shift+Ctrl+A
이 모든 단축키를 기억할 필요는 없다. Find Action, ⇧⌘A(MacOS) 또는 Shift+Ctrl+A(Windows/Linux)를 사용해 IntelliJ IDEA에서 모든 액션을 검색할 수 있다. 드롭다운 메뉴에 액션뿐만 아니라 단축키도 표시되므로 이 단축키를 배우고 연습할 수 있다. 액션 검색을 통해 액션뿐만 아니라 설정도 검색할 수 있으므로, 여기서 바로 설정을 변경할 수 있다. 또한, 도구 창을 검색하고 열 수도 있다.

![img014](/assets/img/2020-01-20-intellij-keymap/img014.gif)

## 15. Shift shift
궁극적인 단축키는 Search Everywhere(전체 검색)이다. Shift 키를 두 번 누르면 모든 항목을 검색할 수 있는 검색 상자가 열린다. 액션 검색처럼 이를 사용해 설정을 변경할 수 있다. 기본적으로 검색 상자에는 최근 파일이 표시되므로 ⌘E / Ctrl+E 대신 이것을 사용할 수 있다. 검색할 항목을 입력하면 클래스, 파일, 심볼 및 액션에 대한 결과가 표시된다. 또한, 전체 검색은 명령어를 지원하므로, 예를 들어, 에디터에 대한 설정을 검색할 수 있다.

![img015](/assets/img/2020-01-20-intellij-keymap/img015.gif)

## [출처 및 참고]
* [https://blog.jetbrains.com/kr/2020/03/top-15-intellij-idea-shortcuts_ko/](https://blog.jetbrains.com/kr/2020/03/top-15-intellij-idea-shortcuts_ko/)
