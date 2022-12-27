---
title: Oracle Data Pump
author: dejavuhyo
date: 2021-08-20 06:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-data-pump, data-pump, oracle-pump, oracle-expdp, oracle-impdb, expdp, impdb, 오라클-데이터-펌프, 오라클-펌프, 데이터-펌프]
---

## 1. Oracle 데이터 펌프
Oracle Data Pump는 이전 Oracle 버전에서 사용된 'exp' 및 'imp' 유틸리티에 대한 보다 새롭고 빠르고 유연한 대안이다. 기본 가져오기 및 내보내기 기능 외에도 PL/SQL API와 외부 테이블을 지원한다.

## 2. Table Exports/Imports
TABLES 매개 변수는 내보낼 테이블을 지정하는 데 사용된다. 다음은 테이블 내보내기 및 가져오기 구문의 예이다.

```sql
expdp scott/tiger@db10g tables=EMP,DEPT directory=TEST_DIR dumpfile=EMP_DEPT.dmp logfile=expdpEMP_DEPT.log
```

```sql
impdp scott/tiger@db10g tables=EMP,DEPT directory=TEST_DIR dumpfile=EMP_DEPT.dmp logfile=impdpEMP_DEPT.log
```

## 3. Schema Exports/Imports
exp의 OWNER 매개 변수가 내보낼 스키마를 지정하는 데 사용되는 SCHEMAS 매개 변수로 대체되었다. 다음은 스키마 내보내기 및 가져오기 구문의 예이다.

```sql
expdp scott/tiger@db10g schemas=SCOTT directory=TEST_DIR dumpfile=SCOTT.dmp logfile=expdpSCOTT.log
```

```sql
impdp scott/tiger@db10g schemas=SCOTT directory=TEST_DIR dumpfile=SCOTT.dmp logfile=impdpSCOTT.log
```

## 4. Database Exports/Imports
FULL 매개 변수는 전체 데이터베이스 내보내기가 필요함을 나타낸다. 다음은 전체 데이터베이스 내보내기 및 가져오기 구문의 예이다.

```sql
expdp system/password@db10g full=Y directory=TEST_DIR dumpfile=DB10G.dmp logfile=expdpDB10G.log
```

```sql
impdp system/password@db10g full=Y directory=TEST_DIR dumpfile=DB10G.dmp logfile=impdpDB10G.log
```

## 5. INCLUDE and EXCLUDE
INCLUDE 및 EXCLUDE 매개 변수를 사용하여 내보내기/가져오기를 특정 개체로 제한할 수 있다.

INCLUDE 매개 변수를 사용하면 매개 변수에 의해 지정된 개체만 내보내기/가져오기에 포함된다. EXCLUDE 매개 변수를 사용하면 해당 매개 변수에 의해 지정된 개체를 제외한 모든 개체가 내보내기/가져오기 작업에 포함된다.

두 매개 변수는 상호 배타적이므로 필요한 결과를 제공하기 위해 최소 항목이 필요한 매개 변수를 사용한다. 두 매개 변수의 기본 구문은 동일하다.

```sql
expdp scott/tiger@db10g schemas=SCOTT include=TABLE:"IN ('EMP', 'DEPT')" directory=TEST_DIR dumpfile=SCOTT.dmp logfile=expdpSCOTT.log
```

```sql
expdp scott/tiger@db10g schemas=SCOTT exclude=TABLE:"= 'BONUS'" directory=TEST_DIR dumpfile=SCOTT.dmp logfile=expdpSCOTT.log
```

## 6. CONTENT and QUERY
CONTENT 매개 변수를 사용하여 내보내기 내용을 변경할 수 있다. 다음 명령은 METADATA_ONLY 매개변수 값을 사용하여 데이터 없이 스키마의 내용을 내보낸다.

```sql
expdp system/password@db10g schemas=SCOTT directory=TEST_DIR dumpfile=scott_meta.dmp logfile=expdp.log content=METADATA_ONLY
```

메타데이터 없이 데이터를 캡처하려면 DATA_ONLY 매개 변수 값을 사용한다.

```sql
expdp system/password@db10g schemas=SCOTT directory=TEST_DIR dumpfile=scott_data.dmp logfile=expdp.log content=DATA_ONLY
```

QUERY 매개 변수를 사용하여 하나 이상의 테이블에서 내보낸 행을 변경할 수 있다. 다음 예제에서는 전체 데이터베이스 내보내기를 수행하지만 EMP 및 DEPT 테이블에 대한 데이터는 포함하지 않는다.

```sql
expdp system/password@db10g full=Y directory=TEST_DIR dumpfile=full.dmp logfile=expdp_full.log query='SCOTT.EMP:"WHERE deptno=0",SCOTT.DEPT:"WHERE deptno=0"'
```

