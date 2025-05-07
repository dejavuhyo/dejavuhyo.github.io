---
title: IntelliJ 파일 상태 하이라이트
author: dejavuhyo
date: 2025-05-07 11:45:00 +0900
categories: [Application, IDE]
tags: [intellij-highlights, intellij-file, file-status-highlights, file-highlights, intellij-파일-상태, intellij-파일-하이라이트]
---

## 1. 파일 상태
프로젝트 도구 창 (`Alt` `1`)에서 파일을 다양한 색상으로 표시할 수 있으며, 편집기 탭은 코드 분석 결과, 버전 관리 상태 등에 따라 강조 표시될 수 있다.

`버전 제어 → 파일 상태 색상` 설정 페이지에서 VCS 파일 상태 색상을 구성할 수 있다. (`Ctrl` `Alt` `S`)

> 수정된 내용이 있는 폴더와 패키지를 강조 표시하려면 `버전 제어 → 확인` 설정 페이지의 프로젝트 트리에서 수정된 파일이 있는 디렉터리 강조 표시를 선택한다. (`Ctrl` `Alt` `S`)

아래 표는 기본 파일 상태 색상과 일부 색상 구성표에서의 의미를 나열한다.

### 1) Light theme

| Color | File Status | Description |
|:-----:|:-----:|:-----:|
| ![added-light](/assets/img/2025-05-07-intellij-file-status-highlights/added-light.png) <span style='color: #0A7700'>#0A7700</span> | Added | 활성 변경 목록에 있는 파일은 저장소에 추가되도록 예약되어 있다. |
| ![added-inactive-changelist-light](/assets/img/2025-05-07-intellij-file-status-highlights/added-inactive-changelist-light.png) #0EAA00 | Added in not active changelist | 비활성 변경 목록에 있는 파일이 저장소에 추가되도록 예약되었다. 이 파일 상태는 `설정 → 버전 관리 → 변경` 목록에서 "비활성 변경 목록의 파일 강조 표시" 옵션이 활성화된 경우에 사용할 수 있다. |
| ![conflicts-light](/assets/img/2025-05-07-intellij-file-status-highlights/conflicts-light.png) #FF0000 | Changelist conflict | 비활성 변경 목록에 있는 파일이 활성 변경 목록에서 수정되었습니다. 이 경우, 변경 목록 충돌을 해결하라는 새 대화 상자가 열ㄴ다. 이 파일 상태는 `설정 → 버전 관리 → 변경 목록`에서 모든 옵션이 활성화된 경우에만 사용할 수 있다. |
| ![added-light](/assets/img/2025-05-07-intellij-file-status-highlights/added-light.png) #0A7700 | Copied | 파일이 다른 파일의 복사본인 경우 해당 파일의 메타데이터가 추적되고 해당 파일은 복사된 것으로 표시된다. |
| ![deleted-light](/assets/img/2025-05-07-intellij-file-status-highlights/deleted-light.png) #616161 | Deleted | 해당 파일은 저장소에서 삭제되도록 예약되었다. |
| ![deleted-from-filesystem-light](/assets/img/2025-05-07-intellij-file-status-highlights/deleted-from-filesystem-light.png) #773895 | Deleted from file system | 해당 파일은 로컬에서 삭제되었지만 삭제 예약은 되어 있지 않으며, 저장소에 여전히 존재한다. |
| ![changed-descendants-light](/assets/img/2025-05-07-intellij-file-status-highlights/changed-descendants-light.png) #8AA4C8 | Have changed descendants | 파일이 수정되면 IDE는 해당 파일이 포함된 모든 디렉터리를 재귀적으로 강조 표시한다. 이 상태는 `설정 → 버전 관리 → 확인`에서 "프로젝트 트리에서 수정된 파일이 포함된 디렉터리 강조 표시" 옵션이 활성화된 경우에만 사용할 수 있다. |
| ![changed-children-light](/assets/img/2025-05-07-intellij-file-status-highlights/changed-children-light.png) #3264B4 | Have immediate changed children | 파일이 수정되면 IDE에서 해당 파일의 상위 디렉터리를 강조 표시한다. 이 상태는 `설정 → 버전 관리 → 확인`에서 "프로젝트 트리에서 수정된 파일이 포함된 디렉터리 강조 표시" 옵션이 활성화된 경우에만 사용할 수 있다. |
| ![hijacked-light](/assets/img/2025-05-07-intellij-file-status-highlights/hijacked-light.png) #B28C00 | Hijacked | `Perforce`, `ClearCase`, `VSS` 파일이 체크아웃 없이 수정되었다. |
| ![ignored-light](/assets/img/2025-05-07-intellij-file-status-highlights/ignored-light.png) #727238 | Ignored | VCS에서 의도적으로 파일을 추적하지 않는다. |
| ![merged-light](/assets/img/2025-05-07-intellij-file-status-highlights/merged-light.png) #7503DC | Merged | 해당 파일은 업데이트로 인해 VCS에 의해 병합되었다. |
| ![conflicts-light](/assets/img/2025-05-07-intellij-file-status-highlights/conflicts-light.png) #FF0000 | Merged with conflicts | 마지막 업데이트 중에 파일이 병합되면서 충돌이 발생했다. |
| ![conflicts-light](/assets/img/2025-05-07-intellij-file-status-highlights/conflicts-light.png) #FF0000 | Merged with property conflicts | 마지막 업데이트 중에 IDE는 로컬 파일과 서버 버전의 속성 간에 차이점을 감지했다. |
| ![conflicts-light](/assets/img/2025-05-07-intellij-file-status-highlights/conflicts-light.png) #FF0000 | Merged with text and property conflicts | 두 명 이상의 개발자가 파일의 같은 줄과 같은 파일 속성을 수정할 때 텍스트와 속성 충돌이 발생한다. |
| ![modified-light](/assets/img/2025-05-07-intellij-file-status-highlights/modified-light.png) #0032A0 | Modified | 마지막 동기화 이후 파일이 변경되었다. |
| ![modified-inactive-changelist-light](/assets/img/2025-05-07-intellij-file-status-highlights/modified-inactive-changelist-light.png) #0047E4 | Modified in not active changelist | 비활성 변경 목록에 있는 파일이 수정되었다. 이 파일 상태는 `설정 → 버전 관리 → 변경 목록`에서 "비활성 변경 목록의 파일 강조 표시" 옵션이 활성화된 경우에 사용할 수 있다. |
| ![obsolete-light](/assets/img/2025-05-07-intellij-file-status-highlights/obsolete-light.png) #7C7C00 | Obsolete | 해당 파일은 더 이상 저장소의 작업 사본에 있어서는 안 된다. |
| ![renamed-light](/assets/img/2025-05-07-intellij-file-status-highlights/renamed-light.png) #007C7C | Renamed | 마지막 업데이트 이후 파일 이름이 변경되었다. |
| ![switched-light](/assets/img/2025-05-07-intellij-file-status-highlights/switched-light.png) #08978F | Switched | [SVN] 해당 파일은 전체 프로젝트와 다른 브랜치에서 가져왔다. |
| ![unversioned-light](/assets/img/2025-05-07-intellij-file-status-highlights/unversioned-light.png) #993300 | (Unknown) Unversioned | 해당 파일은 로컬에 존재하지만 저장소에 없으며 추가가 예약되어 있지 않다. |
| ![up-to-date-light](/assets/img/2025-05-07-intellij-file-status-highlights/up-to-date-light.png) None (default color) | Up to date | 파일이 변경되지 않았다. |

