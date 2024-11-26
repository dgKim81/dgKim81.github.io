---
title: PostgreSQL 기본 - 1
description: >-
  식별자 및 리터럴 규칙 정리.
author: dgkim
date: 2024-11-25 19:20:00 +0900
categories: [데이터베이스]
tags: [PostgreSQL]
pin: false
media_subpath: '/posts/20241125'
comments: true
---
# 식별자 및 리터럴 규칙 정리

## 식별자 규칙
- 식별자는 **소문자 문자(a-z), 비라틴 문자, 발음기호, 언더바(_)**로 시작해야 합니다.
- 후속 문자는 **문자, 언더바(_), 숫자, `$`**를 사용할 수 있습니다.
  - 단, `$`는 SQL 표준이 아니므로 권장되지 않습니다.
- 키워드는 식별자로 사용할 수 없습니다.

### 무제한 식별자 규칙
- **무제한 식별자**는 따옴표로 감싸서 사용합니다.
- 예를 들어, `FOO`, `foo`, 그리고 `"foo"`는 모두 동일한 것으로 간주됩니다.
- 일반 식별자는 **소문자로 취급**되지만, 무제한 식별자는 **대소문자를 구분**합니다.
- PostgreSQL에서는 따옴표로 감싸지 않은 식별자는 자동으로 **소문자**로 변환됩니다. 하지만 따옴표로 감싼 무제한 식별자는 대소문자를 그대로 유지하여 구분합니다.
- 유니코드 문자열을 사용할 수 있습니다: `U&"d\0061t\+000061"`. 이 형식은 유니코드 이스케이프를 사용하여 특정 유니코드 문자를 나타낼 수 있는 기능입니다.
- 식별자의 길이는 기본적으로 **63자**로 제한되며, `src/include/pg_config_manual.h`에서 변경 가능합니다.
- 기본적으로 대소문자를 구분하지 않습니다.

## 문자열 상수
- 문자열은 **홑따옴표**(`'`)로 감쌉니다.
- **특수문자**: `\n`, `\r` 등과 같은 특수 문자를 사용할 수 있으며, `\`를 표현하려면 `\\`와 같이 연속으로 사용합니다.
- **달러 감싸기**: 홑따옴표가 많이 사용될 경우 달러 감싸기를 사용해 가독성을 높일 수 있습니다.
  - 예: `$$Dianne's horse$$`, `$SomeTag$Dianne's horse$SomeTag$`
- **비트 상수 문자열**: `B'1001'`
- **헥사 상수 문자열**: `X'1001'`

## 숫자형 상수
- 기본적인 숫자 표현 방식:
  - `digits`
  - `digits.[digits][e[+-]digits]`
  - `[digits].digits[e[+-]digits]`
  - `digitse[+-]digits`
- 숫자형 리터럴은 **언더바(`_`)**로 구분할 수 있으며, 이는 처리되지 않습니다.
  - 예: `1_500_000_000`, `0b10001000_00000000`, `0xFFFF_FFFF`
- **언더바 제약**
  - 시작과 끝에 언더바를 사용할 수 없습니다.
  - **연속된 언더바**는 허용되지 않으며, 소수점이나 지수 기호 직전 및 직후에도 사용할 수 없습니다.
- 숫자형 상수는 **처음에는 `integer`로 간주**되며, 범위를 벗어나면 **`bigint`**, 최종적으로 맞지 않으면 **`numeric`**으로 간주됩니다.

### 강제 변환
- 문자열 스타일: `REAL '1.23'`
- PostgreSQL 스타일: `1.23::REAL`
- PostgreSQL에서는 `::`를 사용하는 방식이 자주 사용되며, 이는 더 간결하고 명확하게 타입을 변환하는 방법으로 선호됩니다. `CAST`는 SQL 표준에 가깝고, 다른 SQL 데이터베이스와 호환성을 고려할 때 유용합니다.

## 그 외 타입 변환
- `type 'string'`
- `'string'::type`
- `CAST ('string' AS type)`
- 컬럼에 할당되는 상수는 암묵적으로 **타입 캐스팅**됩니다.
- 배열 타입은 `::` 또는 `CAST`를 사용하여 명시적으로 캐스팅해야 합니다.

## 연산자
- 기본 연산자: `+`, `-`, `*`, `/`, `<`, `>`, `=`
- 여러 문자로 이루어진 연산자는 `+`, `-`로 끝날 수 없으며, **`~`, `!`, `@`, `#`, `%`, `^`, `&`, `|`, `` ` ``, `?`** 중 하나 이상을 포함한 경우는 허용됩니다.

