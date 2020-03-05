---
layout: post
title: "[SQLD] 022# SQL 활용 ⑸ 그룹 함수"
tags: [database, SQLD, SQL, ROLLUP, CUBE, GROUPING SETS]
categories: sqld
---



### 1. 데이터 분석 개요  

ANSI/ISO SQL 표준은 데이터 분석을 위해 다음 세 가지 함수를 정의하고 있다.  

#### 1) AGGREGATE FUNCTION  

GROUP AGGREGATE FUNCTION 이라고도 부르며, GROUP FUNCTION 의 한 부분으로 분류할 수 있다. 집계함수 COUNT, SUM, AVG, MAX, MIN 등이 포함되어 있다.  

#### 2) GROUP FUNCTION  

여러 레벨의 결과가 필요할 때 여러 단계의 SQL 을 UNION, UNION ALL 로 묶은 후 하나의 테이블을 여러 번 읽어 재정렬하는 복잡한 단계를 거쳐야 했다. 그러나 그룹 함수를 사용한다면 하나의 SQL 로 테이블을 한 번만 읽어서 빠르게 원하는 리포트를 작성할 수 있다. 소계/합계를 표시하기 위해 GROUPING 함수와 CASE 함수를 이용하면 쉽게 원하는 포맷의 보고서 작성도 가능하다.  

그룹 함수로는 집계 함수를 제외하고, 소그룹 간의 소계를 계산하는 ROLLUP 함수, GROUP BY 항목들 간의 다차원적인 소계를 계산할 수 있는 CUBE 함수, 특정 항목에 대한 소계를 계산하는 GROUPING SETS 함수가 있다. 함수의 결과에 대한 정렬이 필요한 경우 ORDER BY 절에 정렬 컬럼을 명시해야 한다.  

- ROLLUP 은 GROUP BY 의 확장된 형태로 사용하기가 쉬우며, 병렬로 수행이 가능하기 때문에 매우 효과적이며 시간 및 지역처럼 계층적 분류를 포함하고 있는 데이터의 집계에 적합하도록 되어있다.  

- CUBE 는 결합 가능한 모든 값에 대하여 다차원적인 집계를 생성하게 되므로 ROLLUP 에 비해 다양한 데이터를 얻는 장점이 있는 반면, 시스템에 부하를 많이 주는 단점이 있다.  

- GROUPING SETS 은 원하는 부분의 소계만 손쉽게 추출할 수 있는 장점이 있다.

#### 3) WINDOW FUNCTION  

분석 함수 (ANALYTIC FUNCTION) 나 순위 함수 (RANK FUNCTION) 로도 알려져 있는 윈도우 함수는 데이터웨어하우스에서 발전한 기능이며, 자세한 내용은 다음 절에서 설명한다.  




### 2. ROLLUP 함수  

ROLLUP 에 지정된 Grouping Columns 의 List 는 Subtotal 을 생성하기 위해 사용되며, Grouping Columns 의 수를 N 이라고 했을 때, N+1 레벨의 Subtotal 이 생성된다. 중요한 것은 rollup 의 인수는 계층구조이므로 인수 순서가 바뀌면 수행 결과도 바뀌게 되므로 인수의 순서에 주의해야 한다.  

#### 1) 일반적인 GROUP BY절 사용  

```sql
SELECT X, Y, COUNT(*), SUM(Z)
  FROM A
 GROUP BY X, Y;
```

Oracle 을 포함한 일부 DBMS 의 과거 버전에서는 GROUP BY 절 사용 시 자동으로 정렬을 수행하였으나, 현재 대부분의 DBMS 버전은 집계 기능만 지원하고 있으며 정렬이 필요한 경우에는 ORDER BY 절에 명시적으로 정렬 컬럼을 표기해야 한다.  


#### 2) ROLLUP 함수 사용  

```sql
SELECT DNAME, JOB, COUNT(*), SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY ROLLUP (DNAME, JOB);
```

