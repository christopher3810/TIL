***

## 서브 쿼리 위치에 따른 SQL 용어

>서브쿼리란? : 쿼리 안의 보조쿼리를 가르키는 용어
>작성 위치는 크게 보통 SELECT , FROM , WHERE 절로 나뉩니다.

```mysql
SELECT //메인쿼리 (SELECT .... FROM ...) //SELECT절 안에 SELECT절 스칼라 서브쿼리 
FROM //메인쿼리 (SELECT ... FROM ...) // FROM절 인라인 뷰 
WHERE 컬럼명 IN //메인쿼리 (SELECT ... FROM ...) //WHERE절 중첩 서브쿼리
```

<br>
### 스칼라 서브쿼리

---

-   scalar - 하나의 숫자로만 표시되는양 || 단순히 하나의 숫자를 의미
    
    -   스칼라 서브쿼리 : SELECT 절 내부에 하나의 숫자. 문자, 기호 등을 출력하는 SELECT 문
-   FROM절이나 WHERE절에도 스칼라 서브쿼리를 사용할 수 있으나 대부분 SQL문에서 SELECT 절에 사용하므로 SELECT절 기준으로 설명 합니다
    
-   메인쿼리 SELECT절에는 최종 출력하려는 열들이 나열되므로 만약 출력데이터가 1건이면 스칼라 서브쿼리의 결과 건수도 1개가 나와야 합니다. 즉 1행 1열 구조로 출력해야 합니다.
    
    ```sql
    SELECT 이름,
    				(SELECT COUNT(*)
    				 FROM 학생 AS 학생2
    				 WHERE 학생2.이름 = 학생1.이름) 카운트
    	FROM 학생 AS 학생1;
    ```
    
    -   보통 스칼라 서브쿼리는 출력되는 데이터 건수가 1건이어야 하므로 집계함수(max, min, avg, sum, count) 가 자주 쓰입니다


### 인라인 뷰

---

-   FROM 절에서 임시적으로 뷰를 생성하는 방식이므로 인라인 뷰라고 불립니다.
    
    -   인라인 뷰의 결과는 내부적으로 메모리 또는 디스크에 임시 테이블을 생성하여 활용합니다
    
    ```sql
    SELECT 학생2.학번, 학생2.이름
    	FROM (SELECT *
    					FROM 학생
    					WHERE 성별 = '남') 학생2;
    ```


### 중첩 서브 쿼리

---

-   WHERE 절에서 단순한 값을 비교 연산하는 대신, 서브쿼리를 추가하여 비교 연산하기 위해 주업 서브쿼리를 사용합니다.
    
    -   이처럼 WHERE절에서 중첩 서브쿼리와 비교시 보통 비교연산자( > , < , ≥ , ≤ ....)를 비롯해 IN , EXISTS , NOT , IN , NOT EXISTS 문을 자주 사용합니다.
    
    ```sql
    SELECT *
    	FROM 학생
    	WHERE 학번 = (SELECT MAX(학번) FROM 학생)
    ```