### 연산자 우선순위
- `.`: 왼쪽, 테이블/컬럼 이름 구분자
- `::`: 왼쪽, 타입 변경 (PostgreSQL 스타일)
- `[]`: 왼쪽, 배열 요소 선택
- `+`, `-`: 오른쪽, 양수, 음수
- `COLLATE`: 왼쪽, 정렬 기준 선택
- `AT`: 왼쪽, 시간대 지정 (AT TIME ZONE, AT LOCAL)
- `^`: 왼쪽, 지수 연산
- `*`, `/`, `%`: 왼쪽, 곱셈, 나눗셈, 나머지 연산
- `+`, `-`: 왼쪽, 덧셈, 뺄셈
- 기타 연산자: 왼쪽, 사용자 정의 연산자 또는 기타 네이티브 연산자
- `BETWEEN`, `IN`, `LIKE`, `ILIKE`, `SIMILAR`: 범위, 집합, 문자열 패턴 매칭
- `<`, `>`, `=`, `<=`, `>=`, `<>`: 비교 연산자
- `IS`, `ISNULL`, `NOTNULL`: NULL 및 특수 비교
- `NOT`: 오른쪽, 논리 부정 연산
- `AND`: 왼쪽, 논리 곱
- `OR`: 왼쪽, 논리 합

## 특수문자
- `$`: 함수 정의의 본문이나 준비된 문(statement)에서 **위치 매개변수**를 나타낼 때 사용됩니다. 다른 경우 식별자나 달러-인용 문자열 상수의 일부로 사용될 수 있습니다.
- `()` : 일반적으로 **표현식을 그룹화**하거나 우선순위를 지정하는 데 사용됩니다. 일부 경우에는 특정 SQL 명령의 고정된 구문에서 사용이 필수적입니다.
- `[]`: **배열의 요소 선택**에 사용됩니다. PostgreSQL에서 배열 데이터 타입을 다루기 위한 중요한 기호입니다.
- `,`: **리스트 요소를 구분**하는 데 사용됩니다.
- `:`: **배열 슬라이스 선택**에 사용됩니다. 특정 SQL 방언에서는 **변수 이름의 접두어**로 사용되기도 합니다.
- `*`: 테이블 행의 모든 필드나 **복합 값**을 나타냅니다. 집계 함수의 인수로 사용할 때는 **명시적인 매개변수를 필요로 하지 않음**을 나타냅니다.
- `.`: **숫자 상수**, **스키마, 테이블, 컬럼 이름을 구분**할 때 사용됩니다.

## 주석
- 한 줄 주석: `--`
- 여러 줄 주석: `/* 주석 내용 */`

## 컬럼 참조
- correlation.columnname (correlation 는 테이블 이름)

## Positional Parameters
``` SQL
CREATE FUNCTION dept(text) RETURNS dept
 AS $$ SELECT * FROM dept WHERE name = $1 $$
 LANGUAGE SQL;
```
$1은 함수의 첫번째 파라미터입니다.

## Subscripts
- expression\[subscript\]
``` SQL
mytable.arraycolumn[4]
mytable.two_d_column[17][34]
$1[10:42]
(arrayfunction(a,b))[42]
```
## 필드 선택
- expression.fieldname

## 함수호출
- function_name ([expression [, expression ... ]] )