### 2) Darcula theme

| Color | File Status | Description |
|:-----:|:-----:|:-----:|
| ![added-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/added-darcula.png) #629755 | Added | 활성 변경 목록에 있는 파일은 저장소에 추가되도록 예약되어 있다. |
| ![added-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/added-darcula.png) #629755 | Added in not active changelist | 비활성 변경 목록에 있는 파일이 저장소에 추가되도록 예약되었다. 이 파일 상태는 `설정 → 버전 관리 → 변경` 목록에서 "비활성 변경 목록의 파일 강조 표시" 옵션이 활성화된 경우에 사용할 수 있다. |
| ![conflicts-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/conflicts-darcula.png) #D5756C | Changelist conflict | 비활성 변경 목록에 있는 파일이 활성 변경 목록에서 수정되었습니다. 이 경우, 변경 목록 충돌을 해결하라는 새 대화 상자가 열ㄴ다. 이 파일 상태는 `설정 → 버전 관리 → 변경 목록`에서 모든 옵션이 활성화된 경우에만 사용할 수 있다. |
| ![copied-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/copied-darcula.png) #0A7700 | Copied | 파일이 다른 파일의 복사본인 경우 해당 파일의 메타데이터가 추적되고 해당 파일은 복사된 것으로 표시된다. |
| ![deleted-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/deleted-darcula.png) #6C6C6C | Deleted | 해당 파일은 저장소에서 삭제되도록 예약되었다. |
| ![deleted-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/deleted-darcula.png) #6C6C6C | Deleted from file system | 해당 파일은 로컬에서 삭제되었지만 삭제 예약은 되어 있지 않으며, 저장소에 여전히 존재한다. |
| ![changed-descendants-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/changed-descendants-darcula.png) #6897BB | Have changed descendants | 파일이 수정되면 IDE는 해당 파일이 포함된 모든 디렉터리를 재귀적으로 강조 표시한다. 이 상태는 `설정 → 버전 관리 → 확인`에서 "프로젝트 트리에서 수정된 파일이 포함된 디렉터리 강조 표시" 옵션이 활성화된 경우에만 사용할 수 있다. |
| ![changed-descendants-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/changed-descendants-darcula.png) #6897BB | Have immediate changed children | 파일이 수정되면 IDE에서 해당 파일의 상위 디렉터리를 강조 표시한다. 이 상태는 `설정 → 버전 관리 → 확인`에서 "프로젝트 트리에서 수정된 파일이 포함된 디렉터리 강조 표시" 옵션이 활성화된 경우에만 사용할 수 있다. |
| ![hijacked-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/hijacked-darcula.png) None (default color) | Hijacked | `Perforce`, `ClearCase`, `VSS` 파일이 체크아웃 없이 수정되었다. |
| ![ignored-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/ignored-darcula.png) #848504 | Ignored | VCS에서 의도적으로 파일을 추적하지 않는다. |
| ![merged-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/merged-darcula.png) #9876AA | Merged | 해당 파일은 업데이트로 인해 VCS에 의해 병합되었다. |
| ![conflicts-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/conflicts-darcula.png) #D5756C | Merged with conflicts | 마지막 업데이트 중에 파일이 병합되면서 충돌이 발생했다. |
| ![conflicts-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/conflicts-darcula.png) #D5756C | Merged with property conflicts | 마지막 업데이트 중에 IDE는 로컬 파일과 서버 버전의 속성 간에 차이점을 감지했다. |
| ![conflicts-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/conflicts-darcula.png) #D5756C | Merged with text and property conflicts | 두 명 이상의 개발자가 파일의 같은 줄과 같은 파일 속성을 수정할 때 텍스트와 속성 충돌이 발생한다. |
| ![changed-descendants-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/changed-descendants-darcula.png) #6897BB | Modified | 마지막 동기화 이후 파일이 변경되었다. |
| ![changed-descendants-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/changed-descendants-darcula.png) #6897BB | Modified in not active changelist | 비활성 변경 목록에 있는 파일이 수정되었다. 이 파일 상태는 `설정 → 버전 관리 → 변경 목록`에서 "비활성 변경 목록의 파일 강조 표시" 옵션이 활성화된 경우에 사용할 수 있다. |
| ![hijacked-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/hijacked-darcula.png) None (default color) | Obsolete | 해당 파일은 더 이상 저장소의 작업 사본에 있어서는 안 된다. |
| ![renamed-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/renamed-darcula.png) #3A8484 | Renamed | 마지막 업데이트 이후 파일 이름이 변경되었다. |
| ![hijacked-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/hijacked-darcula.png) None (default color) | Switched | [SVN] 해당 파일은 전체 프로젝트와 다른 브랜치에서 가져왔다. |
| ![unversioned-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/unversioned-darcula.png) #D1675A | (Unknown) Unversioned | 해당 파일은 로컬에 존재하지만 저장소에 없으며 추가가 예약되어 있지 않다. |
| ![hijacked-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/hijacked-darcula.png) None (default color) | Up to date | 파일이 변경되지 않았다. |

