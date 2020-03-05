---
layout: post
title: "[SQLD] 023# SQL 활용 ⑹ 윈도우 함수"
tags: [database, SQLD, SQL, RANK, DENSE_RANK, ROW_NUMBER]
categories: sqld
---



### 1. WINDOW FUNCTION 개요  

기존 관계형 데이터베이스는 컬럼과 컬럼간의 연산, 비교, 집계 등은 쉬운 반면, 행과 행간의 관계를 정의하거나 비교, 연산하는 것은 하나의 SQL 문으로 처리하기 어렵다. 절차형 프로그램을 작성하거나, INLINE VIEW 를 이용하여 복잡한 SQL 문을 작성해야 했던 것을 부분적으로라도 행과 행간의 관계를 쉽게 정의하기 위해 사용하는 함수가 WINDOW FUNCTION 이다.  

윈도우 함수를 사용하면 복잡한 프로그램을 하나의 SQL 문장으로 쉽게 해결할 수 있다. 분석함수 (ANALYTIC FUNCTION) 나 순위함수 (RANK FUNCTION) 로 알려져 있는 윈도우 함수 (ANSI/ISO SQL 표준은 WINDOW FUNCTION 으로 사용) 는 데이터웨어하우스에서 발전한 기능이다. WINDOW 함수는 기존에 사용하던 집계 함수를 포함한 새로운 기능을 지원하지만, 다른 함수와는 달리 중첩 (NEST) 하여 사용할 수 없고, 서브쿼리에서는 사용 가능하다.  


#### 1) WINDOW FUNCTION 종류  

순위함수|집계함수|행순서함수|비율함수
---|---|---|---|---
RANK<BR>DENSE_RANK<BR>ROW_NUMBER|SUM, AVG<BR>MAX, MIN<BR>COUNT|FIRST_VALUE<BR>LAST_VALUE<BR>LAG, LEAD|CUME_DIST<BR>PERCENT_RANK<BR>NTILE<BR>RATIO_TO_REPORT

-- NTILE 함수는 ANSI/ISO SQL 표준에는 없지만 Oracle, SQL Server 에서 지원하며, RATIO_TO_REPORT 함수는 Oracle 에서만 지원한다.  


#### 2) WINDOW FUNCTION SYNTAX  

```sql
SELECT WINDOW_FUNCTION (ARGUMENTS)
       OVER ( [PARTITION BY 컬럼]
              [ORDER BY 절]
              [WINDOWING 절] )
  FROM 테이블명;
```

- WINDOW_FUNCTION (ARGUMENTS) : 함수에 따라 0~N 개의 인수가 지정될 수 있다.  

- PARTITION BY 절 : 전체 집합을 기준에 의해 소그룹으로 나눌 수 있다.  

- ORDER BY 절 : 순위를 지정할 항목을 기술한다.  

- WINDOWING 절 : WINDOWING 절은 함수의 대상이 되는 행 기준의 범위를 강력하게 지정할 수 있다. ROWS 는 물리적인 결과 행의 수를, RANGE 는 논리적인 값에 의한 범위를 나타내는데, 둘 중의 하나를 선택해서 사용할 수 있다.  
    -- WINDOWING 절은 SQL Server 에서는 지원하지 않는다.  




### 2 . 그룹 내 순위 함수  

#### 1) RANK 함수  

RANK 함수는 ORDER BY 를 포함한 QUERY 문에서 특정 컬럼에 대한 순위를 구하는 함수이다. 이 때 특정 범위 (PARTITION) 내에서 순위를 구할 수도 있고, 전체 데이터에 대한 순위를 구할 수도 있다. 또한 동일한 값에 대해서는 동일한 순위를 부여하게 된다.  

- [예제] 사원 데이터에서 급여가 높은 순서와 JOB 별로 급여가 높은 순서를 같이 출력한다.  

```sql
SELECT JOB, ENAME, SAL,
       RANK( ) OVER (ORDER BY SAL DESC) ALL_RANK,
       RANK( ) OVER (PARTITION BY JOB ORDER BY SAL DESC) JOB_RANK
  FROM EMP;
```

