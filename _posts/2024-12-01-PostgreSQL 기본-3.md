---
title: PostgreSQL 기본 - 테이블
description: >-
  테이블 생성과 관련한 정리
author: dgkim
date: 2024-11-30 10:13:00 +0900
categories: [데이터베이스]
tags: [PostgreSQL]
pin: false
media_subpath: '/posts/20241130'
comments: true
---
# DDL 관련
## 테이블 

```sql
CREATE [ [ GLOBAL | LOCAL ] { TEMPORARY | TEMP } | UNLOGGED ] TABLE
 [ IF NOT EXISTS ] table_name ( [
 { column_name data_type [ STORAGE { PLAIN | EXTERNAL |
 EXTENDED | MAIN | DEFAULT } ] [ COMPRESSION compression_method ]
 [ COLLATE collation ] [ column_constraint [ ... ] ]
 | table_constraint | LIKE source_table [ like_option ... ] }
 [, ... ]
] )
[ INHERITS ( parent_table [, ... ] ) ]
[ PARTITION BY { RANGE | LIST | HASH } ( { column_name | ( expression
 ) } [ COLLATE collation ] [ opclass ] [, ... ] ) ]
[ USING method ]
[ WITH ( storage_parameter [= value] [, ... ] ) | WITHOUT OIDS ]
[ ON COMMIT { PRESERVE ROWS | DELETE ROWS | DROP } ]
[ TABLESPACE tablespace_name ]


CREATE [ [ GLOBAL | LOCAL ] { TEMPORARY | TEMP } | UNLOGGED ] TABLE
 [ IF NOT EXISTS ] table_name
 OF type_name [ (
 { column_name [ WITH OPTIONS ] [ column_constraint [ ... ] ]
 | table_constraint }
 [, ... ]
) ]
[ PARTITION BY { RANGE | LIST | HASH } ( { column_name | ( expression
 ) } [ COLLATE collation ] [ opclass ] [, ... ] ) ]
[ USING method ]
[ WITH ( storage_parameter [= value] [, ... ] ) | WITHOUT OIDS ]
[ ON COMMIT { PRESERVE ROWS | DELETE ROWS | DROP } ]
[ TABLESPACE tablespace_name ]


CREATE [ [ GLOBAL | LOCAL ] { TEMPORARY | TEMP } | UNLOGGED ] TABLE
 [ IF NOT EXISTS ] table_name
 PARTITION OF parent_table [ (
 { column_name [ WITH OPTIONS ] [ column_constraint [ ... ] ]
 | table_constraint }
 [, ... ]
) ] { FOR VALUES partition_bound_spec | DEFAULT }
[ PARTITION BY { RANGE | LIST | HASH } ( { column_name | ( expression
 ) } [ COLLATE collation ] [ opclass ] [, ... ] ) ]
[ USING method ]
[ WITH ( storage_parameter [= value] [, ... ] ) | WITHOUT OIDS ]
[ ON COMMIT { PRESERVE ROWS | DELETE ROWS | DROP } ]
[ TABLESPACE tablespace_name ]


where column_constraint is:

[ CONSTRAINT constraint_name ]
{ NOT NULL |
 NULL |
 CHECK ( expression ) [ NO INHERIT ] |
 DEFAULT default_expr |
 GENERATED ALWAYS AS ( generation_expr ) STORED |
 GENERATED { ALWAYS | BY DEFAULT } AS IDENTITY [ ( sequence_options
 ) ] |
 UNIQUE [ NULLS [ NOT ] DISTINCT ] index_parameters |
 PRIMARY KEY index_parameters |
 REFERENCES reftable [ ( refcolumn ) ] [ MATCH FULL | MATCH PARTIAL |
 MATCH SIMPLE ]
 [ ON DELETE referential_action ] [ ON UPDATE referential_action
 ] }
[ DEFERRABLE | NOT DEFERRABLE ] [ INITIALLY DEFERRED | INITIALLY
 IMMEDIATE ]

and table_constraint is:

[ CONSTRAINT constraint_name ]
{ CHECK ( expression ) [ NO INHERIT ] |
 UNIQUE [ NULLS [ NOT ] DISTINCT ] ( column_name
 [, ... ] ) index_parameters |
 PRIMARY KEY ( column_name [, ... ] ) index_parameters |
 EXCLUDE [ USING index_method ] ( exclude_element WITH operator
 [, ... ] ) index_parameters [ WHERE ( predicate ) ] |
 FOREIGN KEY ( column_name [, ... ] ) REFERENCES reftable
 [ ( refcolumn [, ... ] ) ]
 [ MATCH FULL | MATCH PARTIAL | MATCH SIMPLE ] [ ON
 DELETE referential_action ] [ ON UPDATE referential_action ] }
[ DEFERRABLE | NOT DEFERRABLE ] [ INITIALLY DEFERRED | INITIALLY
 IMMEDIATE ]

and like_option is:

{ INCLUDING | EXCLUDING } { COMMENTS | COMPRESSION | CONSTRAINTS |
 DEFAULTS | GENERATED | IDENTITY | INDEXES | STATISTICS | STORAGE |
 ALL }
and partition_bound_spec is:
IN ( partition_bound_expr [, ...] ) |
FROM ( { partition_bound_expr | MINVALUE | MAXVALUE } [, ...] )
 TO ( { partition_bound_expr | MINVALUE | MAXVALUE } [, ...] ) |
WITH ( MODULUS numeric_literal, REMAINDER numeric_literal )
index_parameters in UNIQUE, PRIMARY KEY, and EXCLUDE constraints are:
[ INCLUDE ( column_name [, ... ] ) ]
[ WITH ( storage_parameter [= value] [, ... ] ) ]
[ USING INDEX TABLESPACE tablespace_name ]

exclude_element in an EXCLUDE constraint is:

{ column_name | ( expression ) } [ COLLATE collation ] [ opclass
 [ ( opclass_parameter = value [, ... ] ) ] ] [ ASC | DESC ] [ NULLS
 { FIRST | LAST } ]

referential_action in a FOREIGN KEY/REFERENCES constraint is:

{ NO ACTION | RESTRICT | CASCADE | SET NULL [ ( column_name
 [, ... ] ) ] | SET DEFAULT [ ( column_name [, ... ] ) ] }

```
CREATE TABLE는 비어있는 새로운 테이블을 작성합니다. 이 테이블은 명령을 실행시킨 유저가
소유하게 됩니다.