### 3) High-contrast theme

| Color | File Status | Description |
|:-----:|:-----:|:-----:|
| ![added-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/added-hcontrast.png) #62CC47 | Added | 활성 변경 목록에 있는 파일은 저장소에 추가되도록 예약되어 있다. |
| ![added-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/added-hcontrast.png) #62CC47 | Added in not active changelist | 비활성 변경 목록에 있는 파일이 저장소에 추가되도록 예약되었다. 이 파일 상태는 `설정 → 버전 관리 → 변경` 목록에서 "비활성 변경 목록의 파일 강조 표시" 옵션이 활성화된 경우에 사용할 수 있다. |
| ![conflicts-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/conflicts-hcontrast.png) #FF6666 | Changelist conflict | 비활성 변경 목록에 있는 파일이 활성 변경 목록에서 수정되었습니다. 이 경우, 변경 목록 충돌을 해결하라는 새 대화 상자가 열ㄴ다. 이 파일 상태는 `설정 → 버전 관리 → 변경 목록`에서 모든 옵션이 활성화된 경우에만 사용할 수 있다. |
| ![added-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/added-hcontrast.png) #62CC47 | Copied | 파일이 다른 파일의 복사본인 경우 해당 파일의 메타데이터가 추적되고 해당 파일은 복사된 것으로 표시된다. |
| ![deleted-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/deleted-hcontrast.png) #ED864A | Deleted | 해당 파일은 저장소에서 삭제되도록 예약되었다. |
| ![deleted-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/deleted-hcontrast.png) #ED864A | Deleted from file system | 해당 파일은 로컬에서 삭제되었지만 삭제 예약은 되어 있지 않으며, 저장소에 여전히 존재한다. |
| ![changed-descendants-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/changed-descendants-hcontrast.png) #4FF0FF | Have changed descendants | 파일이 수정되면 IDE는 해당 파일이 포함된 모든 디렉터리를 재귀적으로 강조 표시한다. 이 상태는 `설정 → 버전 관리 → 확인`에서 "프로젝트 트리에서 수정된 파일이 포함된 디렉터리 강조 표시" 옵션이 활성화된 경우에만 사용할 수 있다. |
| ![changed-descendants-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/changed-descendants-hcontrast.png) #4FF0FF | Have immediate changed children | 파일이 수정되면 IDE에서 해당 파일의 상위 디렉터리를 강조 표시한다. 이 상태는 `설정 → 버전 관리 → 확인`에서 "프로젝트 트리에서 수정된 파일이 포함된 디렉터리 강조 표시" 옵션이 활성화된 경우에만 사용할 수 있다. |
| ![hijacked-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/hijacked-darcula.png) None (default color) | Hijacked | `Perforce`, `ClearCase`, `VSS` 파일이 체크아웃 없이 수정되었다. |
| ![ignored-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/ignored-hcontrast.png) #A9B837 | Ignored | VCS에서 의도적으로 파일을 추적하지 않는다. |
| ![merged-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/merged-hcontrast.png) #ED94FF | Merged | 해당 파일은 업데이트로 인해 VCS에 의해 병합되었다. |
| ![conflicts-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/conflicts-hcontrast.png) #FF6666 | Merged with conflicts | 마지막 업데이트 중에 파일이 병합되면서 충돌이 발생했다. |
| ![conflicts-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/conflicts-hcontrast.png) #FF6666 | Merged with property conflicts | 마지막 업데이트 중에 IDE는 로컬 파일과 서버 버전의 속성 간에 차이점을 감지했다. |
| ![conflicts-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/conflicts-hcontrast.png) #FF6666 | Merged with text and property conflicts | 두 명 이상의 개발자가 파일의 같은 줄과 같은 파일 속성을 수정할 때 텍스트와 속성 충돌이 발생한다. |
| ![changed-descendants-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/changed-descendants-hcontrast.png) #4FF0FF | Modified | 마지막 동기화 이후 파일이 변경되었다. |
| ![changed-descendants-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/changed-descendants-hcontrast.png) #4FF0FF | Modified in not active changelist | 비활성 변경 목록에 있는 파일이 수정되었다. 이 파일 상태는 `설정 → 버전 관리 → 변경 목록`에서 "비활성 변경 목록의 파일 강조 표시" 옵션이 활성화된 경우에 사용할 수 있다. |
| ![hijacked-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/hijacked-darcula.png) None (default color) | Obsolete | 해당 파일은 더 이상 저장소의 작업 사본에 있어서는 안 된다. |
| ![changed-descendants-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/changed-descendants-hcontrast.png) #4FF0FF | Renamed | 마지막 업데이트 이후 파일 이름이 변경되었다. |
| ![hijacked-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/hijacked-darcula.png) None (default color) | Switched | [SVN] 해당 파일은 전체 프로젝트와 다른 브랜치에서 가져왔다. |
| ![unversioned-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/unversioned-darcula.png) D1675A	 | (Unknown) Unversioned | 해당 파일은 로컬에 존재하지만 저장소에 없으며 추가가 예약되어 있지 않다. |
| ![hijacked-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/hijacked-darcula.png) None (default color) | Up to date | 파일이 변경되지 않았다. |

