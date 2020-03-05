---
layout: post
title: "[SQLD] 017# SQL 기본 ⑻ 조인 (JOIN)"
tags: [database, SQLD, SQL, JOIN]
categories: sqld
---


### 1. JOIN 개요  

JOIN 이란, 두 개 이상의 테이블을 연결 또는 결합하여 데이터를 출력하는 것이다. 일반적으로 PRIMARY KEY (PK) 나 FOREIGN KEY (FK) 값의 연관성에 의해 JOIN 이 성립된다. 하나의 SQL 문장에서 여러 테이블을 조인해서 사용할 수도 있다. JOIN 의 순서는 옵티마이저에 의해 결정되며, 특정 2개의 테이블 먼저 처리된 후 완료 된 결과 테이블과 또다른 테이블이 차례대로 조인되는 방식으로 단 두 개의 집합 간에만 조인이 일어난다.  

![sqld-2-1-12](https://drive.google.com/uc?id=1EVvNB22Rla7GTmSqMH7tdP2am5Nxm5xe)  




### 2. EQUI JOIN  

EQUI (등가) JOIN 은 두 개의 테이블 간에 컬럼값이 서로 정확하게 일치하는 경우에 사용되는 방법으로, 대부분 PF ↔ FK 의 관계를 기반으로 한다. 이 기능은 계층형 (Hierarchical) 이나 망형 (Network) 데이터베이스와 비교하였을 때 관계형 데이터베이스의 큰 장점이다. JOIN 조건은 WHERE 절에서 '=' 연산자를 사용하여 표현한다.  

```sql
SELECT 테이블1.컬럼명, 테이블2.컬럼명, ...
  FROM 테이블1, 테이블2
 WHERE 테이블1.컬럼명1 = 테이블2.컬럼명2;
/* WHERE 절에 JOIN 조건 */
```

◇ ANSI/ISO SQL 표준 방식  

```sql
SELECT 테이블1.컬럼명, 테이블2.컬럼명, ...
  FROM 테이블1 INNER JOIN 테이블2
    ON 테이블1.컬럼명1 = 테이블2.컬럼명2;
/* ON 절에 JOIN 조건 */
```

- INNER JOIN 에 참여하는 N 개의 테이블은 N-1 개 이상의 JOIN 조건이 필요하다.  

- 컬럼에 접근할 때 해당 테이블을 명시하는 이유  
	-- JOIN 되는 두 테이블에 같은 컬럼명이 존재할 경우 발생하는 에러 방지  
    -- 사용자의 가독성 및 유지보수성 향상  

아래 그림은 선수가 소속된 팀의 정보를 확인하기 위한 예제이다.  

![sqld-2-1-14](https://drive.google.com/uc?id=1oIsXMTbVoc7a_J3lmmfqfK9YyEcfztvz)  




### 3. NON EQUI JOIN  

NON EQUI (비등가) JOIN 은 두 개의 테이블 간에 컬럼 값이 서로 정확하게 일치하지 않는 경우에 사용된다. '=' 연산자가 아닌 다른 연산자 (BETWEEN, >, < 등) 를 사용하여 JOIN 을 수행하는 것이다. 데이터 모델에 따라 NON EQUI JOIN 이 불가능한 경우도 있다.  

```sql
SELECT 테이블1.컬럼명, 테이블2.컬럼명, ...
  FROM 테이블1, 테이블2
 WHERE 테이블1.컬럼명1 BETWEEN 테이블2.컬럼명1 AND 테이블2.컬럼명2;
```

아래 예제는 사원의 급여 (SAL) 에 따라 어느 등급 (GRADE) 에 속하는지 조회하는 쿼리에 대한 NON EQUI JOIN 사례이다.  

```sql
SELECT E.NAME, E.SAL, S.GRADE
  FROM EMP E, SALGRADE S
 WHERE E.SAL BETWEEN S.LOSAL AND S.HISAL;
```

![sqld-2-1-17](https://drive.google.com/uc?id=1DsSQfl97LROsrF6G_Q657vO2jj3Rg5bz)  




### 4. 3개 이상 TABLE JOIN  

서로 관계가 없는 두 테이블에 공통된 테이블과 연관관계가 존재한다면, 세 개의 테이블을 JOIN 할 수 있다. 세 개의 테이블을 JOIN 할 때는 2개 이상의 JOIN 조건이 필요하다.  

```sql
SELECT A.COL_NM, B.COL_NM, C.COL_NM, ...
  FROM A, B, C
 WHERE A.PK = B.FK AND B.PK = C.FK;
```




### ◇ 정리  

특정 요구조건을 만족하는 데이터들을 분할된 테이블로부터 조회하기 위해 테이블 간에 논리적인 연관관계가 필요하고, 그 관계성을 통해 다양한 데이터를 출력할 수 있다. 이런 논리적인 관계를 구체적으로 표현하는 것이 SQL 문의 JOIN 조건이라고 할 수 있다. 관계형 데이터베이스의 큰 장점인 JOIN 을 잘못 기술하게 되면 시스템 자원 부족 또는 과다한 응답시간 지연을 발생시킬 수 있는 주요 원인이 될 수 있기 때문에 신중하게 작성해야 한다.  
