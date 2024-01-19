---
title: IntelliJ 마우스를 사용하지 않아도 되는 10개 위치
author: dejavuhyo
date: 2024-01-19 14:00:00 +0900
categories: [Application, IDE]
tags: [intellij-shortcuts, intellij-keymap, 인텔리제이-단축키, 인텔리제이-키맵]
---

## 1. Project(프로젝트) 도구 창(또는 다른 활성 도구 창)의 크기 조정
Project(프로젝트) 도구 창은 IntelliJ IDEA에서 가장 자주 사용되는 창 중 하나이다. 이 창의 너비를 늘리거나 줄여야 하는 경우가 종종 생길 수 있다. 마우스를 사용하는 대신 `Ctrl+Shift+오른쪽 방향키`(Windows 또는 Linux) 또는 `⇧⌘오른쪽 방향키`(macOS)를 눌러 도구 창의 너비를 늘리고 `Ctrl+Shift+왼쪽 방향키` 또는 `⇧⌘왼쪽 방향키`를 눌러 너비를 줄일 수 있다(IntelliJ IDEA 2021.2 이전 버전의 경우).

Run(실행) 또는 Problems(문제)와 같은 다른 도구 창의 경우 `Ctrl+Shift+위쪽 방향키`(Windows 또는 Linux) 또는 `⇧⌘위쪽 방향키`(macOS)를 사용하여 높이를 늘이고 `Ctrl+Shift+아래쪽 방향키` 또는 `⇧⌘아래쪽 방향키`를 사용하여 높이를 줄일 수 있다(IntelliJ IDEA 2021.2 이전 버전의 경우).

IntelliJ IDEA 2021.2.1 이상 버전의 경우, 해당 단축키가 Windows 및 Linux의 경우 `Ctrl+Alt+Shift+오른쪽/왼쪽/위/아래 방향키`로, macOS의 경우 `⌥⇧⌘+오른쪽/왼쪽/위/아래 방향키`로 변경되었다.

![mouse-1](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-1.gif)

## 2. Esc를 사용하여 에디터 창으로 돌아가고 F12를 사용하여 마지막 도구 창으로 이동
Project(프로젝트) 도구 창, Debug(디버그) 창, Maven 등과 같은 도구 창 작업을 마친 후에는 Esc를 사용하여 에디터로 돌아갈 수 있다(마우스로 에디터 창을 클릭하는 대신).

또한 `F12`를 사용하여 마지막으로 사용한 도구 창으로 돌아갈 수 있다(해당 도구 창에 해당하는 지정된 단축키를 사용하는 대신).

다음의 gif에서는 `Escape 키`를 사용하여 에디터 창에 다시 포커스를 놓을 때 Presentation Assistant 플러그인은 'Escape' 텍스트를 표시하지 않는다.

![mouse-2](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-2.gif)

`Escape`(또는 다른 키)를 누를 때의 동작이 상황에 따라 다른 컨텍스트에서 이 키를 눌렀을 때의 결과를 알고 싶다면, *Settings(설정)* → *Keymap(키맵)* → *Find Actions by Shortcut(단축키로 액션 찾기)*로 이동한 다음 `Esc 키`를 누른다.

![mouse-3](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-3.gif)

## 3. 도구 창 표시
우선 많은 개발자들이 IntelliJ IDEA에서 도구 창을 어떻게 표시하는지 알아본다. 예를 들어, 풀 리퀘스트를 불러오려면 도구 창 표시줄에서 Pull Requests(풀 리퀘스트) 탭을 클릭하면 된다.

![mouse-4](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-4.gif)

이 경우에도 마우스를 사용하지 않아도 된다. 처음에 간단히 마우스를 한 번 사용하고 나면 더 이상 마우스를 건드리지 않아도 된다.

Pull Requests(풀 리퀘스트) 탭 위로 마우스를 가져가면 이 도구 창에 단축키가 할당되었는지 확인할 수 있다. 이 경우에는 단축키가 지정되어 있지 않으니 지정해 본다. *Find(찾기)* 액션을 사용하여 *View(뷰)* → *Tool window(도구 창)* 설명이 있는 *Pull Requests(풀 리퀘스트)*를 검색한 다음, `Alt+Enter`를 사용하여 단축키(예: `Ctrl+Shift+Alt+9`)를 할당한다.

이제 단축키를 설정했으므로 이 단축키를 사용해 풀 리퀘스트 도구 창을 표시하거나 숨길 수 있다.

![mouse-5](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-5.gif)

또는 *Project(프로젝트)* 도구 창에 `Alt+1` 또는 *Structure(구조)* 도구 창에 `Alt+7`과 같이 도구 창에 이미 단축키가 할당된 경우, 도구 창 표시줄의 탭 위로 마우스를 가져가면 단축키가 표시된다.

![mouse-6](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-6.gif)

