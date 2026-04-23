---
title: 윈도우에 Apache Jena Fuseki 설치
author: dejavuhyo
date: 2026-03-26 17:00:00 +0900
categories: [Database, GraphDatabase]
tags: [apache-jena, jena, jena-fuseki, fuseki, graphdb, rdf, owl, ontology, jena-sparql-server, sparql-server]
---

## 1. Apache Jena Fuseki 다운로드
Apache Jena [다운로드](https://jena.apache.org/download/index.cgi) 사이트에 접속하여 `apache-jena-fuseki-6.0.0.zip` 파일을 다운로드 받는다.

![download-page](/assets/img/2026-03-26-apache-jena-fuseki-install-windows/download-page.png)

## 2. 압축 해제
다운받은 압축 파일을 원하는 위치에 해제 한다.

## 3. Fuseki 서버 실행
윈도우 콘솔(Console) 창을 실행시키고 `apache-jena-fuseki-6.0.0.zip` 파일 압축 해제한 경로로 이동한다.

아래 명령어로 fuseki 서버를 실행시킨다.

```bash
fuseki-server.bat
```

![start-fuseki-server](/assets/img/2026-03-26-apache-jena-fuseki-install-windows/start-fuseki-server.png)

## 4. Fuseki UI에 접속
`http://localhost:3030` 접속하여 Fuseki UI를 확인한다.

![fuseki-server](/assets/img/2026-03-26-apache-jena-fuseki-install-windows/fuseki-server.png)

## [출처 및 참고]
* [https://jena.apache.org/](https://jena.apache.org/)
