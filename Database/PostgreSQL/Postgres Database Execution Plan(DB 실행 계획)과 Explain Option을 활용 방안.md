***

데이터베이스 실행 계획(Database Execution Plan) 또는 쿼리 실행 계획(Query Execution Plan)은 가능한 가장 효율적인 방법으로 데이터를 검색하거나 조작하기 위해 database management system's (DBMS) query optimizer에서 생성하는 일련의 단계임.

Database 실행계획을 Postgresql의 관점으로 확인. \
PostgreSQL의 맥락에서 실행 계획은 쿼리 처리 파이프라인의 중요한 구성 요소로, 시스템이 쿼리를 빠르고 효율적으로 수행할 수 있도록 도와줌.

PostgreSQL 쿼리 실행 프로세스는 여러 단계로 구성됨.

`Parsing` \
클라이언트가 SQL 쿼리를 전송하면 PostgreSQL Parser가 이를 분석하여  parse tree를 생성함.
parse tree는 테이블, 열, 연산자, 절을 포함한 쿼리 구성 요소의 계층 구조를 나타냄.

`Rewriting` \
PostgreSQL 재작성기는 구문 분석 트리를 처리하고 테이블에 대한 보기 또는 규칙과 같은 적용 가능한 규칙을 적용하여 쿼리를 수정함.

`Planning` \
쿼리 최적화 도구라고도 하는 PostgreSQL 플래너는 구문 분석 트리의 정보와 데이터베이스의 테이블 및 인덱스에 대한 통계를 기반으로 다양한 실행 전략을 평가함. \
목표는 쿼리를 실행하는 가장 효율적인 방법을 찾는 것.

`Execution` \
PostgreSQL 실행기는 선택한 계획을 실행하여 필요에 따라 데이터를 가져오고 조작함.

PostgreSQL의 쿼리 실행 계획은 plan node라는 노드 트리로 표시됨. \
각 노드는 쿼리 실행 중에 사용되는 특정 연산 또는 알고리즘을 나타냄.

### plan node

가장 일반적인 plan node는 다음과 같음.

`sequential scan` \
테이블에서 모든 행을 읽고 지정된 조건에 따라 필터링하는 순차적 스캔.

`Index Scan` \
인덱스를 사용하여 쿼리 조건을 충족하는 행을 빠르게 찾는 인덱스 스캔.

`Bitmap Index Scan` \
비트맵을 사용하여 인덱스에서 쿼리 조건을 충족하는 행을 표시한 다음 비트맵 힙 스캔을 통해 실제 행을 검색하는 스캔.

`Nested Loop` \
두 개의 입력 테이블을 반복하여 첫 번째 테이블의 각 행을 두 번째 테이블의 각 행과 비교하여 일치하는 행을 찾는 조인 알고리즘.

`Merge Join` \
지정된 조인 조건에 따라 정렬된 두 입력 테이블을 병합하는 조인 알고리즘.

`Hash Join` \
한 입력 테이블에서 해시 테이블을 만들고 다른 입력 테이블의 행을 조사하여 일치하는 행을 찾는 조인 알고리즘.

`Aggregate` \
합계, 평균 또는 카운트와 같은 집계 함수를 계산하는 연산.

`Sort` \
지정된 정렬 키를 기준으로 입력 데이터를 정렬하는 작업.
PostgreSQL에서 실행 계획을 분석할 때 다음과 같은 중요한 정보도 표시될 수 있음:

`Startup Cost(시작 비용)` \
plan node의 실행을 시작하는 데 드는 예상 비용임.

`Total Cost(총 비용)` \
모든 하위 노드를 포함하여plan node를 실행하는 데 드는 예상 총 비용임.

`Rows` \
plan node에서 반환할 것으로 예상되는 행 의수.

`Width` \
plan node에서 반환되는 Rows의 예상 평균 너비(바이트).

### Explain

PostgreSQL에서 실행 계획을 보려면 쿼리 전에 EXPLAIN 명령을 사용할 수 있음
그러면 쿼리를 실제로 실행하지 않고 예상 계획이 반환됨.

```sql
SELECT a.id, a.name, b.total_sales
FROM customers a
JOIN sales b ON a.id = b.customer_id
WHERE a.active = true
ORDER BY b.total_sales DESC
LIMIT 10;

```

이 쿼리는 총 매출이 가장 높은 상위 10명의 활성 고객을 검색함.
쿼리를 실제로 실행하지 않고 예상 실행 계획을 보려면 쿼리 앞에 EXPLAIN 명령을 사용하면 됨.

```sql
EXPLAIN
SELECT a.id, a.name, b.total_sales
FROM customers a
JOIN sales b ON a.id = b.customer_id
WHERE a.active = true
ORDER BY b.total_sales DESC
LIMIT 10;

```

출력 결과

