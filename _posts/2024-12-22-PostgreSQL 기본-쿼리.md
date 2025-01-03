---
title: PostgreSQL 쿼리 정리
description: >-
    PostgreSQL 쿼리 기능 정리
author: dgkim
date: 2024-12-22 21:02:00 +0900
categories: [데이터베이스]
tags: [PostgreSQL]
pin: false
media_subpath: '/posts/20241222'
comments: true
---

# PostgreSQL 쿼리 관련
> ANSI SQL 표준를 따른다.
> 인스턴스 내의 다른 데이터베이스를 참조 할 수 없다. 참조하려면 별도 EXTENSION을 설치해야 한다.

---
## 1. 기본 SELECT
```sql
SELECT A, B + C FROM table1;
SELECT 3 * 5;
SELECT RANDOM();

INSERT INTO table1 (A, B, C) VALUES (1, 2, 3);
UPDATE table1 SET A = 4 WHERE B = 2;
DELETE FROM table1 WHERE A = 4;
```



## 2. INSERT, UPDATE, DELETE
### RETURNING 사용
- 데이터 수정 작업(INSERT, UPDATE, DELETE) 후 수정된 데이터를 반환.
- 반환된 데이터를 다른 작업에서 활용 가능.

```sql
UPDATE products
SET price = price * 1.10
RETURNING id, name, price;
```

---

## 3. WITH 문
### 기본 사용
- 쿼리를 간소화하거나 중복 계산을 방지하는 데 사용.
- JSON 데이터를 `WITH`와 결합하여 복잡한 데이터 처리 가능.

```sql
WITH w AS (
    SELECT * FROM big_table
)
SELECT * FROM w WHERE key = 123;
```

### 재귀 사용
- `WITH RECURSIVE`를 사용해 재귀 쿼리를 작성.

```sql
WITH RECURSIVE search_tree(id, parent_id) AS (
    SELECT id, parent_id
    FROM tree
    WHERE parent_id IS NULL
    UNION ALL
    SELECT t.id, t.parent_id
    FROM tree t
    JOIN search_tree st ON t.parent_id = st.id
)
SELECT * FROM search_tree;
```

### MATERIALIZED와 NOT MATERIALIZED
- **MATERIALIZED**: 기본 동작. 결과를 물리적 테이블로 저장하여 중복 계산 방지.
- **NOT MATERIALIZED**: 부모 쿼리와 병합하여 최적화 가능.

```sql
WITH w AS MATERIALIZED (
    SELECT * FROM big_table
)
SELECT * FROM w;

WITH w AS NOT MATERIALIZED (
    SELECT * FROM big_table
)
SELECT * FROM w WHERE key = 123;
```

### 데이터 수정과 결합
- 데이터 수정 문(INSERT, UPDATE, DELETE)과 함께 사용.

```sql
WITH moved_rows AS (
    DELETE FROM products
    WHERE date >= '2022-01-01'
    RETURNING *
)
INSERT INTO products_log
SELECT * FROM moved_rows;
```

---

## 4. 윈도우 함수
### DISTINCT와 사용 가능
### FILTER 조건 활용
- `FILTER (WHERE ...)`를 통해 특정 조건만 집계.

```sql
SELECT SUM(price) FILTER (WHERE category = 'A') AS category_a_total
FROM products;
```

---

## 4. 쿼리와 JOIN
### JOIN 종류
```sql
T1 CROSS JOIN T2;
T1 [INNER | LEFT | RIGHT | FULL OUTER] JOIN T2 ON condition;
T1 NATURAL [INNER | LEFT | RIGHT | FULL OUTER] JOIN T2;

INNER JOIN ON condition
LEFT OUTER JOIN ON condition
RIGHT OUTER JOIN ON condition
FULL OUTER JOIN ON condition
NATURAL JOIN
T1 INNER JOIN T2 USING (column)
```

---

## 5. GROUP BY와 집계

### GROUPING SETS
```sql
SELECT category, region, SUM(sales) AS total_sales
FROM sales
GROUP BY GROUPING SETS ((category, region), (category), (region), ());
```

### CUBE
- 모든 조합에 대해 집계.

```sql
GROUP BY CUBE(category, region);
```

### ROLLUP
- 계층적으로 집계.

```sql
GROUP BY ROLLUP(category, region);
```

---

## 6. DISTINCT ON
- 중복 제거 기준을 지정.

```sql
SELECT DISTINCT ON (category) id, category, region
FROM sales
ORDER BY category, sales DESC;
```

---

## 7. 집합 연산

| 연산자         | 설명                           |
|----------------|-------------------------------|
| `UNION [ALL]`  | 두 쿼리 결과 병합.              |
| `INTERSECT [ALL]`| 두 쿼리의 공통된 행 반환.     |
| `EXCEPT [ALL]` | 첫 번째 쿼리 결과에서 두 번째 쿼리 제외.|

---

## 8. 정렬 및 페이징

