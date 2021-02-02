--- 
title: Linux 명령어
author: dejavuhyo
date: 2020-11-19 14:15:00 +0900
categories: [OS, Linux]
tags: [linux-commands, commands, 리눅스-명령어, 명령어]
---

## 1. File Commands

### 1) ls
directory listing

### 2) ls -al
formatted listing with hidden files

### 3) cd dir
change directory to *dir*

### 4) cd
change to home

### 5) pwd
show current directory

### 6) mkdir dir
create a directory *dir*

### 7) rm file
delete *file*

### 8) rm -r dir
delete directory *dir*

### 9) rm -f file
force remove *file*

### 10) rm -rf dir
force remove directory *dir*

### 11) cp file1 file2
copy *file1* to *file2*

### 12) cp -r dir1 dir2
copy *dir1* to *dir2* (create dir2 if it doesn't exist)

### 13) mv file1 file2
rename or move *file1* to *file2* (if *file2* is an existing directory, moves *file1* into directory *file2*)

### 14) ln -s file link
create symbolic link *link* to *file*

### 15) touch file
create or update *file*

### 16) cat > file
places standard input into *file*

### 17) more file
output the contents of *file*

### 18) head file
output the first 10 lines of *file*

### 19) tail file
output the last 10 lines of *file*

### 20) tail -f file
output the contents of *file* as it grows, starting with the last 10 lines

## 2. Process Management

### 1) ps
display your currently active processes

### 2) top
display all running processes

### 3) kill pid
kill process id *pid*

### 4) killall proc
kill all processes named proc

### 5) bg
lists stopped or background jobs (resume a stopped job in the background)

### 6) fg
brings the most recent job to foreground

### 7) fg n
brings job *n* to the foreground

## 3. File Permissions

### 1) chmod octal file
change the permissions of file to octal, which can be found separately for user, group, and world by adding:

* 4: read (r)

* 2: write (w)

* 1: execute (x)

> **Examples:**
>
> chmod 777 – read, write, execute for all
>
> chmod 755 – rwx for owner, rx for group and world
>
> For more options, see **man chmod**

## 4. Searching

### 1) grep pattern files
search for *pattern* in *files*

### 2) grep -r pattern dir
search recursively for *pattern* in *dir*

### 3) command | grep pattern
search for *pattern* in the output of *command*

### 4) locate file
find all instances of *file*

## 5. Compression

### 1) tar cf file.tar files
create a tar named *file.tar* containing *files*

### 2) tar xf file.tar
extract the files from *file.tar*

### 3) tar czf file.tar.gz files
create a tar with Gzip compression

### 4) tar xzf file.tar.gz
extract a tar using Gzip

### 5) tar cjf file.tar.bz2
create a tar with Bzip2 compression

### 6) tar xjf file.tar.bz2
extract a tar using Bzip2

### 7) gzip file
compresses *file* and renames it to *file.gz*

### 8) gzip -d file.gz
decompresses *file.gz* back to *file*

## 6. Network

### 1) ping host
ping *host* and output results

### 2) whois domain
get whois information for *domain*

### 3) dig domain
get DNS information for *domain*

### 4) dig -x host
reverse lookup *host*

### 5) wget file
download *file*

### 6) wget -c file
continue a stopped download

## [출처 및 참고]
* <https://fosswire.com/post/2007/08/unixlinux-command-cheat-sheet/>
