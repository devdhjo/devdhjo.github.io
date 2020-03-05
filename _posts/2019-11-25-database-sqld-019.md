---
layout: post
title: "[SQLD] 019# SQL 활용 ⑵ 집합연산자"
tags: [database, SQLD, SQL, OPERATION]
categories: sqld
---


### 1. 집합 연산자의 종류  

두 개 이상의 테이블에서 JOIN 을 사용하지 않고 연관된 데이터를 조회하는 방법 중 하나로 집합 연산자 (Set Operator) 를 사용하는 방법이다. 기존의 JOIN 에서는 FROM 절에 검색하고자 하는 테이블을 나열하고, WHERE 절에 JOIN 조건을 기술하여 원하는 데이터를 조회할 수 있었다. 하지만 집합 연산자는 여러 개의 질의의 결과를 연결하여 하나로 결합하는 방식을 사용한다. 즉, 집합 연산자는 2개 이상의 질의 결과를 하나의 결과로 만들어 준다.  

![sqld-ii-2-1](https://drive.google.com/uc?id=1QHL8Ca3WIqouWmf98wFbBB8qBfAexlvF)  

일반적으로 집합 연산자를 사용하는 상황은 1)서로 다른 테이블에서 유사한 형태의 결과를 반환하는 것을 하나의 결과로 합치고자 할 때와 2)동일 테이블에서 서로 다른 질의를 수행하여 결과를 합치고자 할 때 사용할 수 있다. 이외에도 튜닝관점에서 실행계획을 분리하고자 하는 목적으로도 사용할 수 있다.  

집합 연산자를 사용하기 위해서는 다음 제약조건을 만족해야 한다.  
- SELECT 절의 컬럼수가 동일해야 한다.  
- SELECT 절의 동일 위치에 있는 컬럼의 데이터 타입이 상호 호환 가능해야 한다.  




### 2. 집합 연산자의 연산  

집합 연산자는 개별 sql 문의 결과 집합에 대해 합집합 (UNION/UNION ALL), 교집합 (INTERSECT), 차집합 (EXCEPT) 으로 집합간의 관계를 가지고 작업을 한다.  