스키마 이름을 함께 지정할 수 있습니다. (CREATE TABLE myschema.mytable ...) 이럴 경우
특정 스키마에 테이블을 생성합니다. 다만, 임시 테이블은 특별한 스키마가 지정되어 있어 
스키마를 지정할 수 없습니다.
테이블의 이름은 스키마내에서 고유해야 합니다. 

CREATE TABLE는 테이블에 행에 상응하는 복합 타입인 데이터 타입을 자동으로 생성 합니다.
따라서 같은 스키마에 같은 이름의 데이터 타입 이름과 테이블 이름은 같을 수 없습니다.

선택적 제약 조건 절은 삽입 또는 갱신 작업이 성공하기 위해 새로운 행이나 수정된 행이 충족해야 할 제약 조건(테스트)을 명시합니다. 제약 조건은 SQL 객체로, 테이블에서 유효한 값의 집합을 다양한 방식으로 정의하는 데 사용됩니다.

제약 조건을 정의하는 방법에는 두 가지가 있습니다: 테이블 제약 조건과 열 제약 조건입니다. 열 제약 조건은 열 정의의 일부로 작성되며, 테이블 제약 조건은 특정 열에 묶이지 않고 하나 이상의 열을 포함할 수 있습니다. 모든 열 제약 조건은 테이블 제약 조건으로도 작성할 수 있으며, 열 제약 조건은 제약 조건이 한 열에만 영향을 미치는 경우 사용하는 표기상의 편의일 뿐입니다.

테이블을 생성하려면, 각각 모든 열 타입 또는 OF 절에 명시된 타입에 대해 USAGE 권한을 가져야 합니다.

### 파라미터 
#### TEMPORARY 또는 TEMP
명시된 경우, 테이블은 임시 테이블로 생성됩니다. 임시 테이블은 세션 종료 시 자동으로 삭제되며, 선택적으로 현재 트랜잭션 종료 시 삭제될 수도 있습니다(아래 ON COMMIT 참조). 기본 검색 경로(search_path)는 임시 스키마를 우선적으로 포함하므로, 동일한 이름의 기존 영구 테이블은 임시 테이블이 존재하는 동안 새로운 계획에서 선택되지 않습니다. 단, 스키마가 명시된 이름으로 참조할 경우 예외입니다. 임시 테이블에 생성된 모든 인덱스 역시 자동으로 임시 상태가 됩니다.

autovacuum 데몬은 임시 테이블에 접근할 수 없으므로, 임시 테이블에 대해 vacuum 또는 analyze 작업을 수행할 수 없습니다. 따라서, 적절한 vacuum 및 analyze 작업은 세션 SQL 명령을 통해 직접 수행해야 합니다. 예를 들어, 임시 테이블을 복잡한 쿼리에서 사용할 예정이라면, 데이터를 채운 후 해당 임시 테이블에 대해 ANALYZE 명령을 실행하는 것이 현명합니다.

선택적으로, GLOBAL 또는 LOCAL을 TEMPORARY 또는 TEMP 앞에 작성할 수 있습니다. 그러나 PostgreSQL에서는 현재 아무런 차이가 없으며, 이 사용법은 더 이상 권장되지 않습니다. 자세한 내용은 아래 '호환성(Compatibility)' 섹션을 참조하십시오.

#### UNLOGGED
명시된 경우, 테이블은 비로그(unlogged) 테이블로 생성됩니다. 비로그 테이블에 기록된 데이터는 사전 쓰기 로그(write-ahead log)에 기록되지 않아, 일반 테이블보다 속도가 상당히 빠릅니다. 그러나 비로그 테이블은 충돌(crash) 또는 비정상 종료 시 자동으로 잘려 나가므로 안전하지 않습니다. 또한, 비로그 테이블의 내용은 대기 서버로 복제되지 않습니다. 비로그 테이블에 생성된 모든 인덱스 역시 자동으로 비로그 상태가 됩니다.

이것이 명시된 경우, 비로그 테이블과 함께 생성된 모든 시퀀스(identity 또는 serial 컬럼용)는 비로그 상태로 생성됩니다.