## 집계 식 (Aggregate Expressions)
집계식은 쿼리를 통해 선택된 행들을 집계하여 나타낸다. 
``` SQL
aggregate_name (expression [ , ... ] [ order_by_clause ] ) [ FILTER ( WHERE filter_clause ) ]
aggregate_name (ALL expression [ , ... ] [ order_by_clause ] ) [ FILTER ( WHERE filter_clause ) ]
aggregate_name (DISTINCT expression [ , ... ] [ order_by_clause ] ) [ FILTER ( WHERE filter_clause ) ]
aggregate_name ( * ) [ FILTER ( WHERE filter_clause ) ]
aggregate_name ( [ expression [ , ... ] ] ) WITHIN GROUP ( order_by_clause ) [ FILTER ( WHERE filter_clause ) ]
```
1. 각 행을 한번식 입력하여 집계 합니다.
2. ALL은 기본값입니다. 첫번째와 같습니다.
3. 식에서 고유한 값을 찾아 각각 한번식 집계 합니다.
4. 특정 값을 지정하지 않고, 각 행을 집계 합니다.
5. WITHIN GROUP 문법은 ordered-set aggregates와 같이 정렬이 필수인 집계 함수에서 사용됩니다.(이것은 파악 중 입니다.)

## Window Function Calls

``` SQL
function_name ([expression [, expression ... ]]) [ FILTER
 ( WHERE filter_clause ) ] OVER window_name
function_name ([expression [, expression ... ]]) [ FILTER
 ( WHERE filter_clause ) ] OVER ( window_definition )
function_name ( * ) [ FILTER ( WHERE filter_clause ) ]
 OVER window_name
function_name ( * ) [ FILTER ( WHERE filter_clause ) ] OVER
 ( window_definition )
```
***window_definition***
``` SQL
[ existing_window_name ]
[ PARTITION BY expression [, ...] ]
[ ORDER BY expression [ ASC | DESC | USING operator ] [ NULLS { FIRST
 | LAST } ] [, ...] ]
[ frame_clause ]
```
***frame_clause***
``` SQL
{ RANGE | ROWS | GROUPS } frame_start [ frame_exclusion ]
{ RANGE | ROWS | GROUPS } BETWEEN frame_start AND frame_end
 [ frame_exclusion ]
```

***frame_start, frame_end***
``` SQL
UNBOUNDED PRECEDING
offset PRECEDING
CURRENT ROW
offset FOLLOWING
UNBOUNDED FOLLOWING
```
***frame_exclusion***
``` SQL
EXCLUDE CURRENT ROW
EXCLUDE GROUP
EXCLUDE TIES
EXCLUDE NO OTHERS
```

## 배열 생성자
- SELECT ARRAY[1,2,3+4];

## 행 생성자
- SELECT ROW(1,2.5,'this is a test');

## 함수 호출
- 함수 시그니처 : concat_lower_or_upper(a text, b text, uppercase boolean DEFAULT false)

***위치 표기법***
SELECT concat_lower_or_upper('Hello', 'World', true);

***이름 표기법***
SELECT concat_lower_or_upper(a => 'Hello', b => 'World', uppercase => true);

과거버젼
SELECT concat_lower_or_upper(a := 'Hello', uppercase := true, b := 'World');


***혼합 표기법***
SELECT concat_lower_or_upper('Hello', 'World', uppercase => true);

## SQL 표준과 PostgreSQL의 차이점
- PostgreSQL은 표준 SQL과 다른 몇 가지 특징이 있습니다. 예를 들어, `$` 기호는 표준 SQL에서 권장되지 않지만, PostgreSQL에서는 함수나 위치 매개변수를 나타내는 데 사용할 수 있습니다. 이 점을 유의하여 사용하는 것이 좋습니다.
- 타입 변환에서도 PostgreSQL의 `::` 연산자는 표준 SQL과 달리 사용되므로, 다른 데이터베이스와의 호환성을 고려해야 합니다.

