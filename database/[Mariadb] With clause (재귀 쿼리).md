![image](https://user-images.githubusercontent.com/70880695/225812959-efda5dd3-2e03-42b1-8e11-a88c0e31a56a.png)

# With clause 재귀쿼리

## 1. mariadb 재귀쿼리 구조
- with절은 메모리에 가상의 테이블로 저장한다.
- CTE : 공통 테이블 표현식; 쿼리 실행 동안만 임시 테이블이 메모리에 생성되어 재귀 쿼리로 표현이 가능하다.
- CTE에는 Non-Recursive와 Recursive 2가지로 나뉜다.

```mariadb
WITH [RECURSIVE] table_reference [(columns_list)] AS  (
SELECT ...
)
[CYCLE cycle_column_list RESTRICT]
SELECT ...
```

<br />

### 1-1. WITH RECURSIVE 지켜야할 점
1. 반드시 UNION 포함 
2. Non-Recursive clause 최소 1개 필요하며 처음 1번만 실행 
3. with clause 밖에서는 반드시 만들어진 가상테이블을 조회하는 문장 필요 
4. 재귀식은 반드시 base condition(종료조건) 필요

```mariadb
   WITH RECURSIVE cte_count
   AS (
   -- [1] 재귀 초깃값 : Non-Recursive 문장 (첫 번째 루프에서만 실행)
   SELECT 1 AS n
   UNION ALL
   -- [2] Recursive 문장 : 읽어올 때마다 행의 위치가 기억되어 다음 번 읽어올 때 다음 행으로 이동)
   SELECT n + 1
   FROM cte_count
   -- [3] base condition : WHERE절 조건 까지만 실행함
   WHERE n < 5
   )

SELECT * FROM cte_count;
```

<br />

## 2. [CYCLE ... RESTRICT]
- CYCLE RESTRICT는 해당 컬럼에 대해서 순환을 탐지하는 경우 오류를 발생시키고 종료한다는 의미
- 10.5.2부터 지원하는 비표준 문법이다.
```mariadb
WITH RECURSIVE cte(id, parent_id) AS (
    SELECT id, parent_id
    FROM my_table
    WHERE id = 1
    UNION ALL
    SELECT t.id, t.parent_id
    FROM my_table t
    JOIN cte ON t.parent_id = cte.id
)
SELECT id, parent_id
FROM cte
CYCLE id RESTRICT;

```

### 2-1. CYCLE 탐지 ?
1. 위 쿼리는 id를 기준으로 CYCLE 체크가 진행될 것이다.
2. 만약 (id = 1, parent_id = 2)를 가진 row가 있고, 또한 (id = 2, parent_id = 1)이 있을 경우
3. CYCLE id RESTRICT와 함께 실행 시 CYCLE을 감지하고 에러메시지를 return하게 된다.


<br />

## 3. with recursive 예제 (부서 계층형 쿼리)
```mariadb
WITH RECURSIVE cte_dept AS (
    SELECT
        dept_id,
        dept_name,
        parent_dept_id,
        dept_name as dept_path_str,   -- dept_nme을 문자열로 이어서 보여주기 위해 사용
        1 as level, 
        (CASE WHEN 
            (SELECT true FROM dept d WHERE d.parent_dept_id = d.dept_id LIMIT 1) is true
            THEN false ELSE true END) as is_leafnode
    FROM dept
    WHERE dept_id is null
    UNION ALL
    SELECT
        dept_id,
        dept_name,
        parent_dept_id,   
        CONCAT(IFNULL(dept_path_str, ''), '@', IFNULL(dept_name, '')) as dept_path_str,  -- dept_nme을 문자열로 이어서 보여주기 위해 사용
        (level + 1) as level,
        (CASE WHEN
              (SELECT true FROM dept d WHERE d.parent_dept_id = d.dept_id LIMIT 1) is true
              THEN false ELSE true END) as is_leafnode
        FROM cte_dept
        JOIN dept
            ON dept.dept_id = cte_dept.dept_id)
select
    dept_id,
    dept_name,
    parent_dept_id,
    dept_path_str,
    level,
    is_leafnode
FROM cte_dept
ORDER BY dept_path_str;
```



<br />

### WITH RECURSIVE 활용 추천문제
- https://school.programmers.co.kr/learn/courses/30/lessons/59413

<br />

### 출처
[1] [inpa dev : [MYSQL] 📚 RECURSIVE (재귀 쿼리)](https://inpa.tistory.com/entry/MYSQL-%F0%9F%93%9A-RECURSIVE-%EC%9E%AC%EA%B7%80-%EC%BF%BC%EB%A6%AC)
