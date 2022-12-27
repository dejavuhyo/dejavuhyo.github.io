---
title: Linux crontab
author: dejavuhyo
date: 2022-01-26 09:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-crontab, crontab, 리눅스-크론탭, 크론탭]
---

## 1. crontab
정기적으로 지정한 시간에 실행하고 싶은 명령어를 등록한다. 스크립트를 등록해도 된다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:-----:|:-----:|
| -l | 등록된 명령어 리스트 확인 |
| -e | 등록된 명령어를 수정 |

## 3. 사용 예제

### 1) 크론탭 등록
`-e` 옵션으로 실행하면 크론탭을 등록하기 위한 파일이 열린다. vi에디터와 동일한 명령으로 필요한 명령어를 등록할 수 있다.

```shell
# 크론탭을 등록
$ crontab -e

# 등록된 크론탭을 확인
$ crontab -l
```

### 2) 크론탭 주기
크론탭을 등록할 때는 실행하고자 하는 주기와 명령어를 입력한다. 주기는 분, 시, 일, 월, 요일의 형태로 입력한다.

`*`는 모두를 의미하고 매분, 매시, 매일, 매월, 모든 요일에 실행하고 싶을 때 사용한다.

| 주기 | 비고 |
|:-----:|:-----:|
| 분 | 0 ~ 59 |
| 시 | 0 ~ 23 |
| 일 | 1 ~ 31 |
| 월 | 1 ~ 12 |
| 요일 | 0 ~ 7 (0, 7이 일요일) |

```shell
# 입력 형태 
분 시 일 월 요일 명령어

# 매 50분에 time.sh 실행 
50 * * * * /mnt/usr/time.sh

# 매일 1시에 log 로 끝나는 파일을 찾아서 find.log 파일로 저장 
0 1 * * * find -name '*.log' ./ >> /test/log/fin.log 

# 5분 마다 program.sh 실행
*/5 * * * * /home/user/program.sh

# 4-10 시 사이에 1시간마다 program.sh 실행
0 4-10/1 * * * /home/user/program.sh

# 매일 1시, 3시에 program.sh 를 실행하고 로그를 저장
# 크론탭에 입력할 때 %는 오류가 발생하기 때문에 역슬래쉬(\)로 감싸 주어야 함 
0 1,3 * * /home/user/program.sh >> /home/user/logs/`date -u +\%Y\%m\%d.\%H\%M.log` 2>&1
```

### 3) echo로 크론탭 등록
크론탭을 일괄로 등록하고 싶을 때 echo 명령을 이용하여 처리할 수 있다. `/var/spool/cron/유저명`에 유저별 크론탭이 있다. 여기에 넣어주면 `crontab -e`와 동일한 효과를 얻을 수 있다.

```shell
sudo bash -c 'echo \"
# hadoop log cleansing
0 1 * * * find /var/log/hadoop -not -name \"*.gz\" -type f -mtime +2 -exec gzip {} \;
0 1 * * * find /var/log/hadoop -name \"*.gz\" -mtime +14 -delete \" >> /var/spool/cron/user_name'
```

## 4. 크론탭 위치
크론탭 파일은 OS에 따라 위치가 다를 수 있다.

* CentOS
  - /var/spool/cron/유저명

* Ubuntu
  - /var/spool/cron/crontab/유저명

## [출처 및 참고]
* <https://wikidocs.net/82568>