`Ctrl+Tab`(또는 `⌃Tab`)을 사용하여 IntelliJ IDEA에서 *Switcher(전환기)* 기능을 호출하면 도구 창 및 최근 파일 목록을 볼 수도 있다. 이 기능을 사용하면 화살표 키를 사용하여 도구 창 이름으로 빠르게 이동할 수 있다.

메뉴 표시줄로 작업하는 것을 선호하는 경우, *View(뷰)* → *Tool(도구)* 창을 선택하고 사용 가능한 모든 도구 창 목록을 볼 수도 있다.

## 4. 활성화된 또는 모든 도구 창 닫기
도구 창을 닫거나 숨기는 방법에는 여러 가지가 있다. 활성화된 도구 창을 숨기려면 `Shift+Escape`(또는 `⇧ Esc`)를 사용한다. 이 특정 단축키를 사용하여 도구 창을 숨기거나 표시할 수 있다. 예를 들어 `Alt+9`(또는 `⌘9`)는 Git 도구 창을 표시하는 단축키이다. `Alt+9`(또는 `⌘9`)를 다시 누르면 이 도구 창이 숨겨진다.

단축키 `Ctrl+Shift+F12`(`⇧⌘ F12`)를 사용하여 모든 도구 창을 숨기거나 복원할 수도 있다.

![mouse-7](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-7.gif)

## 5. Alt+Enter를 사용하면 전구 아이콘을 통해 팁이 표시 됨
코드 스니펫에 대해 이미 제시된 제안을 보기 위해 더 이상 전구 아이콘을 클릭할 필요가 없다. 전구 아이콘이 표시되는 코드 줄로 이동하고 `Alt+Enter`를 사용하여 컨텍스트 액션을 호출하면 동일한 제안을 볼 수 있다.

![mouse-8](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-8.gif)

## 6. 필요한 패키지의 코드 에디터 창에서 작업하는 동안 새 클래스 선언
이 기능은 IntelliJ IDEA의 숨겨진 보석과 같은 기능 중 하나이다. 매우 유용하지만 알고 있는 개발자가 별로 없는 것 같다.

에디터에서 작업하는 동안 클래스를 생성하려면 `Alt+Home`(또는 `⌘ 위쪽 방향키`)을 사용하여 탐색 표시줄에 액세스한다. 새 클래스, 인터페이스 또는 다른 엔티티를 선언할 패키지를 선택한 다음 `Alt+Insert`(또는 `⌘ N`)을 사용하여 새 대화상자를 호출한 후, 이름만 입력하면 끝난다.

> 추가 팁: 하위 패키지를 만들려면 패키지 이름을 접두사로 추가한다. 예를 들어 'com' 패키지 내의 'space'라는 새 하위 패키지에 'NewClassInPackageSpace'라는 클래스를 만들려면 새 대화상자에서 'space.NewClassInPackageSpace'를 작성한다.

![mouse-9](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-9.gif)

## 7. 열려 있는 파일 사이 이동
많은 개발자들이 에디터에서 파일 사이를 이동할 때 탭을 사용하지만 더 좋은 방법이 있다.

* `Alt+오른쪽 방향키` 또는 `Alt+왼쪽 방향키`를 사용하여 마지막으로 작업한 파일 사이를 빠르게 이동한다. 그러나 Presentation Assistant 플러그인은 이 경우에 사용되는 단축키를 표시하지 않는다.

최근에 작업한 파일 사이를 이동할 때는 파일 사이를 매우 빠르게 이동할 수 있는 이 단축키가 매우 유용하다.

다음 gif는 `Alt+오른쪽 방향키` 또는 `Alt+왼쪽 방향키`를 사용하여 다른 파일로 빠르게 이동하는 방법을 보여준다.

![mouse-10](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-10.gif)

* 최근에 작업한 도구 창 및 파일 목록을 보려면 *Switcher(전환기) 탭*(Windows 및 Linux의 경우 `Ctrl+Tab`, macOS의 경우 `^Tab`)을 사용한 후, 열려는 도구 창이나 파일을 선택하면 된다.

![mouse-11](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-11.png)

* 최근에 작업한 파일을 보려면 `Ctrl+E`(또는 `⌘E`)를 사용한디. 이 목록에는 열어 본 모든 유형의 파일도 포함된다. 대화상자가 열려 있는 상태에서 `Ctrl+E`를 다시 누르면 목록에 편집한 파일들만 추려서 나열된다.
gif
![mouse-12](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-12.gif)

* 작업하던 파일이 도무지 기억나지 않는다면 `Ctrl+Shift+E`를 사용하여 *Recent Locations(최근 위치)*에 액세스 한다. 그러면 파일 이름과 함께 파일 내의 몇 개 줄도 표시된다. 대화상자가 계속 표시된 상태에서 동일한 단축키를 다시 사용하면 최근에 편집한 파일이 표시된다.

![mouse-13](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-13.gif)