#### IF NOT EXISTS
동일한 이름의 릴레이션이 이미 존재하는 경우 에러를 발생시키지 않습니다. 대신, 이 경우 알림이 발행됩니다. 단, 기존 릴레이션이 새로 생성될 릴레이션과 유사하다는 보장은 없습니다.

#### table_name
생성할 테이블의 이름(선택적으로 스키마가 포함된 이름).

#### OF type_name
지정된 복합 타입(선택적으로 스키마가 포함된 이름)에서 구조를 가져오는 타입 테이블(typed table)을 생성합니다. 타입 테이블은 해당 타입에 연결되며, 예를 들어, 해당 타입이 삭제될 경우(DROP TYPE ... CASCADE 명령으로) 테이블도 삭제됩니다.

#### column_name
새로운 테이블에 생성될 열의 이름.

#### data_type
열의 데이터 타입입니다. 여기에는 배열 지정자가 포함될 수 있습니다. PostgreSQL에서 지원하는 데이터 타입에 대한 자세한 내용은 8장을 참조하십시오.

#### COLLATE collation
COLLATE 절은 열에 정렬 순서를 지정합니다(정렬 가능한 데이터 타입이어야 합니다). 지정하지 않을 경우, 열의 데이터 타입에 설정된 기본 정렬 순서가 사용됩니다.

#### STORAGE { PLAIN | EXTERNAL | EXTENDED | MAIN | DEFAULT }
이 설정은 열의 저장 방식을 지정합니다. 이를 통해 열의 데이터가 테이블의 메인 공간(인라인)에 저장될지, 아니면 보조 TOAST 테이블에 저장될지 제어할 수 있습니다. 또한, 데이터에 대해 압축을 사용할지 여부도 이 설정에서 결정합니다.

***PLAIN***은 INTEGER와 같은 고정 길이 값에 사용되며, 인라인으로 저장되고 압축되지 않습니다.
***MAIN***은 인라인으로 저장되며, 압축 가능한 데이터에 사용됩니다.
***EXTERNAL***은 외부에 저장되며 압축되지 않은 데이터를 위한 옵션이고, 
***EXTENDED***는 외부에 저장되며 압축된 데이터를 위한 옵션입니다. 
***DEFAULT***를 작성하면 열의 데이터 타입에 따라 기본 저장 모드로 설정됩니다.

EXTENDED는 PLAIN 저장 방식을 지원하지 않는 대부분의 데이터 타입에서 기본값입니다.

EXTERNAL을 사용하면 매우 큰 텍스트(text) 및 바이트(bytea) 값에서의 substring 작업이 더 빠르게 실행됩니다. 하지만 저장 공간 사용량이 증가하는 단점이 있습니다. 자세한 내용은 섹션 65.2를 참조하십시오.

> TOAST PostgreSQL의 기본 테이블 구조는 제한된 크기의 데이터를 처리하도록 설계되어 있습니다. 이 제한을 넘는 데이터를 저장하려면 특별한 처리가 필요하며, 이때 TOAST 테이블이 사용됩니다.
> PostgreSQL의 기본 테이블 구조는 **한 행(row)**의 크기를 1GB로 제한합니다.
> 페이지 크기(기본 8KB)를 초과하는 데이터는 테이블에 인라인으로 저장되지 못합니다.

```sql
-- 특정 테이블의 TOAST 테이블 이름 확인
SELECT reltoastrelid::regclass AS toast_table
FROM pg_class
WHERE relname = 'your_table_name';
```

#### COMPRESSION compression_method
COMPRESSION 절은 열의 압축 방법을 설정합니다. 압축은 가변 길이 데이터 타입에서만 지원되며, 열의 저장 방식이 MAIN 또는 EXTENDED일 때만 사용됩니다(열 저장 방식에 대한 자세한 내용은 ALTER TABLE을 참조하십시오). 파티션 테이블에서 이 속성을 설정하는 것은 직접적인 영향을 미치지 않는데, 이러한 테이블 자체는 데이터를 저장하지 않기 때문입니다. 하지만 설정된 값은 새로 생성된 파티션에서 상속됩니다. 지원되는 압축 방법은 pglz와 lz4입니다(lz4는 PostgreSQL을 빌드할 때 --with-lz4 옵션을 사용한 경우에만 사용할 수 있습니다). 추가로, compression_method를 default로 설정하면 기본 동작을 명시적으로 지정할 수 있습니다. 기본 동작은 데이터 삽입 시점에 default_toast_compression 설정을 참조하여 사용할 압축 방법을 결정하는 것입니다.

> pglz: 저장 공간 절약이 우선이라면 적합. PostgreSQL에서 기본적으로 제공되며 추가 설정이 필요 없음.
> lz4: 빠른 읽기/쓰기 성능과 실시간 처리가 중요한 환경에 최적.

#### INHERITS ( parent_table [, ... ] )
선택적인 INHERITS 절은 새 테이블이 자동으로 모든 열을 상속받을 테이블 목록을 지정합니다. 부모 테이블은 일반 테이블이거나 외부 테이블일 수 있습니다.

INHERITS를 사용하면 새로 생성된 자식 테이블과 부모 테이블 간에 지속적인 관계가 생성됩니다. 부모 테이블의 스키마 수정 사항은 일반적으로 자식 테이블에 전파되며, 기본적으로 자식 테이블의 데이터는 부모 테이블을 스캔할 때 포함됩니다.

