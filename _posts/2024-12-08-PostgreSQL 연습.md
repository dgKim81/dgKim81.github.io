---
title: PostgreSQL 기본 - 간단한 쿼리 연습
description: >-
  테이블 생성과 데이터 입력, 상속에 대해서
author: dgkim
date: 2024-12-01 20:02:00 +0900
categories: [데이터베이스]
tags: [PostgreSQL, 상속, 데이터 테스트]
pin: false
media_subpath: '/posts/20241201'
comments: true
---
# 간단한 테이블과 쿼리 작성해보기

## 테이블 생성
일단.. 의미가 맞느냐 아니냐는 지금은 상관하지 말자. 
ANIMAL과 그 테이블을 상속 받는 테이블 생성.
```sql
CREATE TABLE ANIMAL (
	ANIMAL_ID		SERIAL			PRIMARY KEY 
,	ANIMAL_NAME		VARCHAR(256)
,	EXTENAL_ID		UUID			DEFAULT(gen_random_uuid())
);
-- ANIMAL을 상속 받는다.
CREATE TABLE CAT (
	COLOR			INTEGER[3]
) INHERITS (ANIMAL);

-- ANIMAL을 상속 받는다.
CREATE TABLE DOG (
	COLOR			INTEGER[3]
) INHERITS (ANIMAL);
```
우리와 우리에 있는 동물에 대해 테이블 생성.
```sql
CREATE TABLE CAGE (
	CAGE_ID			VARCHAR(16) 	PRIMARY KEY
,	CAGE_NAME		VARCHAR(256)
,	CREATE_DATETIME	TIMESTAMP		DEFAULT(NOW())
);
-- ANIMAL과 CAGE에 대해 외래키를 설정한다.
CREATE TABLE CAGE_ANIMAL (
	CAGE_ID			VARCHAR(16)
,	ANIMAL_ID		INTEGER
,	CNT				INTEGER			DEFAULT(1)

,	CONSTRAINT PK_CAGE_ANIMAL PRIMARY KEY (CAGE_ID, ANIMAL_ID)
,	CONSTRAINT FK_CAGE_CAGE_ANIMAL FOREIGN KEY (CAGE_ID)  REFERENCES CAGE(CAGE_ID)
		ON DELETE CASCADE
		ON UPDATE CASCADE
		
,	CONSTRAINT FK_ANIMAL_CAGE_ANIMAL FOREIGN KEY (ANIMAL_ID)  REFERENCES ANIMAL(ANIMAL_ID)
		ON DELETE CASCADE
		ON UPDATE CASCADE
);
```

## 데이터 테스트

```sql
-- 구체 테이블에 데이터를 입력한다.
INSERT INTO CAT (ANIMAL_ID, ANIMAL_NAME, EXTENAL_ID, COLOR) VALUES (DEFAULT, '고양이', DEFAULT, ARRAY[255,126,126]);
INSERT INTO DOG (ANIMAL_ID, ANIMAL_NAME, EXTENAL_ID, COLOR) VALUES (DEFAULT, '개', DEFAULT, ARRAY[255,126,126]);
```

입력 된 데이터 조회

```sql
SELECT * FROM CAT;
```
| animal_id                    | animal_name      | extenal_id                           | color          |
| :--------------------------- | :--------------- | -----------------------------------: | :------------- |
| 2                            | CAT              | 9a3d3480-46ff-49cd-a190-5355d4b664f8 | {255,126,126}  |

```sql
SELECT * FROM DOG;
```
| animal_id                    | animal_name      | extenal_id                           | color          |
| :--------------------------- | :--------------- | -----------------------------------: | :------------- |
| 3                            | 개               | b3e236ad-e53d-4932-93d0-55114c671a37 | {255,126,126}  |

```sql
SELECT * FROM ANIMAL;
```
| animal_id                    | animal_name      | extenal_id                           | color          |
| :--------------------------- | :--------------- | -----------------------------------: | :------------- |
| 2                            | CAT              | 9a3d3480-46ff-49cd-a190-5355d4b664f8 | {255,126,126}  |
| 3                            | 개               | b3e236ad-e53d-4932-93d0-55114c671a37 | {255,126,126}  |

오! 보시다시피 상속을 이용해서 만든 테이블들의 데이터 구조가 별다른 조치 없이도 알맞게 나왔다. 