![sqld-24-2-1-1](https://drive.google.com/uc?id=1CsiI6lRykSUDxM93jtwGLunwfJjG2_JX)  

-- 업무 구분이 없는 ALL_RANK 컬럼에서 동일한 SAL 값은 동일한 순위를 가진다.  
-- PARTITION BY 로 JOB 을 구분한 JOB_RANK 는 업무 내 범위에서만 순위를 부여한다.  
-- 하나의 SQL 문에 (ORDER BY SAL DESC) 와 (PARTITION BY JOB) 조건이 충돌하여 JOB 별로는 정렬되지 않고, SAL DESC 조건으로 정렬되었다.  


- [예제] 전체 SAL 순위를 구하는 ALL_RANK 컬럼은 제외하고, JOB_RANK 만 조회한다.  

```sql
SELECT JOB, ENAME, SAL,
       RANK() OVER (PARTITION BY JOB ORDER BY SAL DESC) JOB_RANK
  FROM EMP;
```

![sqld-24-2-1-2](https://drive.google.com/uc?id=178xL3TBlBYOGdWEgdnpXBkkLm-G7_ZfC)  

-- 업무별 SAL 순서를 구하는 JOB_RANK 만 사용한 경우 파티션의 기준이 된 JOB 과 SAL 별 정렬이 되어있는 것을 알 수 있다.  




#### 2) DENSE_RANK 함수  

DENSE_RANK 함수는 RANK 함수와 다르게 동일한 순위를 하나의 건수로 취급한다.  

- [예제] 사원데이터에서 급여가 높은 순서의 RANK, DENSE_RANK 결과를 조회한다.  

```sql
SELECT JOB, ENAME, SAL,
       RANK( ) OVER (ORDER BY SAL DESC) RANK,
       DENSE_RANK( ) OVER (ORDER BY SAL DESC) DENSE_RANK
  FROM EMP;
```

![sqld-24-2-2](https://drive.google.com/uc?id=156iJxB2V5cG6hqqWTTfSXgZMybN_cgU_)  

-- ANALYST 의 SAL 은 값이 동일하기 때문에 같은 순위를 부여한다. 이 다음 순위가 RANK 함수는 4, DENSE_RANK 함수는 3 으로 달라지는 것을 통해 차이점을 알 수 있다.  




#### 3) ROW_NUMBER 함수  

ROW_NUMBER 함수는 RANK 나 DENSE_RANK 함수가 동일한 값에 대해 동일한 순위를 부여하는 것에 반해, 동일한 값이라도 고유한 순위를 부여한다.  

- [예제] 사원데이터에서 급여가 높은 순서의 RANK, ROW_NUMBER 결과를 조회한다.  

```sql
SELECT JOB, ENAME, SAL,
       RANK( ) OVER (ORDER BY SAL DESC) RANK,
       ROW_NUMBER() OVER (ORDER BY SAL DESC) ROW_NUMBER
  FROM EMP;
```

![sqld-24-2-3](https://drive.google.com/uc?id=1CtJjp-d3b5vdddrIdNHy_zS-aWCpXvQi)  

ROW_NUMBER 는 동일한 순위를 배제하기 위해 유니크한 순위를 정한다. 동일한 SAL 에 대해 정해지는 순서는 데이터베이스 별로 다르다. (Oracle 의 경우 rowid 가 적은 행이 먼저 나온다.)  
순서를 지정하기 위해서는 ROW_NUMBER() OVER (ORDER BY SAL DESC, ENAME) 과 같이 ORDER BY 절을 이용하여 표기해야 한다.  




### 3. 일반 집계 함수  

#### 1) SUM 함수  

SUM 함수를 이용해 파티션별 윈도우의 합을 구할 수 있다.  

- [예제] 사원의 급여 (SAL) , 동일한 매니저를 가진 사원의 급여 합계 (MGR_SUM) 를 조회한다.  

```sql
SELECT MGR, ENAME, SAL,
       SUM(SAL) OVER (PARTITION BY MGR) MGR_SUM
  FROM EMP;
```

![sqld-24-3-1-1](https://drive.google.com/uc?id=1LPZP9fNQm_fekK3CSdeH-jnu0xC2A9Jm)  

- [예제] OVER 절 내에 ORDER BY 절을 추가하여 파티션 내 데이터를 정렬하고, 누적값을 출력한다.  
(SQL Server 에서는 집계함수의 OVER 절 내의 ORDER BY 절을 지원하지 않는다.)  

```sql
SELECT MGR, ENAME, SAL,
       SUM(SAL) OVER (PARTITION BY MGR ORDER BY SAL RANGE UNBOUNDED PRECEDING) AS MGR_SUM
  FROM EMP;
```

-- RANGE UNBOUNDED PRECEDING : 현재 행을 기준으로 파티션 내의 첫 번째 행까지의 범위를 지정한다.  

![sqld-24-3-1-2](https://drive.google.com/uc?id=1S9pzP3wCiEaEkFlRvObcsAlURaKLXz5-)  

-- 7698-WARD 와 7698-MARTIN 은 급여가 같으므로 같은 ORDER 로 취급하여 MGR_SUM 의 값이 950+1250+1250=3450 의 값으로 표기되었다.  




#### 2) MAX 함수  

MAX 함수를 이용하여 파티션별 윈도우의 최대값을 구할 수 있다.  

- [예제] 사원의 급여 (SAL), 같은 매니저를 두고 있는 사원의 급여 중 최대값 (MGR_MAX) 을 조회한다.  

```sql
SELECT MGR, ENAME, SAL,
       MAX(SAL) OVER (PARTITION BY MGR) AS MGR_MAX
  FROM EMP;
```

![sqld-24-3-2-1](https://drive.google.com/uc?id=1goyt9wFWUcHTrZwsz20qj1ZxOto5P8qh)  

-- 실행 결과를 보면 파티션 내의 최대값을 모든 행에서 MGR_MAX 라는 컬럼으로 확인할 수 있다.  

- [예제] INLINE VIEW 를 이용한 파티션별 최대값을 가진 행만 추출한다.  

```sql
SELECT MGR, ENAME, SAL
  FROM (SELECT MGR, ENAME, SAL,
               MAX(SAL) OVER (PARTITION BY MGR) AS IV_MAX_SAL
          FROM EMP)
 WHERE SAL = IV_MAX_SAL ;
```

![sqld-24-3-2-2](https://drive.google.com/uc?id=1uMDDBZiYVQGqAyP3hq4I0HDRkZxxzwOS)  

-- 실행 결과 MGR 7566 의 SCOTT, FORD 는 동일한 값을 가지기 때문에 'WHERE SAL = IV_MAX_SAL' 조건에 의해 두 건 모두 출력되었다.  




#### 3) MIN 함수  

MIN 함수를 이용하여 파티션별 윈도우의 최소값을 구할 수 있다.  

- [예제] 사원의 급여 (SAL), 같은 매니저를 가진 사원의 급여 중 최소값 MIN(SAL) 을 조회하고 입사일자 기준으로 정렬한다.  

```sql
SELECT MGR, ENAME, HIREDATE, SAL,
       MIN(SAL) OVER(PARTITION BY MGR ORDER BY HIREDATE) AS MGR_MIN
  FROM EMP;
```

![sqld-24-3-3](https://drive.google.com/uc?id=1L6_YKnf73p_A_iNDW74ognygWdVqyXSC)  




#### 4) AVG 함수  

AVG 함수와 파티션별 ROWS 윈도우를 이용하여 원하는 조건에 맞는 데이터에 대한 통계값을 구한다.  

- [예제] EMP 테이블에서 같은 매니저를 가진 사원의 평균 SAL 을 조회하는데, 같은 매니저 내에서 바로 앞, 뒤 사번인 직원만을 대상으로 한다.  

```sql
SELECT MGR, ENAME, HIREDATE, SAL,
       ROUND (AVG(SAL) OVER (PARTITION BY MGR ORDER BY HIREDATE ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING)) AS MGR_AVG
  FROM EMP;
```

-- ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING : 현재 행을 기준으로 파티션 내에서 앞의 한 건, 현재 행, 뒤의 한 건을 범위로 지정한다.  

![sqld-24-3-4](https://drive.google.com/uc?id=1bKte5B1CBd2lOmAWjjjyX__cdkDavkft)  

-- ALLEN 의 경우 파티션 내에서 첫 번째 데이터이므로 앞의 한 건은 평균값 집계 대상이 없다. 따라서 평균값 집계 대상은 본인의 데이터와 뒤의 한 건으로 평균값을 구한다. (1600+1250)/2=1425  

-- TURNER 의 경우 앞의 한 건, 본인, 뒤의 한 건으로 평균값을 구한다. (1250+1500+1250)/3=1333  

-- JAMES 의 경우 파티션 내의 마지막 데이터이므로 뒤의 한 건을 제외한 앞의 한 건과 본인의 데이터를 가지고 평균값을 구한다. (1250+950)/2=1100  




#### 5) COUNT 함수  

COUNT 함수와 파티션별 ROWS 윈도우를 이용하여 원하는 조건에 맞는 데이터에 대한 통계값을 구한다.  

- [예제] 사원 본인의 급여보다 50 이하로 적거나, 150 이하로 많은 급여를 받는 인원수를 조회하고, 급여 기준으로 정렬한다.  

```sql
SELECT ENAME, SAL,
       COUNT(*) OVER (ORDER BY SAL RANGE BETWEEN 50 PRECEDING AND 150 FOLLOWING) AS SIM_CNT
  FROM EMP;
```

-- RANGE BETWEEN 50 PRECEDING AND 150 FOLLOWING : 현재 행의 급여값을 기준으로 급여가 -50에서 +150의 범위 내에 포함된 모든 행이 대상이 된다.

![sqld-24-3-5](https://drive.google.com/uc?id=1j3RQS01HHTmQSDq9Aj8pYpoI8h8Wvttq)  

-- 위 SQL 문장은 파티션이 지정되지 않았으므로 모든 건수를 대상으로 -50 ~ +150 기준에 맞는지 검사한다. ORDER BY SAL 로 정렬되어 있기 때문에 비교 연산이 쉬워진다.  

-- ADAMS 의 경우 1100 을 기준으로 1050~1250 의 값을 가진 3명의 데이터 건수를 구할 수 있다.  




### 4. 그룹 내 행 순서 함수  

#### 1) FIRST_VALUE 함수  

FIRST_VALUE 함수를 이용해 파티션별 윈도우에서 가장 먼저 나온 값을 구한다. MIN 함수를 활용하여 같은 결과를 얻을 수도 있다.  
SQL Server 에서는 지원하지 않는다.  

- [예제] 부서별 직원을 연봉이 높은 순서대로 정렬하고, 파티션 내에서 가장 먼저 나온 값을 출력한다.  

```sql
SELECT DEPTNO, ENAME, SAL,
       FIRST_VALUE(ENAME) OVER (PARTITION BY DEPTNO ORDER BY SAL DESC ROWS UNBOUNDED PRECEDING) AS DEPT_RICH
  FROM EMP;
```

-- RANGE UNBOUNDED PRECEDING : 현재 행을 기준으로 파티션 내의 첫 번째 행까지의 범위를 지정한다.  

![sqld-24-4-1-1](https://drive.google.com/uc?id=1fSdWsALVVg-JQRKqISfZSU0jBW2s8-R4)  

-- 실행 결과를 보면 같은 부서 내에 최고 급여를 받는 사람이 두명인 경우, 즉 20-SCOTT 과 20-FORD 중에 어느 사람이 최고 급여자로 선택될지는 위의 SQL 문 만으로는 판단할 수 없다. FIRST_VALUE 는 다른 함수와 달리 공동 등수를 인정하지 않고, 처음 나온 행만을 처리한다.  
위처럼 공동 등수가 있는 경우 의도적으로 세부 항목을 정렬하고 싶다면 별도의 정렬 조건을 가진 INLINE VIEW 를 사용하거나, OVER() 내의 ORDER BY 절에 컬럼을 명시해야 한다.  

- [예제] 위의 SQL 문장에서 FIRST_VALUE 를 처리하기 위해 ORDER BY 정렬 조건을 추가한다.  

```sql
SELECT DEPTNO, ENAME, SAL,
       FIRST_VALUE(ENAME) OVER (PARTITION BY DEPTNO ORDER BY SAL DESC, ENAME ASC ROWS UNBOUNDED PRECEDING)
       AS RICH_EMP
  FROM EMP;
```

![sqld-24-4-1-2](https://drive.google.com/uc?id=1YpTQccTZYdeKZiDbuXUm4z1rbG38GNRK)  

-- 같은 부서 내에 최고 급여를 받는 사람이 두명인 경우를 대비해서 이름을 두 번째 정렬 조건으로 추가한다.  

-- 위의 SQL 문 실행 결과와 비교해보면 부서 20의 최고 급여자가 SCOTT 에서 FORD 로 변경된 것을 확인할 수 있다.  




#### 2) LAST_VALUE 함수  

LAST_VALUE 함수를 이용해 파티션별 윈도우에서 가장 나중에 나온 값을 구한다. MAX 함수를 활용하여 같은 결과를 얻을 수도 있다.  
SQL Server 에서는 지원하지 않는다.  

- [예제] 부서별 직원을 연봉이 높은 순서대로 정렬하고, 파티션 내에서 가장 마지막에 나온 값을 출력한다.  

```sql
SELECT DEPTNO, ENAME, SAL,
       LAST_VALUE(ENAME) OVER (PARTITION BY DEPTNO ORDER BY SAL DESC ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) AS DEPT_POOR
  FROM EMP;
```

-- ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING: 현재 행을 포함해서 파티션 내의 마지막 행까지의 범위를 지정한다.  

![sqld-24-4-2](https://drive.google.com/uc?id=155FD3dY7GCLaHQWjxiVRCecjMjBSM3lg)  




#### 3) LAG 함수  

LAG 함수를 이용해 파티션별 윈도우에서 이전 몇 번째 행의 값을 가져올 수 있다.  
SQL Server 에서는 지원하지 않는다.  

- [예제] 직원을 입사일자 기준으로 정렬하고, 본인보다 입사일자가 한 명 앞선 사원의 급여를 본인의 급여와 함께 출력한다.  

```sql
SELECT ENAME, HIREDATE, SAL,
       LAG(SAL) OVER (ORDER BY HIREDATE) AS PREV_SAL
  FROM EMP
 WHERE JOB = 'SALESMAN';
```

![sqld-24-4-3-1](https://drive.google.com/uc?id=16iFMlGMUFe3Zc2ZqnKoYhqEpu1r0GcK8)  

LAG 함수는 3개의 ARGUMENTS 까지 사용할 수 있는데, 두 번째 인자는 몇 번째 앞의 행을 가져올지 결정하고 (DEFAULT 1), 세 번째 인자는 파티션의 첫 번째 행에 NULL 이 들어올 때 다른 값으로 바꾸어 줄 수 있다. NVL/ISNULL 과 동일하다.  

```sql
SELECT ENAME, HIREDATE, SAL,
       LAG(SAL, 2, 0) OVER (ORDER BY HIREDATE) AS PREV_SAL
  FROM EMP
 WHERE JOB = 'SALESMAN';
```

-- LAG(SAL, 2, 0)의 기능은 두 행 앞의 SALARY를 가져오고, 가져올 값이 없는 경우는 0으로 처리한다.  

![sqld-24-4-3-2](https://drive.google.com/uc?id=1LFFb6UF-_goatTCI6i31Gh0M15-SJBKh)  




#### 4) LEAD 함수  

LEAD 함수를 이용해 파티션별 윈도우에서 이후 몇 번째 행의 값을 가져올 수 있다.  
SQL Server 에서는 지원하지 않는다.  

- [예제] 직원을 입사일자 기준으로 정렬하고, 바로 다음에 입사한 직원의 입사일자를 출력한다.  

```sql
SELECT ENAME, HIREDATE,
       LEAD(HIREDATE, 1) OVER (ORDER BY HIREDATE) AS "NEXTHIRED"
  FROM EMP;
```

![sqld-24-4-4](https://drive.google.com/uc?id=1d0ZXUSIL-z4Uwd-IawrisWpNTgKypVzN)  




### 5. 그룹 내 비율 함수  

#### 1) RATIO_TO_REPORT 함수  

RATIO_TO_REPORT 함수를 이용해 파티션 내 전체 SUM (컬럼) 값에 대한 행별 컬럼 값의 백분율을 소수점으로 구할 수 있다. 결과 값은 0 < X <= 1 의 범위를 가지며, 개별 RATIO 의 합을 구하면 1이 된다.  
SQL Server 에서는 지원하지 않는다.  

- [예제] JOB 이 SALESMAN 인 사원을 대상으로 전체 급여에서 본인이 차지하는 비율을 출력한다.  

```sql
SELECT ENAME, SAL,
       ROUND(RATIO_TO_REPORT(SAL) OVER (), 2) AS R_R
  FROM EMP
 WHERE JOB = 'SALESMAN';
```

![sqld-24-5-1](https://drive.google.com/uc?id=1P0c9rB4UqmxnM6n1KDLDlWefdgSW8DvT)  

-- 실행 결과에서 전체 값은 1650+1250+1250+1500=5600 이 되고, RATIO_TO_REPORT 함수 연산의 분모로 사용된다.  

-- 개별 RATIO 의 전체 합을 구하면 1이 되는 것을 확인할 수 있다. 0.29+0.22+0.22+0.27=1  




#### 2) PERCENT_RANK 함수  

PERCENT_RANK 함수를 이용해 파티션별 윈도우에서 제일 먼저 나오는 것을 0으로, 제일 늦게 나오는 것을 1로 하여, 값이 아닌 행의 순서별 백분율을 구한다. 결과 값은 0 < X <= 1 의 범위를 가진다.  
SQL Server 에서는 지원하지 않는다.  

- [예제] 같은 부서 소속 사원들의 집합에서 본인의 급여가 순서상 몇 번째 위치에 있는지 0과 1 사이의 값으로 출력한다.  

```sql
SELECT DEPTNO, ENAME, SAL,
       PERCENT_RANK() OVER (PARTITION BY DEPTNO ORDER BY SAL DESC) AS P_R
  FROM EMP;
```

![sqld-24-5-2](https://drive.google.com/uc?id=1-9BM5o7n_rXJb8Xmyy6TPMu2fao0CQ2i)  

-- DEPTNO 10 은 3건이므로 2개의 구간이 되어, 0과 1 사이를 2개로 나누면 0, 0.5, 1 이 된다.  

-- DEPTNO 20 은 5건이므로 4개의 구간이 되어, 0과 1 사이를 4개로 나누면 0, 0.25, 0.5, 0.75, 1 이 된다.  

-- DEPTNO 30 은 6건이므로 5개의 구간이 되어, 0과 1 사이를 5개로 나누면 0, 0.2, 0.4, 0.6, 0.8, 1 이 된다.  

-- SCOTT-FORD, WARD-MARTIN 은 ORDER BY SAL DESC 구문에 의해 급여가 같으므로 같은 ORDER 로 취급한다.  




#### 3) CUME_DIST 함수  

CUME_DIST 함수를 이용해 파티션별 윈도우의 전체 건수에서 현재 행보다 작거나 같은 건수에 대한 누적백분율을 구한다. 결과 값은 0 < X <= 1 범위를 가진다.  
SQL Server 에서는 지원하지 않는다.  

- [예제] 같은 부서 소속 사원들의 집합에서 본인의 급여가 누적 순서상 몇 번째 위치에 있는지 0과 1 사이의 값으로 출력한다.  

```sql
SELECT DEPTNO, ENAME, SAL,
       CUME_DIST() OVER (PARTITION BY DEPTNO ORDER BY SAL DESC) AS CUME_DIST
  FROM EMP;
```

![sqld-24-5-3](https://drive.google.com/uc?id=1O5mhm3a_0PuV8HAgp4P0i1canCzb2dFb)  

-- 다른 WINDOW 함수의 경우 동일 순서이면 앞 행의 함수 결과 값을 따르는데, CUME_DIST 의 경우는 동일 순서이면 뒤 행의 함수 결과값을 기준으로 한다.  




#### 4) NTILE 함수  

NTILE 함수를 이용해 파티션별 전체 건수를 ARGUMENT 값으로 N 등분한 결과를 구할 수 있다.  

- [예제] 전체 사원을 급여가 높은 순서로 정렬하고, 급여를 기준으로 4개의 그룹으로 분류한다.  

```sql
SELECT ENAME, SAL,
       NTILE(4) OVER (ORDER BY SAL DESC) AS QUAR_TILE
  FROM EMP;
```

![sqld-24-5-4](https://drive.google.com/uc?id=1OJwI1TTYIUnMcIq2qxSryLJxGYU6s3vC)  