## 7. Network Exports/Imports (NETWORK_LINK)
NETWORK_LINK 매개 변수는 네트워크 내보내기/가져오기 소스로 사용할 데이터베이스 링크를 식별한다. 다음 데이터베이스 링크를 사용하여 해당 사용을 시연한다.

```sql
CONN / AS SYSDBA
GRANT CREATE DATABASE LINK TO test;

CONN test/test
CREATE DATABASE LINK remote_scott CONNECT TO scott IDENTIFIED BY tiger USING 'DEV';
```

내보내기의 경우 NETWORK_LINK매개변수는 소스 서버를 가리키는 데이터베이스 링크를 식별한다. 개체는 일반적인 방식으로 원본 서버에서 내보내지지만 원본 서버의 디렉터리 개체가 아닌 로컬 서버의 디렉터리 개체에 기록된다. 로컬 및 원격 사용자 모두 EXP_FULL_DATABASE에게 부여 된 역할이 필요하다.

```sql
expdp test/test@db10g tables=SCOTT.EMP network_link=REMOTE_SCOTT directory=TEST_DIR dumpfile=EMP.dmp logfile=expdpEMP.log
```

내보내기의 경우 NETWORK_LINK 매개 변수는 원본 서버를 가리키는 데이터베이스 링크를 식별한다. 개체는 일반적인 방법으로 원본 서버에서 내보내지만 원본 서버가 아닌 로컬 서버의 디렉토리 개체에 기록된다. 로컬 및 원격 사용자 모두 EXP_FULL_DATABASE 역할을 부여받아야 한다.

```sql
impdp test/test@db10g tables=SCOTT.EMP network_link=REMOTE_SCOTT directory=TEST_DIR logfile=impdpSCOTT.log remap_schema=SCOTT:TEST
```

## 8. Flashback Exports
exp 유틸리티는 내보내기가 지정 시점으로 일관되어야 함을 나타내기 위해 CONSIGNT=Y 매개변수를 사용했다. 기본적으로 expdp 유틸리티 내보내기는 테이블별로만 일관된다. 내보내기의 모든 테이블을 동일한 시점으로 일치시키려면 FLASHBACK_SCN 또는 FLASHBACK_TIME 매개 변수를 사용해야 한다.

FLASHBACK_TIME 매개 변수 값이 지정된 시간 동안 대략적인 SCN으로 변환된다.

```sql
expdp ..... flashback_time=systimestamp

# In parameter file.
flashback_time="to_timestamp('09-05-2011 09:00:00', 'DD-MM-YYYY HH24:MI:SS')"

# Escaped on command line.
expdp ..... flashback_time=\"to_timestamp\(\'09-05-2011 09:00:00\', \'DD-MM-YYYY HH24:MI:SS\'\)\"
```

당연히 내보내기 작업 중에 데이터에 대한 일관된 보기를 읽을 수 있는 충분한 UNDO 공간이 있는 경우 이전 시간 또는 SCN을 지정하여 내보내기를 이전 시점으로 일관되게 할 수 있다.

SCN을 사용하려면 다음 쿼리 중 하나를 사용하여 현재 SCN을 검색할 수 있다.

```sql
SELECT current_scn FROM v$database;
SELECT DBMS_FLASHBACK.get_system_change_number FROM dual;
SELECT TIMESTAMP_TO_SCN(SYSTIMESTAMP) FROM dual;
```

그런 다음 이 SCN은 FLASHBACK_SCN 매개 변수와 함께 사용된다.

```sql
expdp ..... flashback_scn=5474280
```

## 9. Miscellaneous Information
기존의 확장 유틸리티와 달리 모든 데이터 펌프 '.dmp' 및 '.log' 파일은 클라이언트 시스템이 아닌 Oracle 서버에 생성된다.

PARALLEL 파라미터를 사용하여 데이터 펌프 성능을 개선할 수 있다. 여러 덤프 파일을 만들거나 읽을 수 있도록 DUMPFILE 매개 변수의 '%U' 와일드카드와 함께 사용해야 한다. 가져오는 동안 동일한 와일드카드를 사용하여 여러 파일을 참조할 수 있다.

```sql
expdp scott/tiger@db10g schemas=SCOTT directory=TEST_DIR parallel=4 dumpfile=SCOTT_%U.dmp logfile=expdpSCOTT.log
```

```sql
impdp scott/tiger@db10g schemas=SCOTT directory=TEST_DIR parallel=4 dumpfile=SCOTT_%U.dmp logfile=impdpSCOTT.log
```

## [출처 및 참고]
* <https://oracle-base.com/articles/10g/oracle-data-pump-10g>