![sqld-23-2-2](https://drive.google.com/uc?id=1T2o3WK3hzezH9sCYAblpB7JRwn4tGsb0)  


실행 결과 2개의 GROUPING COLUMNS (DNAME, JOB) 에 대하여 다음과 같은 추가 LEVEL 의 집계가 생성된 것을 볼 수 있다.  

- L1 : GROUP BY 수행 시 생성되는 표준 집계  
- L2 : DNAME 에 대한 모든 JOB 의 SUBTOTAL  
- L3 : GRAND TOTAL (마지막 행, 1건)  

추가로 ROLLUP 의 경우 계층 간 집계에 대해서는 LEVEL 별 순서 (L1-L2-L3) 를 정렬하지만, 계층 내 GROUP BY 수행 시 생성되는 표준 집계에는 별도의 정렬을 지원하지 않는다. L1, L2, L3 계층 내 정렬을 위해서는 별도의 ORDER BY 절을 사용해야 한다.  


#### 3) GROUPING 함수 사용  

ROLLUP, CUBE, GROUPING SETS 등 새로운 그룹 함수를 지원하기 위해 GROUPING 함수가 추가되었다.  
ROLLUP 이나 CUBE 에 의한 소계가 계산된 결과에는 GROUPING(EXPR) = 1 이 표시되고, 그 외의 결과에는 GROUPING(EXPR) = 0 이 표시된다.  
GROUPING 함수와 CASE/DECODE 를 이용하여 소계를 나타내는 필드에 원하는 문자열을 지정할 수 있어서 보고서 작성 시 유용하게 사용할 수 있다.  

```sql
SELECT DNAME,
       GROUPING(DNAME),
       JOB,
       GROUPING(JOB),
       COUNT(*),
       SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY ROLLUP (DNAME, JOB);
```

![sqld-23-2-3](https://drive.google.com/uc?id=1NQViXwJmqyn0HeU4fDKQI4XqQRlDG5s5)  

전체 집계를 표시한 레코드에서는 GROUPING 함수가 1을 리턴하고, 그 외의 결과에는 0을 리턴한다.  


#### 4) GROUPING 함수 + CASE 사용  

```sql
SELECT CASE GROUPING(DNAME) WHEN 1
            THEN 'ALL DEPT' ELSE DNAME
       END  AS DNAME,
       CASE GROUPING(JOB)  WHEN 1
            THEN 'ALL JOB' ELSE JOB
       END  AS JOB,
       COUNT(*), SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY ROLLUP (DNAME, JOB);
```

```sql
SELECT DECODE(GROUPING(DNAME),  1, 'ALL DEPT', DNAME)  AS DNAME,
       DECODE(GROUPING(JOB),    1, 'ALL JOB',  JOB)    AS JOB,
       COUNT(*), SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY ROLLUP (DNAME, JOB);
```

![sqld-23-2-4](https://drive.google.com/uc?id=1TBJQNMtouu-iCwFp8qQorVeAXRoihBGi)  

전체 집계를 표시한 레코드에서는 'ALL DEPT', 'ALL JOB' 라는 텍스트를 확인할 수 있다. Oracle 에서는 DECODE 함수를 사용하여 더 간단하게 표현할 수 있다.  


#### 5) ROLLUP 함수 일부 사용  

- GROUP BY ROLLUP (DNAME, JOB) → GROUP BY DNAME, ROLLUP(JOB)  

```sql
SELECT CASE GROUPING(DNAME) WHEN 1
            THEN 'ALL DEPT' ELSE DNAME
       END  AS DNAME,
       CASE GROUPING(JOB)  WHEN 1
            THEN 'ALL JOB' ELSE JOB
       END  AS JOB,
       COUNT(*), SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY DNAME, ROLLUP(JOB);
```

![sqld-23-2-5](https://drive.google.com/uc?id=18xxMTKJH6ufKWZHwOZ4HpT_Znc98DJob)  

ROLLUP 이 JOB 컬럼에만 사용되었기 때문에 DNAME 에 대한 집계는 계산되지 않았다.  


#### 5-1) ROLLUP 함수 컬럼 결합 사용  

```sql
SELECT DNAME, JOB, MGR, SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY ROLLUP (DNAME, (JOB, MGR));
```

![sqld-23-2-5-1](https://drive.google.com/uc?id=1ONOij1FhJXvd31GqDmdStTMGbisjHrm7)  

괄호로 묶인 JOB 과 MGR 을 하나의 집합 컬럼으로 간주하여 집계를 계산한다.  




### 3. CUBE 함수  

ROLLUP 에서는 단지 가능한 Subtotal 만을 생성하였지만, CUBE 는 결합 가능한 모든 값에 대하여 다차원 집계를 생성한다. CUBE 를 사용할 경우에는 내부적으로는 Grouping Columns 의 순서를 바꾸어 또 한번의 Query 를 추가 수행해야 한다. 뿐만 아니라 Grand Total 은 양 쪽의 Query 에서 모두 생성되기 때문에 한 번의 Query 에서는 제거되어야 하므로 ROLLUP 에 비해 시스템의 연산 대상이 많다.  
이처럼 Grouping Columns 이 가질 수 있는 모든 경우에 대하여 Subtotal 을 생성하는 경우에는 CUBE 함수를 사용하는 것이 바람직하지만, ROLLUP 함수에 비해 시스템 부하가 크기 때문에 주의해야 한다.  

CUBE 함수는 표시된 인수들에 대한 계층별 집계를 구할 수 있으며, 이때 표시된 인수들 간에는 계층 구조인 ROLLUP 과는 달리 평등한 관계이므로 인수의 순서가 바뀌는 경우 행간에 정렬 순서는 바뀔 수 있어도 데이터 결과는 같다. 결과에 대한 정렬이 필요한 경우 ORDER BY 절에 명시적으로 컬럼이 표기되어야 한다.  

#### 1) CUBE 함수 사용

```sql
SELECT CASE GROUPING(DNAME) WHEN 1
            THEN 'ALL DEPT' ELSE DNAME
       END  AS DNAME,
       CASE GROUPING(JOB)  WHEN 1
            THEN 'ALL JOB' ELSE JOB
       END  AS JOB,
       COUNT(*), SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY CUBE (DNAME, JOB);
```

![sqld-23-3-1](https://drive.google.com/uc?id=1sxR2I6gsOvfpuNc6G2LNL2Fc_4Yc8X-2)  

CUBE 는 GROUPING COLUMNS 의 수가 N 이라고 가정하면 2의 N 승 LEVEL 의 Subtotal 을 생성하게 된다. 실행 결과에서 CUBE 함수 사용으로 ROLLUP 함수의 결과에 업무별 집계를 추가하여 출력할 수 있는데, ROLLUP 함수에 비해 업무별 집계를 표시한 5건의 레코드가 추가된 것을 확인할 수 있다.  


#### 2) UNION ALL 사용  

UNION ALL 은 Set Operation 내용으로, 여러 SQL 문장을 연결하는 역할을 할 수 있다. 첫번째 SQL 모듈부터 차례대로 결과가 출력되며, 1) 에서의 CUBE SQL 과 결과 데이터는 동일하지만 행의 정렬은 다를 수 있다.  

```sql
SELECT DNAME, JOB, COUNT(*), SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY ROLLUP (DNAME, JOB)
 UNION ALL
SELECT DNAME, 'ALL JOB', COUNT(*), SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY DNAME
 UNION ALL
SELECT 'ALL DEPT', JOB, COUNT(*), SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY JOB
 UNION ALL
SELECT 'ALL DEPT', 'ALL JOB', COUNT(*), SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO;
```

CUBE 함수를 사용하면서 가장 크게 개선되는 부분은 CUBE 사용 전 SQL 에서 EMP, DEPT 테이블을 반복 엑세스하는 부분을 CUBE 사용 SQL 에서는 한 번으로 줄일 수 있는 부분이다. 결과적으로 수행 속도 및 자원 사용률을 개선할 수 있으며, SQL 문장이 짧아지면서 가독성도 높아졌다.  




### 4. GROUPING SETS 함수  

GROUPING SETS 을 이용하여 SQL 문장을 여러 번 반복하지 않아도 다양한 소계 집합을 만들 수 있다. GROUPING SETS 에 표시된 인수들에 대한 개별 집계를 구할 수 있으며, 계층 구조인 ROLLUP 함수와는 달리 인수들은 모두 평등한 관계이므로 순서에 관계없이 결과는 동일하다. 결과에 대한 정렬이 필요한 경우에는 ORDER BY 절에 명시적으로 컬럼을 표기해야 한다.  

#### 1) 일반 그룹함수를 이용한 SQL  

```sql
SELECT DNAME, 'ALL JOB', COUNT(*), SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY DNAME
 UNION ALL
SELECT 'ALL DEPT', JOB, COUNT(*), SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY JOB;
```

![sqld-23-4-1](https://drive.google.com/uc?id=1PrPs8qSWbP07tUVxigvDQwM8-wPLKDpn)  


#### 2) GROUPING SETS 사용 SQL  

```sql
SELECT DECODE(GROUPING(DNAME),  1, 'ALL DEPT', DNAME) AS DNAME,
       DECODE(GROUPING(JOB),    1, 'ALL JOB',  JOB)   AS JOB,
       COUNT(*), SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY GROUPING SETS (DNAME, JOB);
```

![sqld-23-4-2](https://drive.google.com/uc?id=1INZfWvHNaudcn_C44MpalbozjBSAO_2u)  

GROUPING SETS 함수 사용 시 일반 그룹함수 (UNION ALL) 를 사용한 SQL 과 같은 결과를 얻을 수 있으며, 괄호로 묶은 집합 별 집계를 구할 수 있다. (괄호 내 컬럼은 계층구조가 아닌 하나의 데이터로 간주하기 때문에 표기 순서와 관계 없이 동일한 결과를 얻을 수 있다.  


#### 3) 3개의 인수를 이용한 GROUPING SETS  

```sql
SELECT DNAME, JOB, MGR, SUM(SAL)
  FROM EMP, DEPT
 WHERE DEPT.DEPTNO = EMP.DEPTNO
 GROUP BY GROUPING SETS ((DNAME, JOB, MGR), (DNAME, JOB), (JOB, MGR));
```

![sqld-23-4-3](https://drive.google.com/uc?id=1zdqE-aLrXZTdqlKmHgMXw7cVNGtBOVT4)  

실행 결과 첫 번째 10 건의 데이터는 'DNAME+JOB+MGR' 기준의 집계이며, 두 번째 8 건의 데이터는 'JOB+MGR' 기준, 세 번째 9 건의 데이터는 'DNAME+JOB' 기준의 집계이다.  
