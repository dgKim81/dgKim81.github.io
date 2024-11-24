---
title: PostgreSQL 설치
description: >-
  최근 칭찬하는 글만 보았다. PostgreSQL 을 설치해보자.
author: dgkim
date: 2024-11-24 17:58:00 +0900
categories: [데이터베이스]
tags: [PostgreSQL]
pin: false
media_subpath: '/posts/20241124'
comments: true
---
요즘 또 눈에 띄는 PostgreSQL에 갑자기 관심이 생겼다. 도대체 어떤 녀석이길래 이렇게나
핫한지 한번 해보고 싶었다.

# 소개
PostgreSQL은 ORDBMS 베이스이다. 내가 알고 있는 RDBMS에서 O가 더 붙었다. ORDBMS는 Object-Relational Database Management System 이다.

***ORDBMS*** 객체 지향 데이터베이스와 관계형 데이터베이스가 혼재 된 개념이다.

무료 오픈소스, 유연한 객체 생성, 상속, 함수 그리고 상당한 속도와 상용DB 못지 않게 상당 수준의 가용성을 갖추고 있다고 어디서 본 기억이 있다. 그리고 ANSI-SQL 표준을 따른다고 하였다.

# 설치
공식 사이트(<https://www.postgresql.org/>)에서 설치파일을 다운 받을 수 있다.
집의 컴퓨터가 windows x64 기반이여서 그에 맞춰 파일을 다운받았다.

다운받은 설치 파일 실행하기.

![설치 시작](<화면 캡처 2024-11-24 190012.png>){: width="972" height="589" }

적당한 위치를 선택하고, 컴포넌트는 모두 설치했다.

![컴포넌트선택](<화면 캡처 2024-11-24 190129.png>){: width="972" height="589" }

다른 것은 기본값으로 그대로 설치해도 괜찮지만 비밀번호는 잘입력하고 잘 기억하는 것이 좋을것 같다.

![비밀번호입력](<화면 캡처 2024-11-24 190321.png>){: width="972" height="589" }

stack builder은 옵션인듯해서 건너 뛰었다. 아래와 같이 선택후 Finish를 클릭하면
선택할 수 있는 목록이 나타나는데 일단은 대충 훑어보고 말았다.
![설치 시작](화면 캡처 2024-11-24 190012.png){: width="972" height="589" }

메뉴얼에서는 설치를 종료하고 나서 해당 위치로 이동한 다음 아래의 명령을 치면 데이터베이스가 생성된다고 되어 있었다.
``` cmd
createdb mydb
```
하지만 그렇게 쉽게 풀릴리가 없지..

![첫번째 시도](<화면 캡처 2024-11-24 191941.png>){: width="972" height="589" }

새로 설치된 프로그램 중에 pgAdmin이란 프로그램이 있었다.

그프로그램을 실행하고 로그인한 다음 사용자를 살펴 보았다.

![첫번째 시도](<화면 캡처 2024-11-24 193725.png>){: width="972" height="589" }

마지막에 postgres 라는 user가 보였다. 입력하면 
``` cmd
PS E:\PostgreSQL\17\bin> .\createdb --h

createdb 프로그램은 PostgreSQL 데이터베이스를 만듭니다.

사용법:
  createdb [옵션]... [DB이름] [설명]

옵션들:
  -D, --tablespace=TABLESPACE  데이터베이스를 위한 기본 테이블스페이스
  -e, --echo                   서버로 보내는 작업 명령들을 보여줌
  -E, --encoding=ENCODING      데이터베이스 인코딩
  -l, --locale=LOCALE          데이터베이스의 로캘 설정
      --lc-collate=LOCALE      데이터베이스의 LC_COLLATE 설정
      --lc-ctype=LOCALE        데이터베이스의 LC_CTYPE 설정
      --builtin-locale=LOCALE  builtin locale setting for the database
      --icu-locale=LOCALE      데이터베이스 ICU 로캘 설정
      --icu-rules=RULES        데이터베이스 ICU 룰 설정
      --locale-provider={builtin|libc|icu}
                               locale provider for the database's default collation
  -O, --owner=OWNER            데이터베이스 소유주
  -S, --strategy=STRATEGY      데이터베이스 만드는 전략(wal_log 또는 file_copy)
  -T, --template=TEMPLATE      복사할 템플릿 데이터베이스
  -V, --version                버전 정보를 보여주고 마침
  -?, --help                   이 도움말을 보여주고 마침

연결 옵션들:
  -h, --host=HOSTNAME          데이터베이스 서버 호스트나 소켓 디렉터리
  -p, --port=PORT              데이터베이스 서버 포트
  -U, --username=USERNAME      접속할 사용자
  -w, --no-password            암호 프롬프트 표시 안 함
  -W, --password               암호 프롬프트 표시함
  --maintenance-db=DBNAME      대체용 관리 대상 데이터베이스

초기값으로, DB이름을 지정하지 않으면, 현재 사용자의 이름과 같은 데이터베이스가 만들어집니다.
```

이번에는 사용자를 지정해서 다시 시도 하였다.

``` PowerShell
PS E:\PostgreSQL\17\bin> .\createdb mydb -U postgres
암호:
```
암호는 설치시 입력했던 암호를 입력하면 되었다.

처음으로 PostgreSQL의 DB를 만들었다.