여러 부모 테이블에 동일한 열 이름이 존재할 경우, 각 부모 테이블의 열 데이터 타입이 일치하지 않으면 에러가 발생합니다. 충돌이 없는 경우, 중복된 열들은 새 테이블에서 하나의 열로 병합됩니다. 새 테이블의 열 이름 목록에 상속된 열과 동일한 이름이 포함될 경우, 데이터 타입이 상속된 열과 일치해야 하며 열 정의는 하나로 병합됩니다. 새 테이블이 열에 대해 기본값을 명시적으로 지정한 경우, 이는 상속된 선언의 기본값을 무시하고 우선합니다. 기본값이 명시되지 않은 경우, 부모 테이블 중 기본값을 지정한 테이블들이 모두 동일한 기본값을 지정해야 하며, 그렇지 않으면 에러가 발생합니다.

CHECK 제약 조건은 열과 본질적으로 동일한 방식으로 병합됩니다. 여러 부모 테이블과/또는 새 테이블 정의에 동일한 이름의 CHECK 제약 조건이 포함된 경우, 이 제약 조건들은 모두 동일한 CHECK 표현식을 가져야 하며, 그렇지 않을 경우 에러가 발생합니다. 이름과 표현식이 동일한 제약 조건들은 하나로 병합됩니다. 부모 테이블에 NO INHERIT으로 표시된 제약 조건은 고려되지 않습니다. 또한 새 테이블에 정의된 이름 없는 CHECK 제약 조건은 항상 고유한 이름이 지정되므로 병합되지 않습니다.

열의 STORAGE 설정도 부모 테이블에서 복사됩니다. 부모 테이블의 열이 아이덴티티 열(identity column)인 경우, 해당 속성은 상속되지 않습니다. 필요할 경우 자식 테이블에서 열을 아이덴티티 열로 선언할 수 있습니다.

> 자식 테이블의 데이터도 함께 조회 되는 것을 제어하려면 ONLY 사용
```sql
SELECT * FROM ONLY parent_table;
```
> 아이덴티티 열 상속되지 않음. 필요 시 자식 테이블에서 별도로 선언해야 함.
```sql
ALTER TABLE child_table ALTER COLUMN id ADD GENERATED ALWAYS AS IDENTITY;
```

> **Foreign Table(외부 테이블)**은 PostgreSQL에서 외부 데이터 소스에 접근하기 위해 사용하는 테이블 개념입니다. PostgreSQL은 이 기능을 **Foreign Data Wrapper(FDW)**라는 확장 모듈을 통해 지원하며, 이를 통해 다른 데이터베이스, 파일, 또는 기타 데이터 소스와의 통합이 가능합니다.

#### PARTITION BY { RANGE | LIST | HASH } ( { column_name | ( expression ) } [ opclass ] [, ...] )
선택적인 PARTITION BY 절은 테이블의 파티셔닝 전략을 지정합니다.

- 이 부분은 후에 계속하기로..

#### PARTITION OF parent_table { FOR VALUES partition_bound_spec | DEFAULT }
지정된 부모 테이블의 파티션으로 테이블을 생성합니다.

- 이 부분은 후에 계속하기로..

#### LIKE source_table [ like_option ... ]
LIKE 절은 새 테이블이 지정된 테이블의 모든 열 이름, 데이터 타입, 그리고 NOT NULL 제약 조건을 자동으로 복사하도록 지정합니다.

- 이 부분은 후에 계속하기로..

#### CONSTRAINT constraint_name
열 또는 테이블 제약 조건에 대한 선택적 이름을 지정할 수 있습니다. 제약 조건이 위반될 경우, 에러 메시지에 제약 조건 이름이 포함되므로, col must be positive와 같이 유용한 제약 조건 정보를 클라이언트 애플리케이션에 전달할 수 있습니다. (공백이 포함된 제약 조건 이름을 지정하려면 큰따옴표를 사용해야 합니다.) 제약 조건 이름을 지정하지 않으면, 시스템에서 자동으로 이름을 생성합니다.

#### NOT NULL
해당 열은 NULL 값을 포함할 수 없습니다.

#### NULL
해당 열은 NULL 값을 포함할 수 있습니다. 이는 기본값입니다. 이 절은 비표준 SQL 데이터베이스와의 호환성을 위해 제공되며, 새 애플리케이션에서는 사용을 권장하지 않습니다.

#### CHECK ( expression ) [ NO INHERIT ]
CHECK 절은 Boolean 결과를 생성하는 표현식을 지정하며, 새로 삽입되거나 업데이트된 행이 이 표현식을 만족해야 삽입 또는 업데이트 작업이 성공합니다. TRUE 또는 UNKNOWN으로 평가되는 표현식은 성공하며, 삽입 또는 업데이트 작업 중 하나라도 FALSE를 생성하면 오류가 발생하고 데이터베이스는 변경되지 않습니다. 열 제약 조건으로 지정된 CHECK 제약 조건은 해당 열의 값만 참조해야 하며, 테이블 제약 조건의 표현식은 여러 열을 참조할 수 있습니다.

