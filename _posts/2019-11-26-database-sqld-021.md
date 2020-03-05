---
layout: post
title: "[SQLD] 021# SQL 활용 ⑷ 서브쿼리"
tags: [database, SQLD, SQL, SUBQUERY]
categories: sqld
---



### Ⅰ. 서브쿼리 (Subquery)  

서브쿼리란, 하나의 SQL 문 안에 포함되어 있는 또다른 SQL 문을 말한다. 서브쿼리는 알려지지 않은 기준을 이용한 검색을 위해 사용한다. 서브쿼리는 [그림 II-2-12] 와 같이 메인쿼리가 서브쿼리를 포함하는 종속적인 관계이다.  

![sqld-2-2-12](https://drive.google.com/uc?id=1Mg1_Nd_-GngL8e4CMiZdQL_Zrpzp4n33)  


#### ◇ 서브쿼리의 특징  

서브쿼리는 메인쿼리의 컬럼을 모두 사용할 수 있지만 메인쿼리는 서브쿼리의 컬럼을 사용할 수 없다. 질의 결과에 서브쿼리 컬럼을 표시해야 한다면 조인 방식으로 변환하거나 함수, 스칼라 서브쿼리 등을 사용해야 한다.  

  -- 조인은 조인에 참여하는 모든 테이블이 대등한 관계에 있기 때문에 모든 테이블의 컬럼을 어느 위치에서도 자유롭게 사용할 수 있다.  
  -- 조인은 집합간의 곱 (Product) 관계이다. 즉, 1:1 관계의 테이블이 조인하면 1 (=1\*1) 레벨의 집합이 생성되고, 1:M 관계의 테이블을 조인하면 M (=1\*M) 레벨의 집합이 생성된다.  

SQL 문에서 서브쿼리 방식을 사용해야 할 때 잘못 판단하여 조인 방식을 사용하면 결과 집합의 레벨이 달라질 수 있다. 예를 들어, 조인 결과는 조직 (1) 레벨이어야 하는데 사원 (M) 테이블에서 체크할 조건이 존재한다면 결과 집합은 사원 레벨 (M) 이 될 것이다. 이 경우에는 SQL 문에 DISTINCT 를 추가해서 결과를 다시 조직 (1) 레벨로 만들어야 하는데, 이 때 메인쿼리로 조직 (1) 을 사용하고, 서브쿼리로 사원 (M) 테이블을 사용하면 결과 집합을 조직 (1) 레벨로 만들 수 있다.  


#### ◇ 서브쿼리 사용 시 주의사항  

1) 서브쿼리를 괄호로 감싸서 사용한다.  

2) 서브쿼리는 단일 행 (Single Row) 또는 복수 행 (Multiple Row) 비교 연산자와 함께 사용 가능하다.  
  -- 단일 행 비교 연산자는 서브쿼리 결과가 반드시 1건 이하이어야 한다.  

3) 서브쿼리에서는 ORDER BY 를 사용할 수 없다.  


#### ◇ 서브쿼리를 사용할 수 있는 위치  

- SELECT  
- FROM  
- WHERE  
- HAVING  
- ORDER BY  
- INSERT 문의 VALUES  
- UPDATE 문의 SET  


#### ◇ 서브쿼리 분류  

