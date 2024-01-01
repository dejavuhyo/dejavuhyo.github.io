---
title: CentOS, RHEL, Fedora에 Python 설치
author: dejavuhyo
date: 2019-03-12 15:50:00 +0900
categories: [DevOps, Linux]
tags: [centos-python-install, python-install, centos-python-install, rhel-python-install, fedora-python-install, python-설치, 파이썬-설치]
---

## 1. Python
Python is a powerful programming language. It is very friendly and easy to learn. At writing time of this article Python 3.7.2 latest stable version is available to download and install. This tutorial will help you to install Python 3.7.2 on your CentOS, Red Hat & Fedora operating systems.

## 2. Requirements
This Python installation required GCC compiler on your system. Login to your server using ssh or shell access. Now, use the following command to install prerequisites for Python before installing it.

```shell
[root@localhost local]# yum install gcc openssl-devel bzip2-devel
```

## 3. Download Python
Download Python using the following command from python official site. You can also download the latest version in place of specified below.

```shell
[root@localhost local]# cd /usr/local/
[root@localhost local]# wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tgz
```

Now extract the downloaded package.

```shell
[root@localhost local]# tar xzf Python-3.7.2.tgz
```

## 4. Install Python
Use below set of commands to compile Python source code on your system using altinstall.

```shell
[root@localhost local]# cd Python-3.7.2
[root@localhost local]# ./configure --enable-optimizations
[root@localhost local]# make altinstall
```

make altinstall is used to prevent replacing the default python binary file `/usr/bin/python`.

## 5. Check Python Version
Check the latest version installed of python. Use command python3.7 instead of just python.

```shell
[root@localhost local]# python3.7 -V
Python 3.7.2
```

## 6. Error
No module named '_ctypes'

```shell
[root@localhost local]# yum install libffi-devel
```

## [출처 및 참고]
* [https://tecadmin.net/install-python-3-7-on-centos/](https://tecadmin.net/install-python-3-7-on-centos/)