현재 CHECK 표현식에는 하위 쿼리를 포함할 수 없으며, 현재 행의 열 외의 변수는 참조할 수 없습니다(섹션 5.5.1 참조). 시스템 열 tableoid는 참조할 수 있지만, 다른 시스템 열은 참조할 수 없습니다.

NO INHERIT로 표시된 제약 조건은 자식 테이블로 전파되지 않습니다.

테이블에 여러 CHECK 제약 조건이 있는 경우, NOT NULL 제약 조건을 확인한 후 이름의 알파벳 순서로 각 행에 대해 테스트됩니다. (PostgreSQL 9.5 이전 버전에서는 CHECK 제약 조건의 실행 순서를 보장하지 않았습니다.)

#### DEFAULT default_expr
DEFAULT 절은 해당 열 정의 내에서 나타나는 열에 기본 데이터 값을 할당합니다. 기본값은 변수 없이 작성된 표현식이어야 하며(특히, 현재 테이블의 다른 열을 참조하는 것은 허용되지 않음), 하위 쿼리도 사용할 수 없습니다. 기본값 표현식의 데이터 타입은 열의 데이터 타입과 일치해야 합니다.

기본 표현식은 해당 열에 값을 지정하지 않은 삽입 작업에서 사용됩니다. 열에 기본값이 지정되지 않은 경우, 기본값은 NULL입니다.

#### GENERATED ALWAYS AS ( generation_expr ) STORED
이 절은 열을 생성된 열(generated column)로 생성합니다. 생성된 열은 직접 값을 쓸 수 없으며, 읽을 때는 지정된 표현식의 결과가 반환됩니다.
열이 데이터를 쓰는 시점에 계산되고 디스크에 저장될 것임을 나타내기 위해 키워드 STORED가 필요합니다.
생성 표현식은 테이블 내의 다른 열을 참조할 수 있지만, 다른 생성된 열은 참조할 수 없습니다. 사용된 함수와 연산자는 불변(immutable)이어야 합니다. 다른 테이블에 대한 참조는 허용되지 않습니다.

#### GENERATED { ALWAYS | BY DEFAULT } AS IDENTITY [ ( sequence_options ) ]
이 절은 열을 아이덴티티 열(identity column)로 생성합니다. 해당 열에는 암시적으로 시퀀스(sequence)가 연결되며, 새로 삽입된 행에서는 이 시퀀스의 값을 자동으로 할당받습니다. 이러한 열은 암시적으로 NOT NULL입니다.
ALWAYS와 BY DEFAULT 절은 INSERT 및 UPDATE 명령에서 사용자가 명시적으로 지정한 값이 처리되는 방식을 결정합니다.
INSERT 명령에서 ALWAYS가 선택된 경우, 사용자가 지정한 값은 INSERT 문에 OVERRIDING SYSTEM VALUE를 명시한 경우에만 허용됩니다. 
BY DEFAULT가 선택된 경우, 사용자가 지정한 값이 우선 적용됩니다. 자세한 내용은 INSERT를 참조하십시오. (COPY 명령에서는 이 설정과 관계없이 사용자가 지정한 값이 항상 사용됩니다.)
UPDATE 명령에서는 ALWAYS가 선택된 경우 DEFAULT 이외의 값으로 열을 업데이트하려는 시도가 거부됩니다. BY DEFAULT가 선택된 경우 열을 정상적으로 업데이트할 수 있습니다. (UPDATE 명령에는 OVERRIDING 절이 없습니다.)
선택적인 sequence_options 절은 시퀀스의 매개변수를 재정의하는 데 사용할 수 있습니다. 사용 가능한 옵션에는 CREATE SEQUENCE에 표시된 옵션 외에 시퀀스의 이름과 지속성 수준을 선택할 수 있는 SEQUENCE NAME name, LOGGED, UNLOGGED가 포함됩니다. SEQUENCE NAME이 지정되지 않으면 시스템에서 사용되지 않은 이름을 선택합니다. LOGGED 또는 UNLOGGED가 지정되지 않은 경우, 시퀀스는 테이블과 동일한 지속성 수준을 가집니다.

#### UNIQUE [ NULLS [ NOT ] DISTINCT ] (column constraint)
#### UNIQUE [ NULLS [ NOT ] DISTINCT ] ( column_name [, ... ] ) [ INCLUDE (column_name [, ...]) ] (table constraint)
UNIQUE 제약 조건은 테이블의 하나 이상의 열 그룹이 고유한 값만을 포함해야 한다는 것을 지정합니다. UNIQUE 테이블 제약 조건의 동작은 UNIQUE 열 제약 조건과 동일하지만, 여러 열에 걸쳐 적용할 수 있는 추가 기능이 있습니다. 이 제약 조건은 따라서 두 행이 이러한 열 중 하나 이상에서 반드시 서로 다른 값을 가져야 함을 강제합니다.

UNIQUE 제약 조건에서 NULL 값은 NULLS NOT DISTINCT가 지정되지 않는 한 서로 같지 않다고 간주됩니다.
각 UNIQUE 제약 조건은 해당 테이블에 대해 정의된 다른 UNIQUE 또는 PRIMARY KEY 제약 조건과 열 집합이 달라야 합니다. (그렇지 않으면 중복된 UNIQUE 제약 조건은 제거됩니다.)
다중 계층 파티션 계층 구조에서 UNIQUE 제약 조건을 설정할 때, 대상 파티션 테이블의 파티션 키에 포함된 모든 열뿐만 아니라 그 하위 파티션 테이블의 열도 제약 조건 정의에 포함되어야 합니다.

