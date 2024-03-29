---
title: Subversion(SVN)
author: dejavuhyo
date: 2019-01-16 16:30:00 +0900
categories: [DevOps, Subversion]
tags: [subversion, svn]
---

## 1. SVN이란
서브버전(Subversion)은 자유 소프트웨어 버전 관리 시스템이다. 명령행 인터페이스에서 사용하는 명령어를 따서 “SVN”이라고 줄여서 부르기도 한다. 제한이 있던 CVS를 대체하기 위해 2000년부터 콜랩넷에서 개발되었다. 현재는 아파치 최상위 프로젝트로서 전 세계 개발자 커뮤니티와 함께 개발되고 있다.

서브 버전은 서버-클라이언트 모델을 따른다. 서버는 작업하는 컴퓨터내에 둘 수도 있고, 전산망에 연결된 별도의 컴퓨터에 두고 사용할 수도 있다. 서브버전 서버와 클라이언트는 http, https, svn, svn+ssh의 규약으로 통신한다.

## 2. 기능

* automatical writing 을 지원하므로, 쓰기 도중 중단으로 인한 저장소 내의 불일치나 손상을 피할 수 있다.

* 이름을 바꾸거나, 복사하거나, 파일을 지워도 계정 기록을 유지한다.

* 시스템이 등록부, 개명, 파일 메타데이터에 대해서도 판본 호수를 지정 관리한다. 사용자는 디렉터리 전체를 빠르게 옮기거나 복사하면서도 전체의 개정 이력을 보유할 수 있다.

* 심볼릭 링크도 판본 호수를 지정.

* 이진 파일의 경우 한번 저장한 후 변경될 경우 차이점만 저장하기 때문에 저장소를 효율적으로 사용할 수 있다.

* 아파치 HTTP 서버를 네트워크 서버로, 웹대브/델타-V를 통신규약으로 사용한다. svnserve라는 독립된 서버 프로세스도 있어서 TCP/IP를 통해 전용 통신규약을 사용한다.

* 소스 저장소의 크기에 관계 없이 일정한 시간 안에 가지 치기(branching)나 태그 넣기(tagging)를 할 수 있다.

* 태생적으로 클라이언트-서버, 계층 라이브러리 설계를 채택

* 클라이언트/서버 통신규약이 버전간 차이를 양뱡향으로 보냄

* 소스 저장소로의 접근이 최적화되어 있으므로, 소스 저장고에서 필요 없는 네트워크 트래픽을 줄일 수 있다.

## 3. 기본 용어

* Repository: 프로젝트 파일 및 변경 정보가 저장되는 장소

* Import: 빈 Repository에 맨 처음 파일들을 채우는 것

* Checkout(co): Repository에서 프로젝트 관련 파일들을 받아 오는 것

* Commit(ci): Checkout한 파일의 수정사항을 갱신,Revision 1이 증가

* Revision: commit 할 때 마다 카운트 되는 번호

* Update (up): checkout 한 파일들을 최신의 것으로 갱신, 소스 수정이나 commit 하기 전에 한번씩 해주는 것이 좋다.

* Export: 버전 관리 파일들을 뺀 순수 파일만 빼내는 것

* Tag: 개념적으로 특정 시점에서 프로젝트의 스냅샷을 찍어두는 것이다. 즉 Revision번호 그 자체에 해당한다.

* Brach: 브런치 생성은 복사와 같은 개념이다.

* Truck: 프로젝트에서 가장 중심이 되는 디렉토리로 소스와 파일들이 포함된다.

* Diff: 수정된 파일과 수정 이전 파일을 차이정보를 비교하여 것

## 4. Trunk, branch, tags 관계

![svn-trunk-branch-tags](/assets/img/2019-01-16-subversion/svn-trunk-branch-tags.png)

### 1) Trunk

* 단어 자체의 뜻은 본체, 본문 등이다.

* 프로젝트에서 가장 중심이 되는 디렉토리이다.

* 모든 프로그램의 개발 작업은 trunk 디렉토리에서 이루어 진다.

* trunk 디렉토리 바로 아래에는 소스들의 파일과 디렉토리가 들어가게 된다.

### 2) Branch

* trunk에서 뻗어져 나온 나무가지를 뜻한다.

* 프로그램을 개발하다 보면 trunk 디렉토리에서 또 다른 작은 분류로 빼서 따로 개발해야 할 경우가 있다. 프로젝트안의 작은 프로젝트라고 생각하면 된다.

* branches 디렉토리 안에 또 다른 디렉토리를 두어 그 안에서 개발하게 된다.

### 3) Merge

* Trunk에서 분기된 Branch를 다시 Trunk로 합쳐는 작업

### 4) Tag

* tag는 꼬리표이다.

* 이 디렉토리는 프로그램을 개발하면서 정기적으로 릴리즈 할 때 0.1 / 0.2 / 1.0 등 이런식의 버전 별로 소스 코드를 따로 저장하는 공간이다.

* 버전별로 태그를 붙여서 tag 디렉토리 안에 보관한다고 생각하면 된다.

## 5. 명령어

* add: 파일과 디렉토리를 버전관리 대상에 넣습니다. 저장소에 추가하도록 스케줄링 되며, 다음 commit할 때, 추가된다.

* blame(praise, annotate, ann): 지정한 파일이나 URL의 내용의 수정내역을 각 라인별로 리비전과 작성자를 보여준다.

* cat: 지정한 파일이나 URL의 내용을 출력한다.

* checkout(co): 작업사본을 저장소로부터 꺼낸다.

* cleanup: 제귀적으로 작업 사본을 순회하면서 복사, 제거시 실패한 잠금파일을 삭제하며, 중단된 명령을 재개한다.

* copy(cp): 작업 사본 혹은 저장소의 내용을 이전 로그메시지와 함께 복사한다.

* delete(del, remove, rm): 파일과 디렉토리를 버전 관리 대상에서 제거한다.

* Export: 로컬 시스템에서 정리된 디렉토리 트리 내보내기

* Import: 버전관리 대상이 아닌 파일과 디렉토리를 추가한다.

* Info: 사본이나 저장소의 파일, 디렉토리의 정보를 출력한다.

* list(ls): 저장소에 있는 항목들을 나열해준다.

* Lock: 작업 사본이나 저장소의 URL을 잠군다. 다른 사용자가 변경하여 커밋할 수 없다.

* log: 커밋 로그 메시지

* merge: 변경사항 병합하기

* mkdir: 새 디렉토리 만들기

* move(mv, rename, ren): 작업 사본내 혹은 저장소 안의 파일이나 디렉토리를 이동하거나, 이름을 바꾼다.

* resolve: 작업사본의 파일이나 디렉토리에 발생한 충돌을 해결한다

* Resolved: 작업사본내의 파일이나 디렉토리의 충돌 상황을 제거한다.

* revert: 작업 사본을 받아 왔던 최초 상태로 되돌린다.(작업한 내용을 모두 되돌린다.)

* status(stat, st): 파일/디렉토리의 상태 가져오기 

* switch(sw): 작업 복사본 전환하기

* unlock: 작업사본이나 저장소 URL의 잠금을 해제한다.

* update(up): 작업 복사본 갱신하기

## [출처 및 참고]
* [https://ko.wikipedia.org/wiki/%EC%84%9C%EB%B8%8C%EB%B2%84%EC%A0%84](https://ko.wikipedia.org/wiki/%EC%84%9C%EB%B8%8C%EB%B2%84%EC%A0%84)
* [https://euntark86.blogspot.com/2017/01/svn.html](https://euntark86.blogspot.com/2017/01/svn.html)