### ORDER BY
- ASC/DESC, 다중 열 정렬 지원.
-  NULLS FIRST/LAST: NULL 값을 가장 먼저/마지막에 정렬.

```sql
SELECT * FROM products ORDER BY price DESC, name ASC;
```

### LIMIT와 OFFSET
- 페이징 처리.

```sql
SELECT * FROM products ORDER BY price DESC LIMIT 10 OFFSET 20;
```
> ***성능 저하를 방지하기 위해 커서 또는 Keyset Pagination 권장.***
---

## 9. VALUES
- 임시 데이터 생성.

```sql
SELECT * 
FROM (VALUES ('Alice', 'HR'), ('Bob', 'Finance')) 
AS employees(name, department);
```

---

## 10. 트리 및 그래프 처리

### SEARCH
- 깊이 또는 너비 우선 탐색.

```sql
WITH RECURSIVE search_tree(id, parent_id) AS (
    SELECT id, parent_id FROM tree WHERE parent_id IS NULL
    UNION ALL
    SELECT t.id, t.parent_id FROM tree t, search_tree st WHERE t.parent_id = st.id
) SEARCH DEPTH FIRST BY id SET order_column;
```

### CYCLE
- 순환 방지.

```sql
WITH RECURSIVE search_graph(id, link, path) AS (
    SELECT id, link, ARRAY[id] AS path FROM graph
    UNION ALL
    SELECT g.id, g.link, sg.path || g.id
    FROM graph g, search_graph sg
    WHERE g.id = sg.link AND NOT g.id = ANY(sg.path)
) CYCLE id SET is_cycle USING path;
```

---

## 11. ROWS FROM
- 여러 테이블 함수나 함수 호출 결과를 결합하여 사용.

```sql
SELECT *
FROM ROWS FROM (
    json_to_recordset('[{"a":40,"b":"foo"},
                      {"a":"100","b":"bar"}]')
    AS (a INTEGER, b TEXT),
    generate_series(1, 3)
) AS x (p, q, s)
ORDER BY p;
```
---

## 12. UNNEST와 WITH ORDINALITY

### UNNEST
- 배열을 행으로 변환하여 테이블 형식으로 반환.

```sql
SELECT *
FROM UNNEST(ARRAY[1, 2, 3, 4], ARRAY['a', 'b', 'c', 'd'])
     AS t(col1, col2);
```
- **결과:** 배열의 각 요소가 행으로 확장되어 반환.

### WITH ORDINALITY
- 테이블 함수와 함께 사용하여 결과에 행 번호를 추가.

```sql
SELECT *
FROM UNNEST(ARRAY['foo', 'bar', 'baz'])
     WITH ORDINALITY AS t(value, row_number);
```
- **결과:** 각 배열 요소에 대해 고유한 행 번호(`row_number`)를 반환.

---

## 13. LATERAL

### 설명
- `LATERAL`은 FROM 절 내에서 이전 항목의 결과를 참조하여 동적으로 데이터를 처리할 수 있도록 하는 키워드입니다.
- 주로 테이블 함수나 서브쿼리에서 사용하며, 동적 매핑 및 데이터 확장 작업에 적합합니다.

### 예제: 동적 참조
```sql
SELECT e.id, e.name, d.total_sales
FROM employees e
CROSS JOIN LATERAL (
    SELECT SUM(s.sales) AS total_sales
    FROM sales s
    WHERE s.employee_id = e.id
) d;
```
- **설명:** 
  - `employees` 테이블의 각 행에 대해 `sales` 테이블에서 관련 데이터를 동적으로 집계.

### 예제: 서브쿼리와 결합
```sql
SELECT *
FROM orders o
JOIN LATERAL (
    SELECT *
    FROM order_items i
    WHERE i.order_id = o.id
) item_details ON true;
```
- **설명:**
  - `orders` 테이블의 각 행에 대해 `order_items` 데이터를 동적으로 조회.

### 테이블 함수에서의 LATERAL 생략
- 테이블 함수에서는 `LATERAL` 키워드가 암묵적으로 적용됩니다.
```sql
SELECT *
FROM employees,
     UNNEST(ARRAY['Alice', 'Bob', 'Charlie']) AS names(name);
```

---

## 14. 샘플 데이터베이스 설치

### GitHub 리소스
- [Pagila Sample Database](https://github.com/morenoh149/postgresDBSamples)

### 설치 및 로드
#### SQL 파일 실행하여 데이터 로드
- **예제:**
```bash
./createdb pagila -U postgres
./psql -U postgres -d pagila -a -f /e/postgreSQL/sample/pagila-0.10.1/pagila-schema.sql
./psql -U postgres -d pagila -a -f /e/postgreSQL/sample/pagila-0.10.1/pagila-data.sql
./psql -U postgres -d pagila -a -f /e/postgreSQL/sample/pagila-0.10.1/pagila-insert-data.sql
```