UNIQUE 제약 조건을 추가하면 자동으로 해당 제약 조건에 사용된 열 또는 열 그룹에 대한 고유 btree 인덱스가 생성됩니다. 생성된 인덱스의 이름은 UNIQUE 제약 조건과 동일합니다.
선택적으로 INCLUDE 절을 사용하여 단순히 “페이로드” 역할을 하는 하나 이상의 열을 해당 인덱스에 추가할 수 있습니다. 이러한 열에는 고유성이 강제되지 않으며, 이러한 열을 기준으로 인덱스를 검색할 수는 없습니다. 그러나 이들은 인덱스 전용 스캔을 통해 검색할 수 있습니다. 포함된 열에 대해 제약 조건이 강제되지는 않지만, 여전히 제약 조건은 해당 열에 종속됩니다. 따라서 해당 열에 대한 일부 작업(예: DROP COLUMN)은 제약 조건 및 인덱스 삭제를 연쇄적으로 발생시킬 수 있습니다.

#### PRIMARY KEY (column constraint)
#### PRIMARY KEY ( column_name [, ... ] ) [ INCLUDE ( column_name [, ...])] (table constraint)
PRIMARY KEY 제약 조건은 테이블의 하나 이상의 열에 대해 고유(중복되지 않는)하고 NULL이 아닌 값만 포함해야 한다는 것을 지정합니다. PRIMARY KEY는 테이블당 하나만 지정할 수 있으며, 열 제약 조건으로든 테이블 제약 조건으로든 지정 가능합니다.

PRIMARY KEY 제약 조건은 동일한 테이블에 정의된 다른 UNIQUE 제약 조건과 열 집합이 달라야 합니다. (그렇지 않으면 UNIQUE 제약 조건은 중복으로 간주되어 제거됩니다.)
PRIMARY KEY는 UNIQUE와 NOT NULL의 조합과 동일한 데이터 제약 조건을 강제합니다. 그러나 PRIMARY KEY로 열 집합을 식별하면 다른 테이블이 해당 열 집합을 행의 고유 식별자로 신뢰할 수 있다는 메타데이터 정보를 제공합니다.

파티션된 테이블에 적용되는 경우, PRIMARY KEY 제약 조건은 UNIQUE 제약 조건에 대해 이전에 설명된 제한 사항을 공유합니다.
PRIMARY KEY 제약 조건을 추가하면 해당 제약 조건에서 사용된 열 또는 열 그룹에 대해 고유 btree 인덱스가 자동으로 생성됩니다. 생성된 인덱스의 이름은 PRIMARY KEY 제약 조건과 동일합니다.

선택적으로 INCLUDE 절을 사용하여 단순히 “페이로드” 역할을 하는 하나 이상의 열을 해당 인덱스에 추가할 수 있습니다. 이러한 열에는 고유성이 강제되지 않으며, 해당 열을 기준으로 인덱스를 검색할 수는 없습니다. 그러나 이들은 인덱스 전용 스캔을 통해 검색할 수 있습니다. 포함된 열에 대해 제약 조건이 강제되지는 않지만, 여전히 제약 조건은 해당 열에 종속됩니다. 따라서 해당 열에 대한 일부 작업(예: DROP COLUMN)은 제약 조건 및 인덱스 삭제를 연쇄적으로 발생시킬 수 있습니다.

> INCLUDE는 쓰기 성능에 부정적이고, 읽기 성능에 긍정적이다.

#### EXCLUDE [ USING index_method ] ( exclude_element WITH operator [, ... ] ) index_parameters [ WHERE ( predicate ) ]
EXCLUDE 절은 제외 제약 조건을 정의하며, 지정된 열 또는 표현식에 대해 지정된 연산자를 사용하여 두 행을 비교할 때, 이 비교 결과가 모두 TRUE가 되지 않도록 보장합니다. 모든 지정된 연산자가 동등성을 검사하는 경우, 이는 UNIQUE 제약 조건과 동일하지만, 일반적인 UNIQUE 제약 조건이 더 빠릅니다. 그러나 제외 제약 조건은 단순한 동등성을 넘어서는 제약 조건을 지정할 수 있습니다. 예를 들어, && 연산자를 사용하여 테이블의 두 행에 겹치는 원이 포함되지 않도록 제약 조건을 지정할 수 있습니다(섹션 8.8 참조). 연산자는 교환 가능(Commutative)해야 합니다.

제외 제약 조건은 제약 조건과 동일한 이름을 가진 인덱스를 사용하여 구현되므로, 각 지정된 연산자는 인덱스 접근 메서드(index_method)에 적합한 연산자 클래스(operator class)와 연결되어야 합니다(섹션 11.10 참조). 각 exclude_element는 인덱스의 열을 정의하므로, 선택적으로 정렬 규칙(collation), 연산자 클래스, 연산자 클래스 매개변수, 및/또는 정렬 옵션을 지정할 수 있습니다. 이러한 내용은 CREATE INDEX에서 완전히 설명됩니다.