## 8. 클래스, 인터페이스, 열거형, 레코드, 메서드, 변수 선언 등의 소스에 대한 빠른 뷰
작성한 테스트를 읽으면서 특정 메서드의 정의를 간단히 보고 넘어가려면 메서드 이름에 커서를 놓고 `Ctrl+Shift+I`를 누르기만 하면 된다. 그러면 해당 코드를 읽기 위해 다른 클래스로 이동할 필요가 없다. 이를 적용하여 클래스, 메서드 또는 기타 엔티티의 정의를 볼 수 있다. 이 기능의 이름은 *Quick Definition(빠른 정의)*이다. 

![mouse-14](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-14.gif)

## 9. 대화상자 창의 너비와 높이 수정
IntelliJ IDEA 2021.2 이전 버전에서 대화상자를 호출할 때 `Ctrl+Shift+왼쪽/오른쪽/위/아래 방향키`(Windows 또는 Linux) 또는 `⇧⌘왼쪽/오른쪽/위/아래 방향키`(macOS)를 사용하여 대화상자의 높이와 너비를 수정할 수 있다.

IntelliJ IDEA 2021.2.1 이상 버전의 경우, 해당 단축키가 Windows 및 Linux의 경우 `Ctrl+Alt+Shift+(오른쪽/왼쪽/위/아래 방향키)`로, macOS의 경우 `⌥⇧⌘+오른쪽/왼쪽/위/아래 방향키`로 변경되었다.

대화상자의 크기는 특정 제한을 초과하여 줄일 수 없다(크기를 줄이는 데 마우스 또는 키보드를 사용했는지 여부에 관계가 없음). 또한 이 단축키는 대화상자의 편집 가능한 구성 요소(예: 텍스트 필드 또는 텍스트 영역)에 위치해 있는 경우 작동하지 않는다.

![mouse-15](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-15.gif)

## 10. 커서 위치를 이동하지 않고 에디터 창에서 텍스트 스크롤
코드를 읽기 위해 에디터 창을 위나 아래로 스크롤해야 할 때가 있다. 코드를 편집할 필요가 없다면 현재 위치에서 커서를 이동할 필요가 없다.

에디터 창에서 `Ctrl+위쪽 방향키` 또는 `Ctrl+아래쪽 방향키`(또는 `⌘위쪽/⌘아래쪽 방향키`)를 사용하여 커서 위치를 변경하지 않고 스크롤한다. 또한 유사한 키보드 단축키인 `Ctrl+오른쪽 방향키` 및 `Ctrl+왼쪽 방향키`(또는 `⌘왼쪽/⌘오른쪽`)을 사용하여 프로젝트 도구 창에서 디렉터리 구조를 왼쪽이나 오른쪽으로 스크롤할 수 있다.

![mouse-16](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-16.gif)

> 추가 팁: 코드를 읽기 쉽게 에디터 창에서 위나 아래로 이동하려면 메서드, 코드 블록 또는 import 문 세트를 축소하여 에디터 창에서 차지하는 공간을 최소화할 수 있다. 이를 위해 `Ctrl+Numpad –`(Window/ Linux) 또는 `⌘Numpad –`(macOS)를 사용한다. 코드 블록을 확장하려면 `Ctrl+Numpad +`(Windows/Linux) 또는 `⌘Numpad +`(macOS)를 사용한다. 코드를 읽거나 표시하는 동안 관련이 없는 코드 때문에 주의가 산만해지거나 화면 공간이 차지되는 것을 원하지 않을 때 매우 유용할 수 있다.

![mouse-17](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-17.gif)

클래스에 많은 메서드가 있지만 몇 가지 메서드에 대한 코드만 읽으려는 경우, 모든 메서드를 축소하고 읽으려는 메서드만 확장한다. `Ctrl+Shift+Numpad –`(또는 `⇧⌘Numpad -`)를 사용하여 모든 메서드를 축소할 수 있다. 모든 코드를 확장하려면 `Ctrl+Shift+Numpad +`(또는 `⇧⌘Numpad +`)를 사용한다. 코드 가독성을 높이기 위해 몇 개의 코드 블록만 선택적으로 확장할 수도 있다.

`Alt+아래쪽 방향키`(`⌃아래쪽`) 또는 `Alt+위쪽 방향키`(또는 `⌃위쪽`)를 사용하여 한 메서드 선언에서 다른 메서드 선언으로 건너뛰는 방법도 유용할 수 있다.

![mouse-18](/assets/img/2024-01-19-intellij-do-not-need-mouse/mouse-18.gif)

## [출처 및 참고]
* [https://blog.jetbrains.com/ko/idea/2021/09/10-places-you-don-t-need-to-use-the-mouse-in-intellij-idea/](https://blog.jetbrains.com/ko/idea/2021/09/10-places-you-don-t-need-to-use-the-mouse-in-intellij-idea/)
