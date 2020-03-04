---
layout: post
title: "[MySQL] 005# DML ⑴ 데이터 조회 (SELECT)"
tags: [database, MySQL, DML]
categories: mysql
---

> [[부스트코스] 웹 프로그래밍](https://www.edwith.org/boostcourse-web/joinLectures/12943) 강의를 수강한 학습 내용입니다.  
> -- 출처 : [웹 프로그래밍 > 2.2) DML(select, insert, update, delete)](https://www.edwith.org/boostcourse-web/lecture/16721/)



### ◇ 데이터 조회 (SELECT)  

```sql
SELECT [DISTINCT] 컬럼명 [ALIAS]
  FROM 테이블명;
```

- DISTINCT : 중복행을 제거하고 조회  
- ALIAS : 컬럼의 명칭을 부여  


### 1. 전체 데이터 검색  

- SELECT 뒤에 * 을 입력하면 모든 데이터를 출력할 수 있다.  

```sql
SELECT * FROM 테이블명;
```

- 예제  
-- DEPARTMENT 테이블의 모든 데이터를 출력한다.  

```sql
SELECT * FROM DEPARTMENT;
```

![mysql_select_001](https://cphinf.pstatic.net/mooc/20180131_204/15173752726665yfHB_PNG/2_8_2_select__.png?type=w760)  


### 2. 특정 컬럼 검색  

- SELECT 뒤에 컬럼명을 콤마(,)로 구분하여 나열한다.  

```sql
SELECT 컬럼1, 컬럼2, ... FROM 테이블명;
```

- 예제  
-- EMPLOYEE 테이블에서 사번(empno), 이름(name), 직업(job)을 출력한다.  

```sql
SELECT empno, name, job FROM EMPLOYEE;
```

![mysql_select_002](https://cphinf.pstatic.net/mooc/20180131_242/1517375406686GtLK0_PNG/2_8_2_select__%28__%29.png?type=w760)  


### 3. 컬럼 명칭 설정 (ALIAS)  

```sql
SELECT 컬럼1 as 명칭1, 컬럼2 as 명칭2, ... FROM 테이블명;
```

- 예제  
-- EMPLOYEE 테이블에서 사번(empno), 이름(name), 직업(job)을 명칭으로 출력한다.  

```sql
SELECT empno as 사번, name as 이름, job as 직업 FROM EMPLOYEE;
```

![mysql_select_003](https://cphinf.pstatic.net/mooc/20180131_241/1517375599282HCWV3_PNG/2_8_2_select__%28_alias%29.png?type=w760)  


### 4. 컬럼 합성 (Concatenation)  

- 문자열 결합함수 concat 을 사용하여 여러 컬럼을 하나의 컬럼으로 출력한다.  

- 예제  
-- EMPLOYEE 테이블에서 사번과 부서번호를 하나의 컬럼으로 출력한다.  

```sql
SELECT concat(empno, '-', deptno) as '사번-부서번호'
  FROM EMPLOYEE;
```

![mysql_select_004](https://cphinf.pstatic.net/mooc/20180131_100/1517375714196vQgJz_PNG/2_8_2_select__%28_%29.png?type=w760)  


### 5. 중복행 제거 (DISTINCT)  

- 예제  
-- EMPLOYEE 테이블에서 모든 부서 번호를 출력한다.  

(1) 기본 조회는 사원의 수 만큼 부서번호가 출력된다.  

```sql
SELECT deptno FROM EMPLOYEE;
```

![mysql_select_005](https://cphinf.pstatic.net/mooc/20180131_181/1517375842547vAATO_PNG/2_8_2_select__%28_%29.png?type=w760)  

(2) DISTINCT 를 사용하면 중복되지 않게 출력된다.  

```sql
SELECT DISTINCT deptno FROM EMPLOYEE;
```

![mysql_select_006](https://cphinf.pstatic.net/mooc/20180131_259/1517375862194IANYL_PNG/2_8_2_select__%28_%29-2.png?type=w760)  


### 6. 정렬 (ORDER BY)  

- 예제 1 : 오름차순 정렬  
-- EMPLOYEE 테이블에서 사번(empno), 이름(name), 직업(job)을 이름의 순서대로 출력한다.  

```sql
SELECT empno, name, job
  FROM EMPLOYEE
 ORDER BY name;
```

![mysql_select_007](https://cphinf.pstatic.net/mooc/20180131_293/1517376141070o18OB_PNG/2_8_2_select__%28alias___%29.png?type=w760)  

- 예제 2 : 내림차순 정렬  
-- EMPLOYEE 테이블에서 사번(empno), 이름(name), 직업(job)을 이름의 내림차순으로 출력한다.  

```sql
SELECT empno, name, job
  FROM EMPLOYEE
 ORDER BY name desc;
```

![mysql_select_008](https://cphinf.pstatic.net/mooc/20180131_124/15173762661850euMv_PNG/2_8_2_select__%28_____%29.png?type=w760)  


### 7. 특정 행 검색 (WHERE)  

- 조건식을 이용하여 해당 조건과 일치하는 행만 출력한다.  

```sql
SELECT [DISTINCT] 컬럼명 [ALIAS]
  FROM 테이블명
 WHERE 조건식
 ORDER BY 컬럼 또는 표현식 [ASC | DESC];
```

#### 1) 산술 비교 연산자  

- 예제  
-- EMPLOYEE 테이블에서 고용일(hiredate)이 1981년 이전의 사원이름과 고용일을 출력한다.  

```sql
SELECT name, hiredate
  FROM EMPLOYEE
 WHERE hiredate < '1981-01-01';
```

![mysql_select_009](https://cphinf.pstatic.net/mooc/20180131_47/1517377275536vNSNE_PNG/2_8_2_select__%28__-where%29.png?type=w760)  

#### 2) 논리 연산자  

- 예제  
-- EMPLOYEE 테이블에서 부서번호가 30인 사원이름과 부서번호를 출력한다.  

```sql
SELECT name, deptno
  FROM EMPLOYEE
 WHERE deptno = 30;
```

![mysql_select_010](https://cphinf.pstatic.net/mooc/20180131_226/1517377406317qRg8K_PNG/2_8_2_select__%28__-where%29-2.png?type=w760)  

#### 3) IN 키워드  

- 예제  
-- EMPLOYEE 테이블에서 부서번호가 10 또는 30인 사원이름과 부서번호를 출력한다.  

```sql
SELECT name, deptno
  FROM EMPLOYEE
 WHERE deptno IN (10, 30);
```

![mysql_select_011](https://cphinf.pstatic.net/mooc/20180131_165/15173774686553LyvB_PNG/2_8_2_select__%28__-where%29-3.png?type=w760)  

#### 4) LIKE 키워드  

- 와일드카드를 사용하여 특정 문자를 포함한 값에 대한 조건을 처리  
- '%' 는 여러 개의 문자열, '_' 는 한 개의 문자를 나타내는 와일드카드  

- 예제  
-- EMPLOYEE 테이블에서 이름에 'A' 가 포함된 사원의 이름과 직업을 출력한다.  

```sql
SELECT name, job
  FROM EMPLOYEE
 WHERE name LIKE '%A%';
```

![mysql_select_012](https://cphinf.pstatic.net/mooc/20180131_100/1517377500401b7GdO_PNG/2_8_2_select__%28__-where%29-4.png?type=w760)  


### 8. 그룹함수 (GROUP BY)  

- 조회 결과의 그룹을 나누어 출력한다.  

- 예제  
-- EMPLOYEE 테이블에서 부서번호가 30인 직원의 급여 평균과 총 합계를 출력한다.  

(1) 기본 조회는 모든 행을 기준으로 한 조회 결과가 출력된다.  

```sql
SELECT AVG(salary) , SUM(salary)
  FROM EMPLOYEE
 WHERE deptno = 30;
```

![mysql_select_013](https://cphinf.pstatic.net/mooc/20180131_263/1517380309278sUNR3_PNG/2_8_2_select__%28%29.png?type=w760)  

(2) GROUP BY 를 사용하면 조회 결과에 그룹이 구분되어 출력된다.  

```sql
SELECT AVG(salary) , SUM(salary)
  FROM EMPLOYEE
 GROUP BY deptno;
```

![mysql_select_014](https://cphinf.pstatic.net/mooc/20180131_9/1517380488029v1nbz_PNG/2_8_2_select__%28_groupby_%29.png?type=w760)  


### 9. 함수  

:---:|:---
FLOOR(x)|x보다 크지 않은 가장 큰 정수를 반환합니다. BIGINT로 자동 변환  
CEILING(x)|x보다 작지 않은 가장 작은 정수를 반환  
ROUND(x)|x에 가장 근접한 정수를 반환  
POW(x,y), POWER(x,y)|x의 y 제곱 승을 반환  
GREATEST(x,y,...)|가장 큰 값을 반환  
LEAST(x,y,...)|가장 작은 값을 반환  
CURDATE()<BR>CURRENT_DATE|오늘 날짜를 YYYY-MM-DD나 YYYYMMDD 형식으로 반환  
CURTIME()<BR>CURRENT_TIME|현재 시각을 HH:MM:SS나 HHMMSS 형식으로 반환  
NOW(), SYSDATE()<BR>CURRENT_TIMESTAMP|오늘 현시각을 YYYY-MM-DD HH:MM:SS나 YYYYMMDDHHMMSS 형식으로 반환   
DATE_FORMAT(date,format)|입력된 date를 format 형식으로 반환  
PERIOD_DIFF(p1,p2)|YYMM이나 YYYYMM으로 표기되는 p1과 p2의 차이 개월을 반환  