## 2. 편집기의 줄 상태
현재 프로젝트에 VCS 통합이 활성화된 경우, 파일의 수정된 줄은 여백에서 다른 색상으로 표시된다.

`편집기 → 색상 구성표 → VCS 설정 페이지` (`Ctrl` `Alt` `S`)에서 줄 상태 색상을 구성할 수 있다.

수정된 줄의 강조 표시를 비활성화하려면 `버전 관리 → 확인 설정 페이지` (`Ctrl` `Alt` `S`)에서 "거터에서 수정된 줄 강조 표시" 체크박스를 해제한다.

![line-status-in-editor](/assets/img/2025-05-07-intellij-file-status-highlights/line-status-in-editor.png)

아래 표는 기본 선 강조 색상과 일부 색상 구성표에서의 의미를 나열한다.

### 1) Light theme

| Color | File Status | Description |
|:-----:|:-----:|:-----:|
| ![modified-lines-light](/assets/img/2025-05-07-intellij-file-status-highlights/modified-lines-light.png) #C3D6E8 | Modified lines | 마지막 업데이트 이후 수정된 줄이다. |
| ![whitespace-modified-lines-light](/assets/img/2025-05-07-intellij-file-status-highlights/whitespace-modified-lines-light.png) #EDDCBC | Whitespace-modified lines | 마지막 업데이트 이후 공백만 수정된 줄이다. |
| ![added-lines-light](/assets/img/2025-05-07-intellij-file-status-highlights/added-lines-light.png) #C9DEC1 | Added lines | 마지막 업데이트 이후 추가된 라인이다. |
| ![gutter-gray](/assets/img/2025-05-07-intellij-file-status-highlights/gutter-gray.png) #9F9F9F | Deleted lines | 마지막 업데이트 이후 삭제된 라인이다. |

