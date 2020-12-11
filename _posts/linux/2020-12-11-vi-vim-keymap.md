--- 
title: vi, vim 편집기 명령어
author: Hyosik
date: 2020-12-11 09:30:00 +0900
categories: [OS, Linux]
tags: [vi-keymap, vim-keymap, visual-editor, keymap, vi-단축키, vim-단축키]
---

## 1. 시작

| 명령어 | 설명 |
|:---:|:---:|
| vi filename | 파일열기, 작성 |
| vi +18 filename | 18행으로 파일 열기 |
| vi +/"문자열" fn | "문자열"의 처음발생 단어부터 열기 |
| vi -r filename | 손상된 파일 회복 |
| view filename | 읽기 전용 |

## 2. 커서 이동

| 명령어 | 설명 |
|:---:|:---:|
| h(←) | 왼쪽으로 커서 이동 |
| j(↓) | 아래로 커서 이동 |
| k(↑) | 위로 커서 이동 |
| l(→) | 오른쪽으로 커서 이동 |
| w | 오른쪽 한 단어의 끝 부분으로 커서 이동 |
| e | 오른쪽 한 단어의 앞 부분으로 커서 이동 |
| b | 왼쪽 한 단어의 앞 부분으로 커서 이동 |
| Enter | 한 행 아래로 커서 이동 |
| Back space | 한 문자 왼쪽으로 커서 이동 |
| Space Bar | 한 문자 오른쪽으로 커서 이동 |
| ^ | 행의 맨 왼쪽으로 커서 이동 |
| $ | 행의 맨 오른쪽으로 커서 이동 |
| H | 화면의 맨 위로 이동 |
| M | 화면의 중간으로 이동 |
| L | 화면의 맨 아래로 이동 |
| 숫자G | '숫자' 만큼 지정한 줄로 커서 이동 |
| Ctrl + f | 한 화면 위로 이동 |
| Ctrl + b | 한 화면 아래로 이동 |
| Ctrl + d | 반 화면 위로 이동 |
| Ctrl + u | 반 화면 아래로 이동 |
| Ctrl + e | 한 줄씩 위로 이동 |
| Ctrl + y | 한 줄씩 아래로 이동 |

## 3. 문자, 행, 삽입

| 명령어 | 설명 |
|:---:|:---:|
| a (종료: ESC) | 커서 오른쪽에 문자 삽입 |
| A (종료: ESC) | 커서 오른쪽, 행의 끝에 문자 삽입 |
| i (종료: ESC) | 커서 왼쪽에 문자 삽입 |
| I (종료: ESC) | 커서 왼쪽, 행의 처음에 문자 삽입 |
| o (종료: ESC) | 커서 아래에 행 삽입 |
| O (종료: ESC) | 커서 위에 행 삽입 |

## 4. 텍스트 변경

| 명령어 | 설명 |
|:---:|:---:|
| cw (종료: ESC) | 단어 변경 |
| cc (종료: ESC) | 행 변경 |
| C (종료: ESC) | 커서 오른쪽의 행 변경 |
| s (종료: ESC) | 커서가 위치한 문자열 대체 |
| S (종료: ESC) | 커서가 위치한 라인의 문자열 대체 |
| r | 커서 위치 문자를 다른 문자로 대체 |
| r-Enter | 행 분리 |
| J | 현재 행과 아래 행 결합 |
| xp | 커서 위치 문자와 오른쪽 문자 교환 |
| ~ | 문자형(대/소문자) 변경 |
| u | 이전 명령 취소 |
| U | 행 변경 사항 취소 |
| :u | 이전의 최종 행 취소 |
| . | 이전 최종 명령 반복 |

## 5. 텍스트 삭제

| 명령어 | 설명 |
|:---:|:---:|
| x | 커서가 있는 문자 삭제 |
| 5x | 커서가 있는 위치부터 5개의 문자를 삭제 |
| dw | 현재 커서가 있는 한단어 삭제 |
| dd | 커서가 있는 라인 삭제 |
| 5dd | 커서가 있는 라인부터 5개의 라인 삭제 |
| db | 커서의 위치에서 거꾸로 한단어 삭제 |
| D | 커서 오른쪽 행 삭제 |
| :5.10d | 5-10번째 행 삭제 |

## 6. 복사 및 이동