접근 메서드는 amgettuple을 지원해야 하며(섹션 62 참조), 현재로서는 GIN을 사용할 수 없습니다. 허용되기는 하지만, B-tree 또는 hash 인덱스를 제외 제약 조건과 함께 사용하는 것은 일반적인 UNIQUE 제약 조건보다 나은 점이 없기 때문에 거의 의미가 없습니다. 따라서 실제로 접근 메서드는 항상 GiST 또는 SP-GiST가 됩니다.

predicate는 테이블의 부분 집합에 제외 제약 조건을 지정할 수 있도록 하며, 내부적으로는 부분 인덱스를 생성합니다. 괄호는 predicate를 지정할 때 반드시 필요합니다.

```sql
-- 같은 room_id에 대해 예약 시간이 겹치는 경우 삽입이 거부됨.
CREATE TABLE reservations (
    id SERIAL PRIMARY KEY,
    room_id INT NOT NULL,
    start_time TIMESTAMP NOT NULL,
    end_time TIMESTAMP NOT NULL,
    EXCLUDE USING gist (room_id WITH =, tstzrange(start_time, end_time) WITH &&) -- tstzrange(start_time, end_time)은 && (겹침) 연산자로 비교.
);
```

#### REFERENCES
REFERENCES reftable [ ( refcolumn ) ] [ MATCH matchtype ] [ ON DELETE referential_action ] [ ON UPDATE referential_action ] (column constraint)
FOREIGN KEY ( column_name [, ... ] ) 

REFERENCES reftable [ ( refcolumn [, ... ] ) ] [ MATCH matchtype ] [ ON DELETE referential_action ] [ ON UPDATE referential_action ] (table constraint)

이 절은 외래 키 제약 조건을 지정합니다. 외래 키 제약 조건은 새 테이블의 하나 이상의 열 그룹이 참조된 테이블의 참조 열에서 일부 행의 값과 일치하는 값만 포함하도록 요구합니다. refcolumn 목록이 생략된 경우, 참조 테이블의 기본 키가 사용됩니다. 그렇지 않은 경우, refcolumn 목록은 비지연성(non-deferrable) 고유 키(UNIQUE) 또는 기본 키 제약 조건의 열을 참조하거나, 비부분적(non-partial) 고유 인덱스의 열이어야 합니다. 사용자는 참조 테이블에 대한 REFERENCES 권한을 가져야 하며(테이블 전체 또는 특정 참조 열), 외래 키 제약 조건을 추가하려면 참조 테이블에 대해 SHARE ROW EXCLUSIVE 잠금을 요구합니다.
외래 키 제약 조건은 임시 테이블과 영구 테이블 간에 정의될 수 없습니다.

참조 열에 삽입된 값은 주어진 매치 타입(match type)을 사용하여 참조 테이블 및 참조 열의 값과 비교됩니다. 매치 타입에는 MATCH FULL, MATCH PARTIAL, MATCH SIMPLE(기본값) 세 가지가 있습니다.

- MATCH FULL은 다중 열 외래 키에서 하나의 열이 NULL일 경우 모든 외래 키 열이 NULL이어야 하며, 모두 NULL인 경우 참조 테이블에서 일치하는 행이 없어도 됩니다.
- MATCH SIMPLE은 외래 키 열 중 하나라도 NULL일 경우, 참조 테이블에서 일치하는 행이 없어도 됩니다.
- MATCH PARTIAL은 아직 구현되지 않았습니다.

(물론, 이러한 상황이 발생하지 않도록 외래 키 열에 NOT NULL 제약 조건을 적용할 수 있습니다.)
또한, 참조 열의 데이터가 변경되면 이 테이블의 열 데이터에 대해 특정 작업이 수행됩니다. ON DELETE 절은 참조 테이블에서 참조된 행이 삭제될 때 수행할 작업을 지정합니다. 마찬가지로, ON UPDATE 절은 참조 테이블에서 참조 열이 새 값으로 업데이트될 때 수행할 작업을 지정합니다. 행이 업데이트되더라도 참조 열이 실제로 변경되지 않으면 아무 작업도 수행되지 않습니다.
NO ACTION 검사 외의 참조 작업은 제약 조건이 지연 가능(deferrable)으로 선언되더라도 지연할 수 없습니다. 각 절에 대해 다음과 같은 작업을 수행할 수 있습니다.

##### NO ACTION
삭제 또는 업데이트가 외래 키 제약 조건 위반을 생성할 경우 오류를 발생시킵니다. 제약 조건이 지연(deferrable)된 경우, 참조하는 행이 여전히 존재한다면 제약 조건 확인 시점에 이 오류가 발생합니다. 이는 기본 동작입니다.

##### RESTRICT
삭제 또는 업데이트가 외래 키 제약 조건 위반을 생성할 경우 오류를 발생시킵니다. 이는 NO ACTION과 동일하지만, 확인은 지연할 수 없습니다.

##### CASCADE
삭제된 행을 참조하는 모든 행을 삭제하거나, 참조 열의 값을 참조된 열의 새 값으로 각각 업데이트합니다.

##### SET NULL [ ( column_name [, ... ] ) ]
참조 열 전체 또는 지정된 참조 열의 일부를 NULL로 설정합니다. 열의 일부만 지정하는 것은 ON DELETE 동작에만 가능합니다.

