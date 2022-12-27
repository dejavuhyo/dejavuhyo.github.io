---
title: Linux Nano 에디터 단축키
author: dejavuhyo
date: 2021-02-26 06:10:00 +0900
categories: [DevOps, Linux]
tags: [nano-editor, linux-nano, nano-keymap, linux-nano-keymap, 나노-에디터, 리눅스-나노, 나노-키캡, 리눅스-나노-키맵]
---

## 1. GNU 나노

![nano](/assets/img/2021-02-26-linux-nano-editor-keymap/nano.png)

나노(nano)는 유닉스 계열 컴퓨팅 시스템이나 명령 줄 인터페이스를 사용하는 운영 환경을 위한 문서 편집기이다. 파인 전자메일 클라이언트의 일부인 피코 문서 편집기를 가상으로 구현하며 부가 기능도 제공한다.

피코와는 달리 나노는 GNU 일반 공중 사용 허가서(GPL) 라이선스로 배포된다. 1999년 크리스 알레그레타가 자유 소프트웨어로 출시한 나노는 오늘날 GNU 프로젝트의 일부로 되어 있다.

## 2. 단축키

![nano-svn-version](/assets/img/2021-02-26-linux-nano-editor-keymap/nano-svn-version.png)

^ = Ctrl key, M = Alt key

### 1) 파일 관리

| 명령어 | 작업 |
|:-----:|:-----:|
| Ctrl + r | nano 내에서 파일 열기 (화면 아래에 새로운 메뉴 항목이 표시된다. 예를 들어, Ctrl + T를 누르면 파일 시스템을 탐색하여 파일을 찾아서 열 수 있다.) |
| Alt + > | 다음 파일 버퍼 표시 |
| Alt + < | 이전 파일 버퍼 표시 |
| Ctrl + o | 현재 파일을 디스크에 저장 |
| Ctrl + x | 현재 파일 버퍼 종료 (파일이 저장되지 않았다면 저장할 것인지 물어본다. 파일 버퍼가 하나만 열려 있는 경우 파일 버퍼를 종료하면 nano에서 나가게 된다.) |

### 2) 복사 및 붙여넣기

| 명령어 | 작업 |
|:-----:|:-----:|
| Alt + a | 자르기 또는 붙여넣기 작업을 할 영역 선택 (Alt + a로 마크를 설정한 후에 커서를 이동 시켜 영역을 지정한다. 커서를 움직일 때 하이라이트 된다. 또한, 영역 지정을 취소하려면 Alt + a를 다시 누르면 된다.) |
| Alt + ^ | 강조 표시된 영역을 클립보드에 복사 |
| Ctrl + k | 강조 표시된 영역을 잘라서 클립보드에 저장 (라인을 삭제( 자르기)할 때에도 사용) |
| Ctrl + u | 클립보드의 내용을 현재 커서 위치에 붙여넣기 |
| Ctrl + k | 현재 커서 위치에서 라인의 끝(EOL)까지 자르기 (영역을 강조표시할 필요가 없다.) |

### 3) 코드 탐색

| 명령어 | 작업 |
|:-----:|:-----:|
| Alt + \ | 파일의 시작 부분으로 이동 |
| Alt + / | 파일의 끝으로 이동 |
| Ctrl + v | 한 화면 앞으로 이동(아래로 이동) |
| Ctrl + y | 한 화면 뒤로 이동(위로 이동) |
| Alt + g | 대상 라인 번호로 이동(라인 번호를 입력하여 곧바로 이동) |
| Alt + ] | 짝을 이루는 열린/닫힌 기호로 곧바로 이동 |
| Alt + = | 창 아래로 스크롤 |
| Alt + - | 창 위로 스크롤 |
| Alt + } | 블록 들여쓰기 (Alt + a 를 사용하여 블록을 선택한 후) |
| Alt + { | 블록 내어쓰기 (Alt + a 를 사용하여 블록을 선택한 후) |

### 4) 찾기 및 바꾸기

| 명령어 | 작업 |
|:-----:|:-----:|
| Ctrl + w | 대상 문자열 검색 (화면 하단에 새로운 메뉴 항목이 표시된다. 예를 들어, Alt + B를 누르면 역방향 검색 전환, Ctrl + R은 검색 문자열을 다른 문자열로 바꾸기 이다.) |
| Alt + w | 마지막 검색 반복 |
| Ctrl + w를 누른 후 Ctrl + b | 다음 검색에 대한 방향 토글(전환) |
| Alt + r | 찾아서 바꾸기 |

### 5) 명명된 키에 해당하는 명령어

| 명령어 | 키 |
|:-----:|:-----:|
| Ctrl + a | Home |
| Ctrl + e | End |
| Ctrl + y | Page Up |
| Ctrl + v | Page Down |
| Ctrl + f (오른쪽), Ctrl + b (왼쪽), Ctrl + n (아래), Ctrl + p (위) | 화살표 키 |
| Ctrl + i | Tab |
| Ctrl + h | Backspace |
| Ctrl + d | Delete |
| Ctrl + m | Return |

## [출처 및 참고]
* <https://ko.wikipedia.org/wiki/GNU_%EB%82%98%EB%85%B8>
* <https://www.thewordcracker.com/miscellaneous/%EB%A6%AC%EB%88%85%EC%8A%A4%EC%9D%98-nano-%EC%97%90%EB%94%94%ED%84%B0-%EB%8B%A8%EC%B6%95%ED%82%A4/>
