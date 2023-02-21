---
title: MySQL Too Many Connections 에러
author: dejavuhyo
date: 2021-08-03 06:00:00 +0900
categories: [Database, MySQL]
tags: [mysql-too-many-connections, too-many-connections, mysql-max-connections, mysql-connections, max-connections, connections]
---

## 1. 오류 메시지

```text
com.mysql.jdbc.exceptions.jdbc4.MySQLNonTransientConnectionException: Too many connections
```

## 2. 설정확인

### 1) 현재 max_connections 확인

```sql
mysql> show variables like 'max_connections';
```

### 2) Max_used_connections 확인

```sql
mysql> show status like 'Max_used_connections';
```

### 3) 클라이언트 차단, 커넥션 차단 확인

```sql
mysql> show status like 'Aborted%';
```

## 3. 해결방법

### 1) 온라인 설정
즉시 적용된다.

```sql
mysql> set global max_connections=500;
```

### 2) 영구 설정

* 편집

```shell
[root@localhost ~]# vi /etc/my.cnf
```

* 추가

```text
[mysqld]
max_connections = 500
```

* 재시작

```shell
[root@localhost ~]# service mysqld restart
```

## [출처 및 참고]
* [https://zetawiki.com/wiki/MySQL_max_connections_%EC%84%A4%EC%A0%95](https://zetawiki.com/wiki/MySQL_max_connections_%EC%84%A4%EC%A0%95)