### 2) Darcula theme

| Color | File Status | Description |
|:-----:|:-----:|:-----:|
| ![modified-lines-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/modified-lines-darcula.png) #374752 | Modified lines | 마지막 업데이트 이후 수정된 줄이다. |
| ![whitespace-modified-lines-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/whitespace-modified-lines-darcula.png) #4C4638 | Whitespace-modified lines | 마지막 업데이트 이후 공백만 수정된 줄이다. |
| ![added-lines-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/added-lines-darcula.png) #384C38 | Added lines | 마지막 업데이트 이후 추가된 라인이다. |
| ![deleted-line-darcula](/assets/img/2025-05-07-intellij-file-status-highlights/deleted-line-darcula.png) #656E76 | Deleted lines | 마지막 업데이트 이후 삭제된 라인이다. |

### 3) High-contrast theme

| Color | File Status | Description |
|:-----:|:-----:|:-----:|
| ![modified-lines-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/modified-lines-hcontrast.png) #1AABFF | Modified lines | 마지막 업데이트 이후 수정된 줄이다. |
| ![whitespace-modified-lines-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/whitespace-modified-lines-hcontrast.png) #666666 | Whitespace-modified lines | 마지막 업데이트 이후 공백만 수정된 줄이다. |
| ![added-lines-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/added-lines-hcontrast.png) #009924 | Added lines | 마지막 업데이트 이후 추가된 라인이다. |
| ![deleted-line-hcontrast](/assets/img/2025-05-07-intellij-file-status-highlights/deleted-line-hcontrast.png) #FFA14F | Deleted lines | 마지막 업데이트 이후 삭제된 라인이다. |

## [출처 및 참고]
* [https://www.jetbrains.com/help/idea/file-status-highlights.html](https://www.jetbrains.com/help/idea/file-status-highlights.html)
