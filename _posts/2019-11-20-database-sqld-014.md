---
layout: post
title: "[SQLD] 014# SQL 기본 ⑸ WHERE 절"
tags: [database, SQLD, SQL, WHERE]
categories: sqld
---


### 1. WHERE 조건절 개요  

기본적인 SQL 문장은 Oracle의 경우 SELECT 와 FROM 절로 이루어져 있다. WHERE 절은 조회하려는 데이터에 특정 조건을 부여할 목적으로 사용하기 때문에 FROM 절 뒤에 사용한다.  

```sql
SELECT [DISTINCT/ALL] 컬럼명 [ALIAS] FROM 테이블명 WHERE 조건식;
```

WHERE 조건식의 구성은 아래와 같다.  

- 컬럼명 (일반적으로 조건식의 좌측에 위치)  
- 비교 연산자  
- 문자, 숫자, 표현식 (일반적으로 조건식의 우측에 위치)  
- 비교 컬럼명 (JOIN 사용 시)  




### 2. 연산자의 종류  

![sqld-ii-1-15](https://drive.google.com/uc?id=1bAQQOmfkmg6YZzcXvgkR12_3U61pE-XM)  

![sqld-ii-1-16](https://drive.google.com/uc?id=1NvWPtAMOanXfAKWAROhlA9xwWFH_S6Hl)  




### 3. 비교 연산자  

![sqld-ii-1-17](https://drive.google.com/uc?id=1drUSIxJRVgDEMUSoM3YSGj62y2O2HS2e)  

- CHAR 변수나 VARCHAR2 와 같은 문자형 타입을 가진 컬럼을 특정 값과 비교하기 위해서는 인용부호 (작은 따옴표, 큰 따옴표) 로 묶어서 처리해야 한다. 하지만 숫자형 컬럼은 인용부호를 사용하지 않는다.  
- 숫자로 변환이 가능한 문자열과 숫자형 컬럼을 비교하는 경우 내부적으로 문자열을 숫자로 변환하여 처리한다.  

![sqld-ii-1-18](https://drive.google.com/uc?id=1zexHjmVTIZPdCyv1JcrWZ3zfJZlRazPy)  




### 4. SQL 연산자  

![sqld-ii-1-19](https://drive.google.com/uc?id=1qpmRlvQ0lQHq43iDCt2g2E9l7PTBu4kV)  

- LIKE 연산자에서는 와일드카드를 사용할 수 있다.  

![sqld-ii-1-20](https://drive.google.com/uc?id=1PwPg5BdPW4IOLWRG0CKDnqY7KTCVl1AQ)  

- IS NULL 연산자  
	-- NULL (ASCII 00) 은 값이 존재하지 않기 때문에 비교가 불가능하다. 따라서 NULL 과의 수치연산은 NULL을 리턴한다.  
    -- NULL 값과의 비교 연산은 거짓(FALSE) 를 리턴한다.  




### 5. 논리 연산자  

![sqld-ii-1-21](https://drive.google.com/uc?id=1HJ5sAAagWpHFojfqjn3P_s3bwUaTnjnT)  




### 6. 부정 연산자  

![sqld-ii-1-23](https://drive.google.com/uc?id=1FWPlkTyfC0vge5wnV53TdTGJ3p3uEJuY)  




### 7. ROWNUM, TOP 사용  

#### 1) ROWNUM  

Oracle 에서 사용하는 ROWNUM 은 SQL 처리 결과 집합의 각 행에 대해 임시로 부여되는 일련번호로, 테이블이나 집합에서 원하는 만큼의 행만 가져오고 싶을 때 WHERE 절에서 행의 개수를 제한하는 목적으로 사용한다.  

```sql
-- 1 건의 행만 조회할 때
SELECT 컬럼명 FROM 테이블명 WHERE ROWNUM = 1;
-- ( ROWNUM <=1 , ROWNUM <2 도 사용 가능 )

-- 2 건 이상의 행을 조회할 때
SELECT * FROM (SELECT ROWNUM 별칭, 컬럼명 FROM 테이블명)
 WHERE ROWNUM별칭 < N;
-- ( 인라인뷰 테이블을 이용하여 ROWNUM 조건 설정)
```


#### 2) TOP 절  

SQL Server 에서는 TOP 절을 사용하여 결과 집합으로 출력되는 행의 수를 제한할 수 있다.  

> TOP (N) [PERCENT] [WITH TIES]


- N : 반환할 행의 수를 지정하는 숫자이다.  
- PERCENT : 쿼리 결과 집합에서 N% 의 행만 반환하는 경우를 나타낸다.  
- WITH TIES : ORDER BY 절이 지정된 경우에만 사용하며, TOP N(PERCENT) 의 마지막 행과 같은 값이 있는 경우 추가 행이 출력되도록 지정할 수 있다.  

```sql
SELECT TOP(N) 컬럼명 FROM 테이블명;
```

- SQL 문장에서 ORDER BY 절이 사용되지 않으면 Oracle 의 ROWNUM 과 SQL Server 의 TOP 절은 같은 기능을 하지만, ORDER BY 절이 함께 사용되면 기능의 차이가 발생한다.  
