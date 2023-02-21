---
title: MariaDB tmp full 해결방법
author: dejavuhyo
date: 2021-07-09 05:00:00 +0900
categories: [Database, MySQL]
tags: [mariadb-tmp-full, tmp-full, errcode-28, mariadb-errcode-28]
---

## 1. tmp란
스토리지 엔진으로부터 받아온 레코드를 order by, group by 혹은 alter table 명령어 수행을 위해 내부적인 임시 테이블을 사용한다.

일반적으로 임시 테이블은 처음엔 메모리에 생성됐다가 설정값 (`max_heap_table_size` 혹은 `tmp_table_size` 값)을 초과하는 경우 디스크로 옮겨진다.

이와 같은 내부적인 임시 테이블은 쿼리 수행이 완료되면 자동으로 삭제된다.

Oracle의 PGA와 같은 역할이다.

## 2. 관련 설정값
 
* max_heap_table_size

* tmp_table_size

* tmp_dir

## 3. 에러 메시지

```text
mysqld: Disk is full writing '/temp001/masvc01/TEST/#sql_5a37_3.MAD' (Errcode: 28 "No space left on device"). Waiting for someone to free space... (Expect up to 60 secs delay for server to continue after freeing disk space)
```

## 4. 해결방법
tmp disk가 full 발생하면, temp 영역을 사용하는 쿼리는 hang 상태가 되기 때문에 temp 영역을 증설하거나 temp를 많이 사용하는 쿼리를 정리해야 한다.

### 1) temp 디스크를 사용하는 thread kill

* 프로세스 확인

```sql
show processlist;
```

* copying to tmp table 상태인 쿼리 프로세스 kill

```sql
kill 프로세스ID;
```

* **copying to tmp table:** 정렬하는 데이터 크기가 `tmp_table_size`, `max_heap_table_size`보다 작아서 memory 내에 tmp 임시테이블 생성

* **copying to tmp disk:** 정렬하는 데이터 크기가 `tmp_table_size`, `max_heap_table_size` 보다 커져서 memory가 아닌 디스크에 tmp 임시 테이블 생성

### 2) temp 영역 여러개 지정
tmpdir 설정에 `:`로 구분하여 복수로 지정할 수 있다. 지정된 tmpdir를 round-robin 방식으로 사용하게 된다.

* my.cnf

```text
[mysqld]

port=3306
socket=/engn001/masvc01/OLDDB/mysql/mysql.sock
basedir=/engn001/masvc01/OLDDB/mysql
datadir=/data001/masvc01/OLDDB
#tmpdir=/data001/masvc01/OLDDB/tmp

tmpdir=/temp001/masvc01/OLDDB:/data001/masvc01/OLDDB
```

### 3) temp 파일 시스템 증설
DB에서 사용 중인 temp 영역을 OS의 해당 경로에서 temp 영역을 증설한다.

* 사용중인 temp 조회

```sql
show variables like 'tmpdir';
```

## [출처 및 참고]
* [https://sarc.io/index.php/mariadb/1058-mariadb-tmp-full](https://sarc.io/index.php/mariadb/1058-mariadb-tmp-full)
