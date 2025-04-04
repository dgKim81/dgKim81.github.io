---
title: 리눅스 디렉토리 구조
description: >-
  리눅스 디렉토리 구조에 대해 정리
author: dgkim
date: 2025-03-20 15:40:00 +0900
categories: [학습]
tags: [Linux, Ubuntu]
pin: false
media_subpath: '/assets/posts/20250220'
comments: true
---

# 📂 Linux 폴더 구조 정리

리눅스 파일 시스템은 **FHS(Filesystem Hierarchy Standard)**를 기반으로 정리되어 있으며, 각 폴더는 특정한 역할을 담당한다.  
이 문서는 리눅스 폴더 구조를 설명하고, 각 디렉터리의 용도를 정리한 것이다.

---

## 🏠 **리눅스 기본 폴더 구조**
```
/
├── bin/            # 기본 실행 파일 (ls, cp, rm 등)
├── sbin/           # 시스템 관리자용 실행 파일 (shutdown, fdisk 등)
├── usr/            # 사용자 프로그램 및 라이브러리
│   ├── bin/        # 추가적인 실행 파일
│   ├── sbin/       # 시스템 관리자용 실행 파일
│   ├── lib/        # 공용 라이브러리
│   ├── local/      # 수동 설치 프로그램
│   ├── share/      # 문서 및 데이터 파일
│   └── include/    # 헤더 파일
├── var/            # 로그 및 동적으로 변하는 데이터
│   ├── log/        # 시스템 및 서비스 로그 파일
│   ├── spool/      # 메일, 프린터 큐
│   ├── cache/      # 애플리케이션 캐시
│   ├── tmp/        # 재부팅 후에도 남는 임시 파일
│   └── lib/        # 실행 중인 프로그램 데이터
├── etc/            # 시스템 설정 파일
├── dev/            # 하드웨어 장치 파일
├── proc/           # 실시간 시스템 정보 (가상 파일 시스템)
├── sys/            # 커널 및 하드웨어 설정 (가상 파일 시스템)
├── run/            # 시스템 시작 후 생성되는 임시 데이터
├── home/           # 사용자 계정별 디렉터리
├── root/           # 관리자(root) 홈 디렉터리
├── tmp/            # 임시 파일 저장소 (재부팅 시 삭제)
├── opt/            # 수동 설치된 프로그램
├── srv/            # 서비스 관련 데이터 저장소
├── boot/           # 부팅 관련 파일 (커널, 부트로더)
├── lib/            # 시스템 라이브러리
├── lib64/          # 64비트 라이브러리
└── lost+found/     # 파일 시스템 복구 데이터
```

## 📌 **리눅스 폴더 상세 설명**

### 🛠 **1. 기본 실행 파일 & 시스템 명령어**
- **`/bin`** → 시스템 필수 실행 파일 (`ls`, `cp`, `rm`, `echo` 등)
- **`/sbin`** → 시스템 관리자 명령어 (`shutdown`, `fdisk`, `mount` 등)

### 🖥 **2. 사용자 프로그램 & 라이브러리 (`/usr`)**
- **`/usr/bin`** → 추가적인 실행 파일 (`vim`, `git`, `wget` 등)
- **`/usr/sbin`** → 관리자용 실행 파일 (`apachectl`, `nginx` 등)
- **`/usr/lib`** → 공용 라이브러리 (`libssl.so`, `libc.so` 등)
- **`/usr/local/`** → 사용자가 수동 설치한 프로그램
- **`/usr/share/`** → 공통 문서, 데이터 (`man` 페이지, 아이콘 등)

### 📂 **3. 설정 파일 (`/etc`)**
- **`/etc/nginx/`** → Nginx 웹 서버 설정
- **`/etc/ssh/`** → SSH 관련 설정 (`sshd_config` 등)
- **`/etc/fstab`** → 마운트할 파일 시스템 설정
- **`/etc/crontab`** → 시스템 크론 작업 (예약 실행)

### 📦 **4. 동적으로 변하는 데이터 (`/var`)**
- **`/var/log/`** → 시스템 로그 (`syslog`, `auth.log`, `dmesg` 등)
- **`/var/spool/`** → 메일, 프린터 큐 데이터
- **`/var/cache/`** → 패키지 매니저(Apt, Yum 등) 다운로드 캐시
- **`/var/tmp/`** → 재부팅 후에도 유지되는 임시 파일

### 💾 **5. 시스템 & 하드웨어 정보**
- **`/dev/`** → 하드웨어 장치 파일 (`/dev/sda`, `/dev/null` 등)
- **`/proc/`** → 실시간 시스템 정보 (`cpuinfo`, `meminfo` 등)
- **`/sys/`** → 커널 및 하드웨어 설정
- **`/run/`** → 시스템 부팅 후 생성되는 임시 데이터

### 🏠 **6. 사용자 및 서비스 관련 폴더**
- **`/home/`** → 개별 사용자 계정 디렉터리 (`/home/user`)
- **`/root/`** → `root` 사용자의 홈 디렉터리
- **`/srv/`** → 서비스 관련 데이터 저장 (웹, FTP 등)
- **`/opt/`** → 서드파티 애플리케이션 설치 디렉터리

### 🚀 **7. 부팅 & 시스템 복구**
- **`/boot/`** → 부팅 관련 파일 (커널, 부트로더 등)
- **`/lost+found/`** → 파일 시스템 복구 데이터 (`fsck` 실행 시 사용)

--- 

## 🔥 **리눅스 폴더 구조 요약**
| 폴더 | 역할 |
|------|------|
| `/bin` | 기본 실행 파일 (`ls`, `cp`, `rm` 등) |
| `/sbin` | 시스템 관리 명령어 (`shutdown`, `fdisk` 등) |
| `/usr/bin` | 추가 실행 파일 (`vim`, `git`, `wget`) |
| `/usr/lib` | 공용 라이브러리 (`libssl.so`, `libc.so`) |
| `/var/log` | 시스템 & 서비스 로그 (`syslog`, `auth.log`) |
| `/var/tmp` | 재부팅 후 유지되는 임시 파일 |
| `/tmp` | 임시 파일 (재부팅 시 삭제) |
| `/etc` | 시스템 설정 파일 (`nginx.conf`, `passwd`) |
| `/dev` | 하드웨어 장치 파일 (`/dev/sda`, `/dev/null`) |
| `/proc` | 실시간 시스템 정보 (`/proc/cpuinfo`) |
| `/srv` | 서비스 데이터 저장 (`/srv/www`, `/srv/ftp`) |
| `/opt` | 수동 설치 프로그램 |
| `/home` | 사용자 디렉터리 (`/home/user`) |
| `/root` | `root` 홈 디렉터리 |