##### SET DEFAULT [ ( column_name [, ... ] ) ]
참조 열 전체 또는 지정된 참조 열의 일부를 기본값으로 설정합니다. 열의 일부만 지정하는 것은 ON DELETE 동작에만 가능합니다. (기본값이 NULL이 아닌 경우, 기본값과 일치하는 행이 참조된 테이블에 반드시 존재해야 하며, 그렇지 않으면 작업이 실패합니다.)

참조 열이 자주 변경되는 경우, 외래 키 제약 조건과 관련된 참조 작업을 더 효율적으로 수행할 수 있도록 참조 열에 인덱스를 추가하는 것이 좋습니다.

##### DEFERRABLE
##### NOT DEFERRABLE
이 설정은 제약 조건을 지연 가능(deferrable)하게 할 수 있는지 여부를 제어합니다. 지연할 수 없는 제약 조건은 각 명령 직후에 즉시 확인됩니다. 지연 가능한 제약 조건의 확인은 SET CONSTRAINTS 명령을 사용하여 트랜잭션 종료 시점까지 연기할 수 있습니다. 기본값은 NOT DEFERRABLE입니다. 현재 이 절은 UNIQUE, PRIMARY KEY, EXCLUDE, REFERENCES(외래 키) 제약 조건에서만 사용할 수 있습니다. NOT NULL 및 CHECK 제약 조건은 지연할 수 없습니다. 또한, 지연 가능한 제약 조건은 ON CONFLICT DO UPDATE 절을 포함하는 INSERT 문에서 충돌 조정자로 사용할 수 없습니다.

##### INITIALLY IMMEDIATE
##### INITIALLY DEFERRED
제약 조건이 지연 가능하다면, 이 절은 제약 조건을 확인할 기본 시점을 지정합니다. 제약 조건이 INITIALLY IMMEDIATE로 설정된 경우, 각 명령문 후에 확인됩니다. 이는 기본값입니다. 제약 조건이 INITIALLY DEFERRED로 설정된 경우, 트랜잭션 종료 시점에만 확인됩니다. 제약 조건 확인 시점은 SET CONSTRAINTS 명령을 사용하여 변경할 수 있습니다.

##### USING method
이 선택적 절은 새 테이블의 내용을 저장하기 위해 사용할 테이블 접근 방식을 지정합니다. 접근 방식은 TABLE 유형이어야 합니다. 자세한 내용은 61장을 참조하십시오. 이 옵션을 지정하지 않으면 새 테이블에 대해 기본 테이블 접근 방식이 선택됩니다. 기본 테이블 접근 방식에 대한 자세한 내용은 default_table_access_method를 참조하십시오.
파티션을 생성할 때, 테이블 접근 방식은 설정된 경우 해당 파티션 테이블의 접근 방식이 사용됩니다.

##### WITH ( storage_parameter [= value] [, ... ] )
이 절은 테이블 또는 인덱스에 대한 선택적 저장 매개변수를 지정합니다. 자세한 내용은 아래의 저장 매개변수(Storage Parameters)를 참조하십시오. 이전 버전과의 호환성을 위해 테이블의 WITH 절에는 새 테이블의 행에 OID(Object Identifiers)가 포함되지 않도록 OIDS=FALSE를 지정할 수도 있습니다. OIDS=TRUE는 더 이상 지원되지 않습니다.

##### ON COMMIT
트랜잭션 블록 종료 시 임시 테이블의 동작은 ON COMMIT을 사용하여 제어할 수 있습니다. 선택 가능한 세 가지 옵션은 다음과 같습니다.
- PRESERVE ROWS
  트랜잭션 종료 시 특별한 작업이 수행되지 않습니다. 이것이 기본 동작입니다.
- DELETE ROWS
  각 트랜잭션 블록이 종료될 때 임시 테이블의 모든 행이 삭제됩니다. 본질적으로 각 커밋 시 자동으로 TRUNCATE가 수행됩니다. 파티션된 테이블에 사용될 경우, 이는 파티션에는 적용되지 않습니다.
- DROP
  임시 테이블은 현재 트랜잭션 블록이 종료될 때 삭제됩니다. 파티션된 테이블에 사용될 경우, 해당 파티션도 함께 삭제되며, 상속된 자식 테이블이 있는 경우 종속된 자식 테이블도 삭제됩니다.

#### TABLESPACE tablespace_name
tablespace_name은 새 테이블이 생성될 테이블스페이스의 이름을 나타냅니다. 지정되지 않은 경우, 기본 테이블스페이스는 default_tablespace 또는 테이블이 임시 테이블인 경우 temp_tablespaces를 참조합니다. 파티션된 테이블의 경우, 테이블 자체에는 저장소가 필요하지 않으므로 지정된 테이블스페이스는 명시적으로 다른 테이블스페이스가 지정되지 않을 때 새로 생성된 파티션에 사용할 기본 테이블스페이스로 default_tablespace를 재정의합니다

#### USING INDEX TABLESPACE tablespace_name
이 절은 UNIQUE, PRIMARY KEY, 또는 EXCLUDE 제약 조건과 관련된 인덱스가 생성될 테이블스페이스를 선택할 수 있도록 합니다. 지정하지 않을 경우, 기본 테이블스페이스는 default_tablespace를 참조하며, 테이블이 임시 테이블인 경우 temp_tablespaces를 참조합니다.