![sqld-2-2-5](https://drive.google.com/uc?id=1KhQwtCYcWw_OFw0rb8e7nrn2xDRbJZlQ)  

집합 연산자를 가지고 연산한 결과는 [그림 II-2-5] 와 같다. 그림의 왼쪽에 존재하는 R1, R2 는 각각의 SQL 문을 실행해서 생성된 개별 결과 집합을 의미한다.  

그림에서 보면 UNION ALL 을 제외한 다른 집합 연산자에서는 SQL 문의 결과 집합에서 먼저 중복된 건을 배제하는 작업을 수행한 후 집합 연산을 적용한다. UNION 연산에서 R1 = {1,2,3,5}, R2 = {1,2,3,4} 가 되고, 이것의 합집합 (R1 ∪ R2) 의 결과는 {1,2,3,4,5} 이다. UNION ALL 연산은 중복에 대한 배제 없이 2 개의 결과 집합을 단순히 합친 것과 동일한 결과이다. UNION ALL 의 결과는 {1,1,1,2,2,3,3,5,1,1,2,2,2,3,4} 이다.  

INTERSECT 연산에서 R1 = {1,2,3,5}, R2 = {1,2,3,4} 의 교집합 (R1 ∩ R2) 의 결과는 {1,2,3} 이다.  

EXCEPT 연산에서는 R1 = {1,2,3,5}, R2 = {1,2,3,4} 의 차집합 (R1 - R2) 의 결과는 {5} 이다. EXCEPT 연산에서는 순서가 중요하다. 만약 순서가 바뀌어서 R2 - R1 의 차집합이었다면 결과는 {4} 가 된다.  

집합 연산자를 사용하여 만들어지는 SQL문의 형태는 다음과 같다.  

```sql
SELECT 칼럼명1, 칼럼명2, ...
  FROM 테이블명1
[WHERE 조건식]
[GROUP BY 칼럼(Column)이나 표현식
HAVING 그룹조건식]

집합 연산자

SELECT 칼럼명1, 칼럼명2, ...
  FROM 테이블명2
[WHERE 조건식]
[GROUP BY 칼럼(Column)이나 표현식
HAVING 그룹조건식]
[ORDER BY 1, 2 [ASC 또는 DESC]] ;
```

집합 연산자는 사용상의 제약조건을 만족한다면 어떤 형태의 SELECT 문이라도 이용할 수 있다. ORDER BY 는 집합 연산을 적용한 최종 결과에 대한 정렬 처리이므로 가장 마지막 줄에 한번만 기술한다.  




### 3. 집합 연산 예제  

#### 1) UNION (합집합)  

```sql
SELECT X, Y, Z   FROM A   WHERE X = '001'
 UNION
SELECT X, Y, Z   FROM A   WHERE X = '002';
```

- 합집합을 WHERE 절에 IN 또는 OR 연산자로도 변환이 가능하다. 다만 IN 또는 OR 연산자를 사용할 경우에는 결과의 표시 순서가 달라질 수 있다. 동일한 표시 순서를 원한다면 ORDER BY 절을 사용해서 명시적으로 정렬 순서를 정의할 수 있다.  

```sql
SELECT X, Y, Z   FROM A   WHERE X = '001' OR X = '002';

SELECT X, Y, Z   FROM A   WHERE X IN ('001', '002');
```


#### 2) UNION ALL (합집합)  

```sql
SELECT X, Y, Z   FROM A   WHERE X = '001'
 UNION
SELECT X, Y, Z   FROM A   WHERE Y = '999';
```

- 예제2 또한 OR 연산자를 사용하여 SQL문을 변경할 수 있다. 다만 조건절에서 서로 다른 컬럼을 사용했기 때문에 IN 연산자는 사용할 수 없다.  

```sql
SELECT X, Y, Z   FROM A   WHERE X = '001' OR Y = '999';
```

- UNION 연산자 대신 UNION ALL 연산자를 사용하면 결과 집합에서 중복을 제외시키지 않기 때문에 더 많은 행이 조회된다.  

```sql
SELECT X, Y, Z   FROM A   WHERE X = '001'
 UNION ALL
SELECT X, Y, Z   FROM A   WHERE Y = '999';
```


#### 3) 그룹함수  

```sql
SELECT X, AVG(Y)   FROM A   GROUP BY X
 UNION
SELECT Z, AVG(Y)   FROM A   GROUP BY Z
 ORDER BY 1;
```

- 그룹함수 또한 집합 연산자에서 사용이 가능하다. 집합 연산자의 결과를 표시할 때 HEADING 부분은 첫번째 SQL 문에서 사용된 HEADING 이 적용된다.  


#### 4) MINUS/EXCEPT (차집합)  

```sql
- Oracle
SELECT X, Y, Z   FROM A   WHERE X = '001'
 MINUS
SELECT X, Y, Z   FROM A   WHERE Y = '999'
 ORDER BY 1, 2, 3;
```

- SQL Server 에서는 MINUS 대신에 EXCEPT 를 사용할 수 있다.  
- 차집합은 앞의 집합의 결과에서 뒤의 집합의 결과를 빼는 것이다. 연산자의 앞에 오는 SQL 문의 조건은 만족하고, 뒤에 오는 SQL 문의 조건은 만족하지 않는 SQL 문과 동일한 결과를 얻을 수 있다.  

```sql
SELECT X, Y, Z   FROM A   WHERE X = '001' AND Y <> '999';
```

- MINUS 연산자는 NOT EXISTS 또는 NOT IN 서브쿼리를 이용한 SQL 문으로 변경이 가능하다.


#### 5) INTERSECT (교집합)  

```sql
SELECT X, Y, Z   FROM A   WHERE X = '001'
INTERSECT
SELECT X, Y, Z   FROM A   WHERE Y = '999';
```

- 교집합의 결과는 두 조건을 모두 만족하는 데이터의 집합이다. 연산자의 앞에 오는 SQL 문의 조건과 뒤의 SQL 문의 조건을 만족하는 SQL 문과 동일한 결과를 얻을 수 있다.  

```sql
SELECT X, Y, Z   FROM A   WHERE X = '001' AND Y = '999';
```

- INTERSECT 연산자는 EXISTS 또는 IN 서브쿼리를 이용한 SQL 문으로 변경할 수 있다.  

```sql
SELECT X, Y, Z   FROM A T1
 WHERE T1.X = '001'
   AND EXISTS (SELECT 1 FROM A T2
                WHERE T2.X = T1.X
                  AND T2.Y = '999');
```

```sql
SELECT X, Y, Z   FROM A
 WHERE X = '001'
   AND Y IN (SELECT Y FROM A WHERE Y = '999');
```
