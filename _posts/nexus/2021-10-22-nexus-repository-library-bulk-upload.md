---
title: Nexus Repository에 Library 일괄 등록
author: dejavuhyo
date: 2021-10-22 06:00:00 +0900
categories: [DevOps, Nexus]
tags: [nexus-repository-upload, nexus-bulk-upload, library-upload, library-bulk-upload, nexus-repository-등록, nexus-repository-라이브러리-등록, nexus-라이브러리-등록, nexus-일괄-등록, 라이브러리-일괄-등록]
---

## 1. Library 일괄 등록
[Nexus Repository Manager를 사용해 등록 하는 방법](https://dejavuhyo.github.io/posts/nexus-repository-add/)과 스토리지에 직접 넣는 방법이 있다.

등록해야되는 Library가 많을 경우 스토리지에 직접 넣는 방법을 추천한다.

## 2. 등록 방법

### 1) Repository 직접 등록

* 설치 폴더

![nexus-repository](/assets/img/2021-10-22-nexus-repository-library-bulk-upload/nexus-repository.png)

nexus-버전 폴더는 Nexus Repository 실행에 관련된 폴더이며, sonatype-work 폴더는 Repository 관리 폴더이다.

`/sonatype-work/nexus/storage` 경로에 Repository 목록이 나온다.

![repository-list](/assets/img/2021-10-22-nexus-repository-library-bulk-upload/repository-list.png)

등록하려는 Repository에 라이브러리 복사 후 Nexus Repository Manager에서 해당 Repository를 Update Index를 선택한다. 등록한 파일이 갱신되어 Nexus에 적용된다.

![update-index](/assets/img/2021-10-22-nexus-repository-library-bulk-upload/update-index.png)

### 2) Shell Script
로컬 레파지토리 최상단에서 해당 파일을 생성하고 수행하면 경로에 따라 파일 목록을 텍스트 형태로 생성하고 Nexus REST API를 통해 업로드 하는 형태이다.

* `nexus_bulk_upload.sh`

```shell
#!/bin/bash

files="./files.out"

username="admin"
password="admin123"
nexusurl="http://{넥서스IP}:{넥서스Port}/repository/nexus/"

find . -name '*.*' -type f | cut -c 3- | grep "/" > $files

while read i; do
  echo "upload $i to $nexusurl"
  curl -v -u $username:$password --upload-file $i "$nexusurl$i"
done <$files
```

> username과 password는 넥서스 로그인 아이디와 패스워드이다.

로컬 메이븐 Repository 디렉터리로 이동하여 Git Bash를 열고, `nexus_bulk_upload.sh` 파일을 실행한다.

## [출처 및 참고]
* [https://sound10000w.tistory.com/181](https://sound10000w.tistory.com/181)
* [https://gblee1987.tistory.com/104](https://gblee1987.tistory.com/104)