```bash
postgres=# EXPLAIN
SELECT a.id, a.name, b.total_sales
FROM customers a
JOIN sales b ON a.id = b.customer_id
WHERE a.active = true
ORDER BY b.total_sales DESC
LIMIT 10;
                                      QUERY PLAN                                       
---------------------------------------------------------------------------------------
 Limit  (cost=50.46..50.48 rows=10 width=552)
   ->  Sort  (cost=50.46..51.96 rows=600 width=552)
         Sort Key: b.total_sales DESC
         ->  Hash Join  (cost=12.28..37.49 rows=600 width=552)
               Hash Cond: (b.customer_id = a.id)
               ->  Seq Scan on sales b  (cost=0.00..22.00 rows=1200 width=36)
               ->  Hash  (cost=11.40..11.40 rows=70 width=520)
                     ->  Seq Scan on customers a  (cost=0.00..11.40 rows=70 width=520)
                           Filter: active
(9 rows)
```

쿼리를 실행하고 실제 실행 통계를 제공하는 EXPLAIN ANALYZE를 사용할 수도 있음.
```sql
EXPLAIN ANALYZE
SELECT a.id, a.name, b.total_sales
FROM customers a
JOIN sales b ON a.id = b.customer_id
WHERE a.active = true
ORDER BY b.total_sales DESC
LIMIT 10;

```


### Explain 명령어 & Rollback

또한 `begin`과 `rollback` 명령을 사용하여 특정 쿼리의 테스트를 진행하고 롤백을 할 수 있음. 

이를 사용하면 쿼리의 변경사항을 데이터베이스에 반영하지 않고 쿼리의 실행 계획을 확인할 수 있음.

```sql
BEGIN;

EXPLAIN (ANALYZE, BUFFERS, TIMING)
UPDATE products SET price = price * 1.10 WHERE category = 'Electronics';

EXPLAIN (ANALYZE, BUFFERS, TIMING)
UPDATE orders SET total = total * 1.05 WHERE order_date < '2022-01-01';

ROLLBACK;

```


### buffers, timing option

`analyze`, `buffers`, `timing` 옵션은 각각 실행 계획과 실제 실행 통계, 각 계획 노드에서 사용되는 버퍼 통계, 각 단계에서 소요되는 시간을 출력

- 출력 예제

```bash
UPDATE on products  (cost=0.00..43100.00 rows=10000 width=6) (actual time=101.123..210.930 rows=8000 loops=1)
  Buffers: shared hit=32000 read=10000
  ->  Seq Scan on products  (cost=0.00..43100.00 rows=10000 width=6) (actual time=0.013..50.013 rows=8000 loops=1)
        Filter: (category = 'Electronics')
        Rows Removed by Filter: 2000
        Buffers: shared hit=32000 read=10000
Planning Time: 0.081 ms
Execution Time: 215.264 ms
Trigger for constraint constraint_Id: time=0.100 calls=1


UPDATE on orders  (cost=0.00..62100.00 rows=12000 width=6) (actual time=110.512..310.721 rows=10000 loops=1)
  Buffers: shared hit=44000 read=15000
  ->  Seq Scan on orders  (cost=0.00..62100.00 rows=12000 width=6) (actual time=0.012..60.012 rows=10000 loops=1)
        Filter: (order_date < '2022-01-01'::date)
        Rows Removed by Filter: 2000
        Buffers: shared hit=44000 read=15000
Planning Time: 0.088 ms
Execution Time: 320.450 ms
Trigger for constraint constraint_Id: time=0.110 calls=1
```

**Planning Time (0.081 ms)**

PostgreSQL 최적화기가 쿼리 실행 계획을 만드는데 걸린 시간.
    
**Filter**

`category = 'Electronics'` 필터를 적용하여 일부 행을 제거. 

`Rows Removed by Filter: 2000`은 이 필터에 의해 제거된 행의 수.
    
**Buffers: shared hit=32000 read=10000**

쿼리 실행 동안 PostgreSQL이 공유 버퍼에서 32000 페이지를 읽었으며, 디스크에서 10000 페이지를 읽었다는 것을 나타냄.
    
**Execution Time (215.264 ms)**: 쿼리 실행에 소요된 전체 시간.
    
**Trigger for constraint constraint_Id: time=0.100 calls=1**

제약 조건 `constraint_Id`에 대한 트리거가 실행. 

`time` 은 걸린시간 `calls` 는 호출 횟수.

특정 제약 조건에 `time`과 `call` 이 비정상적으로 높거나 많은 경우 `indexing 작업`을 고려해봐야 함



### 참조
[PostgreSQL: Documentation: 15: EXPLAIN](https://www.postgresql.org/docs/current/sql-explain.html) \
[Explore the secrets of SQL Server execution plans (sqlshack.com)](https://www.sqlshack.com/explore-the-secrets-of-sql-server-execution-plans/)