| 명령어 | 설명 |
|:---:|:---:|
| yy | 행 yank 또는 복사 |
| Y | 행 yank 또는 복사 |
| p | yank 되거나 삭제된 행 현재 행 위에 삽입 |
| P | yank 되거나 삭제된 행 현재 행 아래에 삽입 |
| :1.2 co 3 | 1-2 행을 3행 다음으로 복사 |
| :4.5 m 6 | 4-5 행을 6행 위로 이동 |

## 7. 행 번호 설정

| 명령어 | 설명 |
|:---:|:---:|
| :set nu | 행 번호 표시 |
| :set nonu | 행 번호 숨기기 |

## 8. 행 찾기

| 명령어 | 설명 |
|:---:|:---:|
| G | 파일의 마지막 행으로 가기 |
| 21G | 파일의 21번째 행으로 가기 |
| Ctrl + G | 현재의 filename과 line 수를 알려줌 |

## 9. 탐색 및 대체

| 명령어 | 설명 |
|:---:|:---:|
| /검색할 문자열/ | 오른쪽 아래 방향으로 문자열 검색 |
| ?검색할 문자열? | 왼쪽 위 방향으로 문자열 검색 |
| n | 문자열의 다음으로 계속 검색 |
| N | 문자열의 이전으로 계속 검색 |
| :g/search-string/s//replace-string/g/gc | 각 발생 탐색 후 확인하고 대체 |
| :s/str/rep/ | 현재 행의 str을 rep로 대체 |
| :1..s/str/rep | 1 부터 현재 행의 str을 rep로 대체 |
| :%s/str/rep/g | 파일 전체 str을 rep로 전부 대체 |
| :.$/aaa/bbb/ | 모든 aaa를 bbb로 대체 |

## 10. 화면 정리

| 명령어 | 설명 |
|:---:|:---:|
| Ctrl + l | 불필요한 화면정리 후 다시 표시 |

## 11. 파일

| 명령어 | 설명 |
|:---:|:---:|
| :r filename | 커서 다음에 파일 삽입 |
| :34 r filename | 파일을 34번째 행 다음에 삽입 |

## 12. 보관 및 종료

| 명령어 | 설명 |
|:---:|:---:|
| :w | 변경사항 보관 |
| :w filename | 버퍼를 파일로 보관 |
| :wq | 변경사항 보관 후 vi 종료 |
| ZZ | 변경사항 보관 후 vi 종료 |
| :q! | 변경사항 보관하지 않고 vi 종료 |
| q | 수정한 파일을 저장하지 않고 vi 종료 |
| e! | 수정한 것을 무시하고 다시 편집상태로 |

* Basic Editing

![img001](/assets/img/2020-12-11-vi-vim-keymap/img001.gif)

* Operators & Repetition

![img002](/assets/img/2020-12-11-vi-vim-keymap/img002.gif)

* Yank & Paste

![img003](/assets/img/2020-12-11-vi-vim-keymap/img003.gif)

* Searching

![img004](/assets/img/2020-12-11-vi-vim-keymap/img004.gif)

* Marks & Macros

![img005](/assets/img/2020-12-11-vi-vim-keymap/img005.gif)

* Various Motions

![img006](/assets/img/2020-12-11-vi-vim-keymap/img006.gif)

* Various Commands

![img007](/assets/img/2020-12-11-vi-vim-keymap/img007.gif)

* 한글 단축키 모음

![img008](/assets/img/2020-12-11-vi-vim-keymap/img008.jpg)

* vim 명령어 단축키

![img009](/assets/img/2020-12-11-vi-vim-keymap/img009.jpg)

* vim 이동 단축키

![img010](/assets/img/2020-12-11-vi-vim-keymap/img010.jpg)

## [출처 및 참고]
* <http://www.viemu.com/a_vi_vim_graphical_cheat_sheet_tutorial.html>
* <https://iamfreeman.tistory.com/entry/vi-vim-%ED%8E%B8%EC%A7%91%EA%B8%B0-%EB%AA%85%EB%A0%B9%EC%96%B4-%EC%A0%95%EB%A6%AC-%EB%8B%A8%EC%B6%95%ED%82%A4-%EB%AA%A8%EC%9D%8C-%EB%AA%A9%EB%A1%9D>
