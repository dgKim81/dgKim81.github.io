---
title: PostgreSQL 기본 - 데이터타입
description: >-
  데이터 타입 관련 - 정리 중...
author: dgkim
date: 2024-11-30 10:13:00 +0900
categories: [데이터베이스]
tags: [PostgreSQL]
pin: false
media_subpath: '/posts/20241130'
comments: true
---
# 데이터 타입
### 데이터 타입 개요. 이름, 별칭, 설명
- ***bigint*** : int8, 8byte 정수(SQL 스펙)
- ***bigserial*** : serial8, 8byte 자동 증가
- ***bit[(n)]*** : 고정길이 비트 문자(SQL 스펙)
- ***bit varying[(n)]***: varbit[(n)], 가변 길이 문자(SQL 스펙)
- ***boolean*** :bool, true/false (SQL 스펙)
- ***box***: 평면 위의 사각형 
- ***bytea***: 바이너리 데이터("바이트 배열")
- ***character[(n)]***: char[(n)], 고정 길이 문자열(SQL 스펙)
- ***character varying[(n)]***: varchar[(n)], 가변 길이 문자열(SQL 스펙)
- ***cidr***: IPv4 또는 IPv6 네트워크 어드레스
- ***circle***: 평면 위의 원
- ***date***: 달력의 날짜 (연,월일) (SQL 스펙)
- ***double precision***: float8, 8바이트 부동 소수점 숫자 (SQL 스펙)
- ***inet***: IPv4 또는 IPv6 호스트 어드레스
- ***integer***: int, int4, 4바이트 정수 (SQL 스펙)
- ***interval[fileds][(p)]***: 타임 스탬 (SQL 스펙)
- ***json***: 텍스트 JSON 데이터
- ***jsonb***: 해체된(?) 바이너리 JSON data
- ***line***: 평면 위의 직선
- ***lseg***: 평면 위의 선분
- ***macaddr***: MAC (Media Access Control) 주소
- ***macaddr8***: MAC (Media Access Control) 주소 (EUI-64 포맷)
- ***money***: 통화 금액
- ***numeric[(precision, scale)]***: 선택 가능한 정밀도의 숫자 (SQL 스펙)
- ***path***: 평면 위의 기하 경로
- ***pg_lsn***: PostgreSQL 로그 순차 번호
- ***pg_snapshot***: 사용자 레벨의 트랜잭션 ID 스냅샷(deprecated)
- ***point***: 평명 위의 기하 점
- ***real***: float4, 단 정밀도 부동 소수점 숫자 (SQL 스펙)
- ***smallint***: int2, 2byte 정수 (SQL 스펙)
- ***smallserial***: serial2, 2byte 자동증가 정수
- ***serial***: serial4, 4byte 자동증가 정수
- ***text***: 가변 길이 문자열
- ***time[(p)][ without time zone]***: 하루의 시간(타임 존 없음) (SQL 스펙)
- ***time[(p)] with time zone*** : timez, 타임 존을 포함한 하루의 시간 (SQL 스펙)
- ***timestamp[(p)][ without time zone]***: 날짜시간(타임 존 없음) (SQL 스펙)
- ***timestamp[(p)] with time zone***: timestampz, 타임 존을 포함한 날짜시간 (SQL 스펙)
- ***tsquery***: 텍스트 검색 쿼리
- ***tsvector***: 텍스트 검색 문서
- ***txid_snapshot***: 사용자 레벨의 트랜잭션 ID 스냅샷
- ***uuid***: 범용 고유 식별자
- ***xml***: XML 데이터

## numeric 타입 특징
```SQL
NUMERIC(3, 1)
```
- -99.9 부터 99.9까지 저장 할 수 있다.

```SQL
NUMERIC(2, -3)
```
- -99000 부터 99000 까지 저장 할 수 있다.

```SQL
NUMERIC(3, 5)
```
- -0.00999부터 0.00999 까지 저장 할 수 있다.

그리고 Infinity 양의 무한대, -Infinity 음의 무한대, NaN (숫자가 아님) 이런 값들을 가질 수 있다.
이 특징은 부동 소수점 자리도 마찬가지다

## interval 타입

시간 단위를 제한 할 수 있는 옵션
- YEAR
- MONTH
- DAY
- HOUR
- MINUTE
- SECOND
- YEAR TO MONTH
- DAY TO HOUR
- DAY TO MINUTE
- DAY TO SECOND
- HOUR TO MINUTE
- HOUR TO SECOND
- MINUTE TO SECOND

```sql
INTERVAL '1-2' YEAR TO MONTH; -- 1년 2개월
INTERVAL '5 12:34:56' DAY TO SECOND; -- 5일 12시간 34분 56초
```
### interval 단위 입력 관련
- P [ years-months-days ] [ T hours:minutes:seconds ]
```sql
SELECT '2 years 15 months 100 weeks 99 hours 123456789 milliseconds'::interval;
```

```sql
SELECT 'P1Y2M3DT4H5M6S'::interval;
```

## 날짜시간 관련 특수 값
- ***epoch***  : date, timestamp   
    - 1970-01-01 00:00:00+00 (유닉스 시스템 0 시간)
- ***infinity***: date, timestamp, interval
    - 다른 모든 시간보다 뒤의 시간
- ***-infinity***: date, timestamp, interval
    - 다른 모든 시간보다 전의 시간
- ***now***: date, timestamp
    - 현재 시간
- ***today***: date, timestamp
    - 오늘 자정
- ***tomorrow***: date, timestamp
    - 내일 자정
- ***yesterday***: date, timestamp  
    - 어제 자정
- ***allballs***: 
    - 00:00:00.00 UTC