![sqld-ii-2-4](https://drive.google.com/uc?id=1oWGY7z5wpXBgwM-Yl5s29S4TLoSGtO1Q)  

![sqld-ii-2-5](https://drive.google.com/uc?id=1Rd8rL957oDK3AurA-MJv9-r-Qp5P7VG0)  




### 1. 단일 행 서브쿼리  

서브쿼리가 단일 행 비교 연산자 (=, >, >=, <, <=, <>) 와 함께 사용할 때는 서브쿼리의 결과 건수가 반드시 1건 이하이어야 한다. 만약 서브쿼리의 결과로 2건 이상을 반환하면 SQL 문은 Run Time 오류가 발생한다.  

```sql
SELECT X, Y, Z
  FROM A
 WHERE X = (SELECT X FROM A WHERE X = '001');
```

테이블 전체에 하나의 그룹 함수를 적용할 때는 결과값이 1건으로 생성되기 때문에 단일 행 서브쿼리로 사용이 가능하다.  

```sql
SELECT X, Y, Z
  FROM A
 WHERE X <= (SELECT AVG(X) FROM A);
```




### 2. 다중 행 서브쿼리  

서브쿼리의 결과가 2건 이상 반환될 수 있다면 반드시 다중 행 비교 연산자 (IN, ALL, ANY, SOME) 와 함께 사용해야 한다.   

![sqld-ii-2-6](https://drive.google.com/uc?id=1i6NUf0OgY5aKw7csNWfdDBHqg8CZke_n)  

```sql
SELECT X, Y, Z
  FROM A
 WHERE X = (SELECT X FROM A WHERE X = '001');

-- ORA-01427: 단일 행 하위 질의에 2개 이상의 행이 리턴되었다.
```

위의 SQL 문에서 서브쿼리의 결과로 2개 이상의 행이 반환되어 단일 행 비교 연산자 '=' 로 처리가 불가능 한 경우에는 다중행 비교 연산자로 바꾸어 SQL 문을 작성해야 한다.  

```sql
SELECT X, Y, Z
  FROM A
 WHERE X IN (SELECT X FROM A WHERE X = '001');
```




### 3. 다중 컬럼 서브쿼리  

다중 컬럼 서브쿼리는 서브쿼리의 결과로 여러 개의 컬럼이 반환되어 메인쿼리의 조건과 동시에 비교되는 것을 의미한다.  

```sql
SELECT X, Y, Z
  FROM A
 WHERE (X, Y) IN (SELECT X, MIN(Y) FROM A GROUP BY X);
```

메인쿼리 조건절에서 컬럼을 괄호로 묶어 서브쿼리 결과와 비교하여 원하는 결과를 얻을 수 있다. 이 기능은 SQL Server 에서는 지원하지 않는다.  




### 4. 연관 서브쿼리 (Correlated Subquery)  

연관 서브쿼리는 서브쿼리 내에 메인쿼리 컬럼이 사용된 서브쿼리이다.  

```sql
SELECT T1.X, T1.Y, T1.Z
  FROM A T1
 WHERE T1.Y < (SELECT AVG(T2.Y)
                 FROM A T2
                WHERE T1.X = T2.X
                  AND T2.Y IS NOT NULL
                GROUP BY T2.X);
```

EXISTS 서브쿼리는 항상 연관 서브쿼리로 사용된다. 또한 EXISTS 서브쿼리는 조건을 만족하는 결과가 여러 건이라도, 1건만 찾으면 더 이상 검색하지 않는다.  

```sql
SELECT T1.X, T1.Y, T1.Z
  FROM A T1
 WHERE EXISTS (SELECT 1
                 FROM A T2
                WHERE T1.X = T2.X
                  AND T2.Y BETWEEN '111' AND  '999');
```




### 5. 그 외 서브쿼리  

#### 1) SELECT 절 서브쿼리  

SELECT 절에서 사용하는 서브쿼리인 스칼라 서브쿼리 (Scalar Subquery) 는 한 행, 한 컬럼 (1 Row 1 Column) 만 반환하는 서브쿼리를 말한다. 스칼라 서브쿼리는 컬럼을 쓸 수 있는 위치는 대부분 사용할 수 있다. 스칼라 서브쿼리는 단일 행 서브쿼리이기 때문에 결과가 2건 이상 반환되면 오류가 발생한다.  

```sql
SELECT X, Y, Z,
       (SELECT AVG(X)   FROM A T2   WHERE T1.X = T2.X) X_AVG
  FROM A T1;
```


#### 2) FROM 절 서브쿼리  

FROM 절에서 사용되는 서브쿼리는 인라인 뷰 (Inline View) 라고 한다. 테이블명이 오는 위치인 FROM 절에 서브쿼리를 사용하면 동적으로 생성된 테이블과 같이 사용할 수 있다. SQL 문이 실행될 때만 임시적으로 생성되는 동적인 뷰이기 때문에 인라인 뷰를 동적 뷰 (Dynamic View) 라고 하고, 일반적인 뷰를 정적 뷰 (Static View) 라고 한다.  

인라인 뷰를 사용하는 것은 조인 방식을 사용하는 것과 동일하다. 그렇기 때문에 인라인 뷰의 컬럼은 SQL 문에서 자유롭게 참조될 수 있다.  

```sql
SELECT A.Y, B.I, B.J
  FROM A,
       (SELECT X, I, J FROM B WHERE K = '2019') B
 WHERE A.X = B.X;
```

인라인 뷰에서 ORDER BY 절을 사용할 수 있다. 따라서 정렬된 결과에 TOP-N 쿼리를 수행하여 일부 데이터만 추출하는 것이 가능하다. Oracle 에서는 ROWNUM 연산자를 사용한다.  

```sql
SELECT X, Y, Z
  FROM (SELECT X, Y, Z
          FROM A
         WHERE Y IS NOT NULL
         ORDER BY Y DESC)
 WHERE ROWNUM <= 5;
```


#### 3) HAVING 절 서브쿼리  

HAVING 절은 그룹함수로 그룹핑 된 결과에 대해 부가적인 조건을 주기 위해 사용한다.  

```sql
SELECT X, Y, AVG(Z)
  FROM A
 WHERE Y IS NOT NULL
 GROUP BY X, Y
HAVING AVG(Z) < (SELECT AVG(Z) FROM A WHERE X = '001');
```


#### 4) UPDATE 문의 SET 절 서브쿼리  

특정 컬럼의 값을 다른 테이블의 컬럼에 따라 변경하고자 할 때 다음과 같이 SQL 문을 작성할 수 있다. 이 때 서브쿼리가 NULL 을 반환할 경우 해당 컬럼도 NULL 이 될 수 있는 것에 유의해야 한다.  

```sql
UPDATE A
   SET A.Z = (SELECT B.Z FROM B WHERE A.X = B.X);
```


#### 5) INSERT 문의 VALUES 절 서브쿼리  

```sql
INSERT INTO A
(
  X, Y, Z
)
VALUES
(
  (SELECT MAX(TO_NUMBER(X))+1 FROM A),
  '345',
  'abc'
);
```




### 6. 뷰 (View)  

뷰는 테이블과 달리 실제 데이터를 가지고 있지 않다. 질의에서 뷰가 사용되면 DBMS 내부적으로 뷰 정의를 참조하여 질의를 재작성 (Rewrite) 한 후 질의를 수행한다. 뷰는 실제 데이터는 없지만 테이블의 역할을 수행하기 때문에 가상 테이블 (Virtual Table) 이라고도 한다.  

![sqld-ii-2-7](https://drive.google.com/uc?id=1PwakYFBk4sH8Osc1_xkONH3nPyyHk_vA)  

- 뷰는 다음과 같이 CREATE VIEW 문을 통해 생성할 수 있다.  

```sql
CREATE VIEW V_AB AS
SELECT A.X, A.Y, B.I, B.J
  FROM A, B
 WHERE A.X = B.X;
```

- 뷰는 테이블 뿐만 아니라 이미 존재하는 뷰를 참조해서도 생성할 수 있다.  

```sql
CREATE VIEW V_ABC AS
SELECT X, Y, I, J
  FROM V_AB
 WHERE X IN ('001', '002');
```

- 뷰를 조회하는 방법은 다음과 같다.  

```sql
SELECT X, Y, I, J
  FROM V_AB
 WHERE Y LIKE '9%';
```

- DBMS 는 내부적으로 위의 SELECT 문을 다음과 같이 재작성한다.  

```sql
SELECT X, Y, I, J
  FROM (SELECT A.X, A.Y, B.I, B.J
          FROM A, B
         WHERE A.X = B.X)
 WHERE Y LIKE '9%';
```

- 뷰를 제거하기 위해서는 DROP VIEW 문을 사용한다.  

```sql
DROP VIEW V_AB;
DROP VIEW V_ABC;
```
