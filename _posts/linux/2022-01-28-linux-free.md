---
title: Linux free
author: dejavuhyo
date: 2022-01-28 09:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-free, free, 리눅스-free]
---

## 1. free
메모리 사용량을 확인하는 명령어 이다.

## 2. 주요 옵션

| 옵션 | 내용 |
|:-----:|:-----:|
| `-h` | 사람이 읽을 수 있는 GB, MB, KB 형태로 변경하여 출력 |
| `-s [second]` | 지정한 초(second) 마다 이용량 출력 |

## 3. 사용예제
메모리 사용량 확인

| 이름 | 내용 |
|:-----:|:-----:|
| total | 전체 메모리 용량 |
| used | 사용중인 메모리 용량 |
| free | 유휴 메모리 용량 |
| shared | 공유 메모리 용량. 프로세서, 스레드간 통신을 위해 사용 |
| buffers | 버퍼 메모리 용량. 파일 저장을 위한 임시 저장 공간 등 |
| cached | 캐쉬 메모리 용량. 자주 사용하는 데이터를 메모리에 캐슁하여 IO 속도 증가 |

```shell
$ free 
             total       used       free     shared    buffers     cached
Mem:       3921016    1925576    1995440        144     159096     999700
-/+ buffers/cache:     766780    3154236
Swap:            0          0          0

$ free -h
             total       used       free     shared    buffers     cached
Mem:          3.7G       1.8G       1.9G       144K       155M       976M
-/+ buffers/cache:       748M       3.0G
Swap:           0B         0B         0B
```

## 4. 주기적인 메모리 사용량 확인
메모리 사용량을 주기적으로 확인하는 방법은 -s 옵션을 이용하는 방법과 watch 명령을 이용하는 방법이 있다.

```shell
# 1초에 한번씩 메모리 사용량 출력
$ free -h -s 1
             total       used       free     shared    buffers     cached
Mem:          3.7G       1.8G       1.9G       144K       155M       976M
-/+ buffers/cache:       748M       3.0G
Swap:           0B         0B         0B

             total       used       free     shared    buffers     cached
Mem:          3.7G       1.8G       1.9G       144K       155M       976M
-/+ buffers/cache:       748M       3.0G
Swap:           0B         0B         0B

$ watch free -h
Every 2.0s: free -h
Wed May  8 01:32:49 2019

             total   used       free     shared    buffers     cached
Mem:          3.7G   1.8G       1.9G       144K       155M       976M
-/+ buffers/cache:   750M       3.0G
Swap:           0B     0B         0B
```

## [출처 및 참고]
* [https://wikidocs.net/36771](https://wikidocs.net/36771)
