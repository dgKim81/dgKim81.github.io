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
식별자 규칙
 - .a-z,비라틴문자, 발음기호, 그리고 언더바(_)로 시작한다.
 - 후속문자는 문자, 언더바(_) 숫자, $를 쓸수 있다.
 *. 그러나 $는 권장하지 않는다. SQL 표준이 아니다.
 - 키워드는 식별자로 쓸수 없다.

무제한 식별자 규칙 따움표로 감싼다.

FOO, foo 그리고 "foo"는 같은 것으로 간주한다. 식별자를 소문자로 취급하고
무제한 식별자는 대소문자를 구분한다.

U&"d\0061t\+000061" 유니코드 사용가능..

식별자의 길이는 63이다. src/include/pg_config_manual.h. 에서 변경 가능..
대소문자를 구분하지 않는다.

문자열 상수

문자열은 홑따움표로 감싼다.

\n, \r 은 특수문자다 \를 표현하려면 \\ 이렇게 연속으로 써야 한다.

달러감싸기 홑따움표가 많은 경우 대체할 수 있다.
$$Dianne's horse$$ : 
$SomeTag$Dianne's horse$SomeTag$ : 

비트 상수 문자열 : B'1001'
헥사 상수 문자열 : X'1001'

숫자형 상수
digits
digits.[digits][e[+-]digits]
[digits].digits[e[+-]digits]
digitse[+-]digits

x,o,b 대소문자 가리지 않음.
0xhexdigits
0ooctdigits
0bbindigits

숫자형을 언더바로 구분할 수 있다. 이때 언더바는 처리되지 않는다.
1_500_000_000
0b10001000_00000000
0o_1_755
0xFFFF_FFFF
1.618_034

언더바 제약 
시작 종료에 쓸수 없음. 
Underscores are not allowed at the start or end of a numeric constant or a group of digits (that is, immediately before or after the decimal point or the exponent marker), and more than one underscore in a row
is not allowed

숫자형은 처음에는 integer로 간주 그렇지 않으면 bigint로 간주되고
안맞으면 numberic으로 간주된다.

강제 변환
REAL '1.23' -- string style
1.23::REAL -- PostgreSQL (historical) style

그외 타입
type 'string'
'string'::type
CAST ( 'string' AS type )

컬럼에 바로 활당 되는 상수는 암묵적 타입 캐스팅이 된다.

typename ( 'string' )

#연산자
+ - * / < > = ~ ! @ # % ^ & | ` ?

-- 과 /*는 주석이다.