그럼 테이블 삭제는 어떤가?
```sql
delete from ANIMAL;
```

삭제 했을 때 관련 테이블들에서 행이 조회 되지 않았다. 오! 이것도 되네?

CAGE에 데이터를 입력한다.
```sql
INSERT INTO CAGE (CAGE_ID, CAGE_NAME, CREATE_DATETIME) VALUES ('CAT-1', '고양이 우리 1', DEFAULT);
INSERT INTO CAGE (CAGE_ID, CAGE_NAME, CREATE_DATETIME) VALUES ('DOG-1', '개 우리 1', DEFAULT);
```
그리고 CASE_ANIMAL에 데이터를 입력한다.
```sql
INSERT INTO CAGE_ANIMAL (CAGE_ID, ANIMAL_ID, CNT) VALUES ('CAT-1',2,1);
INSERT INTO CAGE_ANIMAL (CAGE_ID, ANIMAL_ID, CNT) VALUES ('DOG-1',3,1);
```
그런데 오류가 났다.... 
```cmd
ERROR:  (animal_id)=(2) 키가 "animal" 테이블에 없습니다."cage_animal" 테이블에서 자료 추가, 갱신 작업이 "fk_animal_cage_animal" 참조키(foreign key) 제약 조건을 위배했습니다 

오류:  "cage_animal" 테이블에서 자료 추가, 갱신 작업이 "fk_animal_cage_animal" 참조키(foreign key) 제약 조건을 위배했습니다
SQL state: 23503
Detail: (animal_id)=(2) 키가 "animal" 테이블에 없습니다.
```
animal에 데이터가 존재하던데 왜 입력되지 않을까? 혹시 foreign 에 뭘 잘못 썼나 살펴보다가. 매뉴얼에서 어떤 글귀를 봤던 기억이 났다.
그래서 다음과 같이 쿼리를 실행했다.
```sql
SELECT * FROM ONLY ANIMAL;
```
결과는 데이터가 비어 있었다....

보통 부모 자식이라고 설정하면 부모 테이블을 참조하는 테이블들이 있을 수도 있는데, 부모 테이블 자체에는 데이터가 없다니...
부모에도 데이터 입력하고 자식에도 데이터를 입력해야 하는 상황이 벌어졌다.
```sql
INSERT INTO ANIMAL (ANIMAL_ID, ANIMAL_NAME, EXTENAL_ID) 
	SELECT 
		A.*
	FROM 
		ANIMAL A
		LEFT JOIN
		ONLY ANIMAL B
		ON	A.ANIMAL_ID = B.ANIMAL_ID
	WHERE
		B.ANIMAL_ID IS NULL;
```
일단 insert 하고 다시 생각해보기로 했다.

## 특이한 update 쿼리 실행
update문에서는 from 절은 없는 경우가 많다. 그러나 정말 특이한 요구 사항이 생기면 이런 쿼리가 필요할 때가 있다.
```sql
UPDATE CAGE_ANIMAL AS C
SET	CNT = 7
FROM 
	CAGE A, ANIMAL B
WHERE
	A.CAGE_ID = C.CAGE_ID
AND B.ANIMAL_ID = C.ANIMAL_ID
AND A.CAGE_ID	= 'CAT-1'
```
확인 해보니 join 문은 없었고 위와 같이 릴레이션을 나열하고 아래 where 절에서 조건을 입력하는 방법이었다.
별로 좋아하는 방식은 아니지만, join문은 없었다.

# 결론
일단 여기까지 두고, 부모 데이블에 데이터를 직접 넣어줘야하고 부모 테이블에 데이터가 들어가면 자식 요소의
데이터와 부모 요소의 데이터가 중복된 것 처럼 나타난다. 어.. 이러면 굳이 inherit을 쓰는 이유가 있나....

[**stack OverFlow**](https://stackoverflow.com/questions/7042860/sql-table-inheritance-causing-duplicate-records-in-base-table-even-though-primar);
내용을 일부 보았을 때 상속에 대한 데이터 입력 부분은 버그인 것 같다.

약간 테스트 해보았지만, 현재로서는 일반 RDBMS와 비슷하게 쓴다면, SQL에 익숙한 누구라도 별문제 없이 
사용이 가능해 보였다. 
