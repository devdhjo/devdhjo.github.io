---
layout: post
title: "[SQLD] 015# SQL 기본 ⑹ GROUP BY, HAVING, ORDER BY"
tags: [database, SQLD, SQL]
categories: sqld
---


### Ⅰ. GROUP BY, HAVING  


### 1. 집계 함수 (Aggregate Function)  

- 집계함수 : 여러 행들의 그룹이 모여서 그룹당 단 하나의 결과를 돌려주는 함수이다.  
	-- GROUP BY 절은 행들을 소그룹화 한다.  
    -- SELECT 절, HAVING 절, ORDER BY 절에 사용할 수 있다.  

![sqld-ii-1-36](https://drive.google.com/uc?id=1eaSSgzYv1txB-fZhEZnO_Ax0rT5Mho9B)  

- 일반적으로 집계함수는 GROUP BY 절과 함께 사용하지만, 테이블 전체가 하나의 그룹이 되는 경우에는 GROUP BY 절 없이 단독으로도 사용할 수 있다.  




### 2. GROUP BY 절  

```sql
 SELECT [DISTINCT] 컬럼명 [ALIAS]
   FROM 테이블명 [WHERE 조건식]
[ WHERE 조건식]
[ GROUP BY 컬럼 또는 표현식]
[HAVING 그룹조건식]
[ ORDER BY 컬럼명];
```


GROUP BY 절과 HAVING 절은 다음과 같은 특성을 가진다.  
- GROUP BY 절을 통해 소그룹별 기준을 정한 후, SELECT 절에 집계 함수를 사용한다.  
- 집계 함수의 통계 정보는 NULL 값을 가진 행을 제외하고 수행한다.  
- GROUP BY 절에서는 SELECT 절과는 달리 ALIAS 명을 사용할 수 없다.  
- 집계 함수는 WHERE 절에는 올 수 없다. (GROUP BY 절보다 WHERE 절이 먼저 수행된다.)  
- WHERE 절은 전체 데이터를 GROUP 으로 나누기 전에 행들을 미리 제거시킨다.  
- HAVING 절은 GROUP BY 절의 기준 항목이나 소그룹의 집계 함수를 이용한 조건을 표시할 수 있다.  
- GROUP BY 절에 의한 소그룹별로 만들어진 집계 데이터 중, HAVING 절에서 제한 조건을 두어 조건을 만족하는 내용만 출력한다.  
- HAVING 절은 일반적으로 GROUP BY 절 뒤에 위치한다.  




### 3. HAVING 절  

WHERE 절은 FROM 절에 정의된 집합 (주로 테이블) 의 개별 행에 WHERE 절의 조건절이 먼저 적용되고, WHERE 절의 조건에 맞는 행이 GROUP BY 절의 대상이 된다. 그 다음 결과 집합의 행에 HAVING 조건절이 적용된다. 결과적으로 HAVING 절의 조건을 만족하는 내용만 출력된다. 즉, HAVING 절은 WHERE 절과 비슷하지만 그룹을 나타내는 결과 집합의 행에 조건이 적용된다는 점에서 차이가 있다.  

GROUP BY 절과 HAVING 절의 순서를 바꾸어서 수행하더라도 문법 에러가 없고 결과물도 동일하게 출력한다. 그렇지만 SQL 내용을 보면, 그룹핑 되어 통계 정보가 만들어 지고, 이후 적용된 결과 값에 대한 HAVING 절의 제한 조건에 맞는 데이터만 출력하는 것으로 GROUP BY 절과 HAVING 절의 논리적 순서를 지키는 것을 권고한다.  

GROUP BY 소그룹의 데이터 중 일부만 필요한 경우, GROUP BY 연산 전 WHERE 절에서 조건을 적용하여 필요한 데이터만 추출하여 GROUP BY 연산을 하는 방법과, GROUP BY 연산 후 HAVING 절에서 필요한 데이터만 필터링 하는 두 가지 방법을 사용할 수 있다. 그렇지만 가능하면 WHERE 절에서 조건절을 적용하여 GROUP BY 의 계산 대상을 줄이는 것이 효율적이다.  




### 4. 집계 함수와 NULL  

NULL 을 ZERO 로 표현하기 위해 NVL (Oracle) / ISNULL (SQL Server) 함수를 사용하는 경우가 많은데, 다중 행 함수를 사용하는 경우 불필요한 부하가 발생할 수 있다. 다중 행 함수는 입력 값으로 전체 건수가 NULL 값인 경우에만 함수의 결과가 NULL 이 나오고, 전체 건수 중 일부만 NULL 인 경우는 NULL 인 행을 다중 행 함수의 대상에서 제외한다.  
예를 들면, 100명 중 10명의 성적이 NULL 값일 때, 평균을 구하는 다중 행 함수 AVG 를 사용하면 NULL 값이 아닌 90명의 성적에 대해서 평균값을 구하게 된다.  

CASE 표현 사용 시 ELSE 절을 생략하게 되면 Default 값이 NULL 이다. 반면, SUM(CASE 컬럼 WHEN 값1 THEN 값2 ELSE 0 END) 처럼 ELSE 절에서 0을 지정하면 불필요한 0이 SUM 연산에 사용되므로 자원의 사용이 많아진다. 따라서 가능한 ELSE 절은 상수값을 사용하지 않거나, 작성하지 않는 것이 좋다. Oracle DECODE GKATNDML 4번째 인자 또한 Default 로 NULL 이 할당된다.  
레포트 출력 시 NULL 이 아닌 0 을 표기하고 싶다면 SUM(NVL(i,0)) 보다는 NVL(SUM(i,0)) 처럼 전체 SUM 의 결과에 대해 함수를 사용하도록 한다.  




### Ⅱ. ORDER BY  

### 1. ORDER BY 절  

ORDER BY 절은 SQL 문장으로 조회된 데이터들을 특정 기준으로 정렬하는 데 사용한다. ORDER BY 절에 컬럼명 대신 ALIAS 나 컬럼 순서를 나타내는 정수도 사용 가능하다. 또한 별도로 정렬 방식을 지정하지 않으면 오름차순이 적용되며, SQL 문장의 제일 마지막에 위치한다.  

- ASC (Ascending) : 조회한 데이터를 오름차순으로 정렬한다. (기본값이기 때문에 생략 가능)  
- DESC (Descending) : 조회한 데이터를 내림차순으로 정렬한다.  

```sql
 SELECT 컬럼명 [ALIAS] FROM 테이블명
[ WHERE 조건식]
[ GROUP BY 컬럼 또는 표현식]
[HAVING 그룹조건식]
[ ORDER BY 컬럼 또는 표현식 [ASC / DESC]]
```

ORDER BY 절의 특징을 정리하면 다음과 같다.  
- 기본적인 정렬 순서는 오름차순 (ASC) 이다.  
- 숫자형 데이터 타입은 오름차순으로 정렬했을 때 가장 작은 값 부터 출력된다.  
- 날짜형 데이터 타입은 오름차순으로 정렬했을 때 가장 이전의 값이 먼저 출력된다.  
- Oracle 에서는 NULL 을 가장 큰 값으로 간주하여 오름차순으로 정렬하면 가장 마지막에, 내림차순은 가장 먼저 출력된다.  
- SQL Server 에서는 NULL 을 가장 작은 값으로 간주하여 오름차순으로 정렬하면 가장 먼저, 내림차순은 가장 마지막에 출력된다.  




### 2. SELECT 문장 실행 순서  

```sql
５    SELECT 컬럼명 [ALIAS]
１      FROM 테이블명
２     WHERE 조건식
３     GROUP BY 컬럼 또는 표현식
４    HAVING 그룹조건식
６     ORDER BY 컬럼 또는 표현식
```

위 순서는 옵티마이저가 SQL 문장의 SYNTAX, SEMANTIC 에러를 점검하는 순서와 동일하다. FROM 절에 정의되지 않은 테이블의 컬럼은 다른 구문에서 사용할 수 없지만, ORDER BY 절에서는 SELECT 하지 않은 컬럼이라도 이미 메모리에 올라와 있기 때문에 사용할 수 있는 것이다.  
단, SELECT DISTINCT 를 지정하거나, SQL 문장에 GROUP BY 가 있거나, UNION 을 사용하는 경우에는 해당 컬럼이 SELECT 목록에 표기되어야 한다.  

GROUP BY 절에서 그룹핑 기준을 정의하게 되면 데이터베이스는 일반적인 SELECT 문장처럼 FROM 절에 정의된 테이블 구조를 그대로 사용하는 것이 아니라, GROUP BY 절의 그룹핑 기준에 사용된 컬럼과 집계 함수에 사용될 숫자형 데이터 컬럼의 집합을 새로 만드는데, 개별 데이터는 필요하지 않기 때문에 저장하지 않는다. 따라서 GROUP BY 이후 수행절인 SELECT 절이나 ORDER BY 절에서 개별 데이터를 사용하는 경우 에러가 발생한다.  




### 3. TOP N 쿼리  

#### 1) ROWNUM  

Oracle 에서 순위가 높은 N 개의 행을 추출하기 위해 ORDER BY 절과 WHERE 절의 ROWNUM 조건을 같이 사용하는 경우가 있는데, 이 두 조건으로는 원하는 결과를 얻을 수 없다. Oracle 의 경우 ORDER BY 절은 결과 집합을 결정하는 데 관여하지 않고, 데이터의 일부가 먼저 추출된 후 정렬 작업이 일어나기 때문이다.  

```sql
< 예 > 사원 테이블에서 급여가 높은 3명만 내림차순으로 출력하고자 한다.

-- 잘못된 사례
SELECT ENAME, SAL
  FROM EMP
 WHERE ROWNUM < 4
 ORDER BY SAL DESC;

-- 올바른 사례
SELECT ENAME, SAL
  FROM (SELECT ENAME, SAL FROM EMP ORDER BY SAL DESC)
 WHERE ROWNUM < 4;
```

- ORDER BY 절이 없으면 Oracle 의 ROWNUM 조건과 SQL Server 의 Top 절은 같은 결과를 보인다. 하지만 ORDER BY 절이 사용되는 경우 Oracle 은 ROWNUM 조건을 ORDER BY 절보다 먼저 철되는 WHERE 에서 처리하므로, 인라인뷰에서 먼저 데이터 정렬을 수행한 후 메인 쿼리에서 ROWNUM 조건을 사용해야 한다.  


#### 2) TOP()  

SQL Server 에서는 TOP 조건을 사용하게 되면 별도 처리 없이 관련 ORDER BY 절의 데이터 정렬 후 원하는 일부 데이터만 쉽게 출력할 수 있다.  

> TOP(N) [PERCENT] [WITH TIES]

- WITH TIES 옵션은 ORDER BY 절의 조건 기준으로 TOP N의 마지막 행으로 표시되는 추가 행의 데이터가 같을 경우 N + 동일 정렬 순서 데이터를 추가 반환하도록 지정하는 옵션이다.  
