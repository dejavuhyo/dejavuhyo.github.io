---
title: Git 버전 관리 무시 목록 설정
date: 2021-05-21 06:20:00 +0900
categories: [DevOps, Git]
tags: [git, git-ignore, ignore, 깃, 깃-무시]
---

## 1. Git ignore
특정 파일은 git에서 버전 관리 대상에서 제외해야 할 경우가 있다.

maven이나 bundle, composer 같은 패키지 관리자를 사용할 경우 다운받은 외부 패키지는 버전 관리 대상에서 제외해야 한다.

프로젝트에서 배포 등의 외부에 ssh로 연결한다면 개인 키가 프로젝트 폴더 내에 있을 수 있으며 이런 중요한 설정 정보는 git 저장소에 추가하지 않는 것이 보안과 유지 보수상 필요하다.

git은 이런 문제를 방지하기 위해 무시 목록(ignore list)을 .gitignore에 작성하면 해당 파일은 git add 명령 시 자동으로 제외된다.

## 2. 사용 방법
.gitignore의 각 라인마다 무시할 파일이나 디렉터리의 패턴을 적어주면 되며 여러 파일을 지정할 경우 '*'를 사용한다.

예를 들어, java 프로젝트일 경우 DB 연결 정보를 담고 있는 jdbc.properties라는 파일과 .class와 .jar 확장자를 가진 파일을 무시할 경우 다음과 같이 작성한다.

```text
jdbc.properties
*.class
*.jar
```

git add 명령 실행 시 위 조건에 맞는 파일들은 자동으로 제외된다.

> 이미 추가되어 버전 관리 중인 파일에는 영향을 미치지 않으며 새로 add 할 경우에만 적용된다.

만약 ignore 목록에 있어도 추가해야 할 경우 -f(force) 옵션을 사용해서 추가한다.

```bash
[root@localhost ~]# git add -f jdbc.properties
```

## 3. 하위 폴더에 다른 ignore 정책 사용
gitignore에 설정한 정보는 하위 폴더에도 적용된다. 만약 특정 하위 폴더는 다른 ignore 정책을 적용해야 할 경우 해당 폴더에 .gitignore를 만들면 상위 정책과 다르게 적용할 수 있다.

예를 들어, jdbc.properties라는 파일을 무시하지만 config 폴더에 있는 jdbc.properties 파일은 버전 관리를 해야 할 경우 다음과 같이 config 폴더에 .gitignore 파일을 만들고 설정한다.

```text
*
!jdbc.properties
```

위의 의미는 config의 모든 파일을 무시하지만, 예외 케이스로 jdbc.properties는 버전  관리하겠다는 의미이며 '!'는 이전 패턴을 무효 하는 특별한 의미의 문자열이다.

만약 파일명에 '!'가 들어갈 경우 escape 문자인 '\'를 '!'앞에 붙여주면 된다.

## 4. gitignore.io
사용하는 언어나 프레임워크 IDE마다 추가해야 할 패턴들이 있으며 이를 일일이 기억하는 것은 매우 귀찮은 일이다.

이런 귀찮은 일을 대신해 주는 [gitignore.io](https://www.toptal.com/developers/gitignore) 사이트가 있다. 일반적인 무시 패턴은 이 서비스를 통해서 생성하고 여기에 프로젝트의 고유한 특성에 맞는 무시 목록을 추가하면 된다.

![gitignore-io](/assets/img/2021-05-21-git-ignore/gitignore-io.gif)

## [출처 및 참고]
* <https://www.lesstif.com/gitbook/git-ignore-gitignore-54952369.html>
* <https://www.toptal.com/developers/gitignore>
