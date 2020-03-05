---
layout: post
title: "[SQLD] 018# SQL 활용 ⑴ 표준 조인"
tags: [database, SQLD, SQL, JOIN]
categories: sqld
---



### 1. STANDARD SQL 개요  

연도|내용
:---:|---
 1970년 | Dr. E. F.Codd - 관계형 DBMS (Relational DB) 논문 발표  
 1974년 | IBM SQL 개발  
 1979년 | Oracle 상용 DBMS 발표  
 1980년 | Sybase SQL Server 발표 (이후 Sybase ASE로 개명)  
 1983년 | IBM DB2 발표  
 1986년 | ANSI/ISO SQL 표준 최초 제정 (SQL-86, SQL1)  
 1992년 | ANSI/ISO SQL 표준 개정 (SQL-92, SQL2)  
 1993년 | MS SQL Server 발표 (Windows OS, Sybase Code 활용)  
 1999년 | ANSI/ISO SQL 표준 개정 (SQL-99, SQL3)  
 2003년 | ANSI/ISO SQL 표준 개정 (SQL-2003)  
 2008년 | ANSI/ISO SQL 표준 개정 (SQL-2008)  

현재 우리가 사용하는 관계형 데이터베이스를 접속할 수 있는 언어는 SQL 이다. ANSI/ISO SQL 표준을 통해 STANDARD JOIN 을 포함한 많은 기능이 여러 DBMS 간에 평준화를 이루어 가고 있다. ANSI/ISO 표준 SQL 기능은 다음 내용을 포함한다.  

- STANDARD JOIN 기능 추가 (CROSS, OUTER JOIN 등 새로운 FROM 절 JOIN 기능)  
- SCALAR SUBQUERY, TOP-N QUERY 등 새로운 SUBQUERY 기능  
- ROLLUP, CUBE, GROUPING SETS 등의 새로운 리포팅 기능  
- WINDOW FUNCTION 같은 새로운 개념의 분석 기능  


#### 1) 일반 집합 연산자  

![sqld-2-2-1](https://drive.google.com/uc?id=1C_1AC6_xMra3P4f6VY7EDIvMLWn0dBk1)  

현재 사용하는 SQL 의 많은 기능이 관계형 데이터베이스의 이론을 수립한 E.F.Codd 박사의 논문에 언급이 되어 있다. 논문에 언급된 8가지 관계형 대수는 다시 각각 4개의 일반 집합 연산자와 순수 관계 연산자로 나눌 수 있으며, 관계형 데이터베이스 엔진 및 SQL 의 기반 이론이 되었다.  
일반 집합 연산자를 현재 SQL 과 비교하면 아래와 같이 구현되었다.  

> １. UNION 연산 → UNION 기능  
> ２. INTERSECTION 연산 → INTERSECT 기능  
> ３. DIFFERENCE 연산 → EXCEPT (Oracle 은 MINUS)  
> ４. PRODUCT 연산 → CROSS JOIN 기능  

첫 번째, UNION 연산은 수학적 합집합을 제공하기 위해 공통 교집합의 중복을 사전에 없애기 위한 정렬 작업으로 시스템에 부하가 발생한다. 이후 UNION ALL 기능이 추가되었는데, 특별한 요구 사항이 없다면 공통 집합을 중복으로 보여주기 때문에 정렬 작업이 일어나지 않는 장점을 가진다.  

두 번째, INTERSECTION 은 수학의 교집합으로써 두 집합의 공통 집합을 추출한다.  

세 번째, DIFFERENCE 는 수학의 차집합으로써 첫 번째 집합에서 두 번째 집합과의 공통 집합을 제외한 부분이다. 대다수 벤더는 EXCEPT 를, Oracle 은 MINUS 용어를 사용한다. (SQL 표준에는 EXCEPT 로 표기되어 있으며, 벤더에서 SQL 표준 기능을 구현할 때 다른 용어를 사용하는 것은 현실적으로 허용되고 있다.)  

네 번째, PRODUCT 의 경우는 CROSS (ANSI/ISO 표준) PRODUCT 라고 불리는 곱집합으로, JOIN 조건이 없는 경우 생길 수 있는 모든 데이터의 조합을 말한다. 양 집합의 M*N 건의 데이터 조합이 발생하며, CARTESIAN (수학자 이름) PRODUCT 라고도 표현한다.  


#### 2) 순수 관계 연산자  

![sqld-2-2-2](https://drive.google.com/uc?id=1o0jCCPpKuABDIdKw7E9EipNmkGMmmoSK)  

순수 관계 연산자는 관계형 데이터베이스를 구현하기 위해 새롭게 만들어진 연산자이다. 순수 관계 연산자를 현재 SQL 문장과 비교하면 다음과 같다.  

> ５. SELECT 연산은 WHERE 절로 구현되었다.  
> ６. PROJECT 연산은 SELECT 절로 구현되었다.  
> ７. (NATURAL) JOIN 연산은 다양한 JOIN 기능으로 구현되었다.  
> ８. DIVIDE 연산은 현재 사용되지 않는다.  

다섯 번째, SELECT 연산은 SQL 문장에서 WHERE 절의 조건절 기능으로 구현되었다. (SELECT 연산과 SELECT 절의 의미는 다르다.)  

여섯 번째, PROJECT 연산은 SQL 문장에서는 SELECT 절의 컬럼 선택 기능으로 구현되었다.  

일곱 번째, JOIN 연산은 WHERE 절의 INNER JOIN 조건과 함께 FROM 절의 NATURAL JOIN, INNER JOIN, OUTER JOIN, USING 조건절, ON 조건절 등으로 가장 다양하게 발전하였다.  

여덟 번째, DIVIDE 연산은 나눗셈과 비슷한 개념으로, 왼쪽의 집합을 'XZ' 로 나누었을 때, 즉 'XZ' 를 모두 가지고 있는 'A' 가 답이 되는 기능이다. 현재 사용되지 않는다.  

관계형 데이터베이스는 '요구사항 분석 - 개념적/논리적/물리적 데이터 모델링' 단계를 거치게 되는데, 이 단계에서 엔터티 확정 및 정규화 과정, 그리고 M:N (다대다) 관계를 분해하는 절차를 거치게 된다. 특히 정규화 과정의 경우 데이터 정합성과 데이터 저장 공간의 절약을 위해 엔터티를 최대한 분리하는 작업으로, 일반적으로 3차 정규형이나 보이스코드 정규형까지 진행하게 된다. 이런 정규화를 거치면 하나의 주제에 관련 있는 엔터티가 여러 개로 나누어 지게 되고, 이 엔터티들이 주로 테이블이 되는데 이렇게 흩어진 데이터를 연결해서 원하는 데이터를 가져오는 작업을 JOIN 이라고 한다.  




### 2. FROM 절 JOIN 형태  

ANSI/ISO SQL 에서 표시하는 FROM 절의 JOIN 형태는 다음과 같다.  

- INNER JOIN  
- NATURAL JOIN  
- USING 조건절  
- CROSS JOIN  
- OUTER JOIN  

ANSI/ISO 에서 규정한 JOIN 문법은 WHERE 절을 사용하던 기존 JOIN 방식과 차이가 있다. 사용자는 기존 WHERE 절의 검색 조건과 테이블 간의 JOIN 조건을 구분 없이 사용하던 방식을 그대로 사용하면서, 추가된 선택 기능으로 테이블 간의 JOIN 조건을 명시적으로 정의할 수 있게 되었다.  

INNER JOIN 은 WHERE 절에서부터 사용하던 JOIN 의 DEFAULT 옵션으로, JOIN 조건에서 동일한 값이 있는 행만 반환한다. DEFAULT 옵션이므로 생략이 가능하지만, CROSS JOIN, OUTER JOIN 과는 같이 사용할 수 없다.  

NATURAL JOIN 은 INNER JOIN 의 하위 개념으로, 두 테이블 간의 동일한 이름을 갖는 모든 컬럼에 대해 EQUI(=) JOIN 을 수행한다. NATURAL INNER JOIN 이라고도 표시할 수 있으며, 결과는 NATURAL JOIN 과 같다.  

새로운 SQL JOIN 문장 중에서 가장 중요하게 기억해야 하는 문장은 ON 조건절을 사용하는 경우이다. 과거 WHERE 절에서 JOIN 조건과 데이터 검증 조건이 같이 사용되어 용도가 불분명한 경우가 발생할 수 있었는데, WHERE 절의 JOIN 조건을 FROM 절의 ON 조건절로 분리하여 표시함으로써 사용자가 이해하기 쉽도록 한다.  
ON 조건절의 경우 NATURAL JOIN 처럼 JOIN 조건이 숨어 있지 않고 명시적으로 JOIN 조건을 구분할 수 있으며, NATURAL JOIN 이나 USING 조건절처럼 컬럼명이 동일해야 한다는 제약 없이 JOIN 조건으로 사용할 수 있기 때문에 가장 많이 사용될 것으로 예상된다.  
다만 FROM 절에 테이블이 많이 사용될 경우 다소 복잡하게 보여 가독성이 떨어지는 단점이 있다. 그런 측면에서 SQL Server 의 경우 ON 조건절만 지원하고 NATURAL JOIN 과 USING 조건절을 지원하지 않고 있는 것으로 보인다.  




### 3. INNER JOIN  

INNER JOIN 은 OUTER (외부) JOIN 과 대비되어 내부 JOIN 이라고 하며, JOIN 조건에서 동일한 값이 있는 행만 반환한다. INNER JOIN 표시는 그동안 WHERE 절에서 사용하던 JOIN 조건을 FROM 절에서 정의하겠다는 표시이므로, USING 조건절이나 ON 조건절을 필수적으로 사용해야 한다.  

- WHERE 절 JOIN 조건  

```sql
SELECT EMP.DEPTNO, EMPNO, ENAME, DNAME
  FROM EMP, DEPT
 WHERE EMP.DEPTNO = DEPT.DEPTNO;
```

위 SQL과 아래 SQL은 같은 결과를 얻을 수 있다.  

- FROM 절 JOIN 조건  

```sql
SELECT EMP.DEPTNO, EMPNO, ENAME, DNAME
  FROM EMP
 INNER JOIN DEPT
    ON EMP.DEPTNO = DEPT.DEPTNO;
```

- INNER는 JOIN의 디폴트 옵션으로 아래 SQL문과 같이 생략 가능하다.  

```sql
SELECT EMP.DEPTNO, EMPNO, ENAME, DNAME
  FROM EMP
  JOIN DEPT
    ON EMP.DEPTNO = DEPT.DEPTNO;
```




### 4. NATURAL JOIN  

NATURAL JOIN 은 두 테이블 간의 동일한 이름을 가지는 모든 컬럼에 대해 EQUI(=) JOIN 을 수행한다. NATURAL JOIN 이 명시되면 추가로 USING 조건절, ON 조건절, WHRER 절에서 JOIN 조건을 정의할 수 없으며, SQL Server 에서는 지원하지 않는다.  

- 예제 1  

```sql
SELECT DEPTNO, EMPNO, ENAME, DNAME
  FROM EMP NATURAL JOIN DEPT;
```

위 SQL 은 별도의 JOIN 컬럼을 지정하지 않았지만, 두 개의 테이블에서 DEPTNO 라는 동일한 이름의 컬럼이 존재하기 때문에 JOIN 된 결과를 확인할 수 있다. JOIN 에 사용되는 컬럼들은 동일한 데이터 유형이어야 하며, ALIAS 나 테이블명 같은 접두사를 붙일 수 없다.  

- 예제 2  

```sql
SELECT EMP.DEPTNO, EMPNO, ENAME, DNAME
  FROM EMP NATURAL JOIN DEPT;

-- ERROR: NATURAL JOIN에 사용된 열은 식별자를 가질 수 없음
```

NATURAL JOIN 은 JOIN 이 되는 테이블의 데이터 성격(도메인)과 컬럼명 등이 동일해야 하는 제약조건이 있다. 간혹 모델링 부주의로 인해 동일한 컬럼명이라도 다른 용도의 데이터를 저장하는 경우도 있기 때문에 주의해서 사용해야 한다.  

- 와일드카드를 사용하여 별도의 컬럼 순서 지정 없이 SELECT 한 경우  
	-- NATURAL JOIN : JOIN 의 기준이 되는 컬럼들이 하나의 컬럼으로 처리되어 먼저 출력된다.  
    -- INNER JOIN : 테이블 순서 및 컬럼 순서대로 데이터가 출력된다. JOIN 기준 컬럼은 각각 별개의 컬럼으로 표시한다.  




### 5. USING 조건절  

NATURAL JOIN 은 동일한 모든 컬럼에 대해 JOIN 이 이루어지지만, FROM 절의 USING 조건절을 이용하면 동일한 이름을 가진 컬럼들 중 원하는 컬럼에 대해서만 선택적으로 EQUI JOIN 을 할 수 있다. SQL Server 에서는 지원하지 않는다.  

- 예제 1  

```sql
SELECT * FROM DEPT JOIN DEPT_TEMP USING (DEPTNO);
```

와일드카드를 사용하여 별도의 컬럼 순서를 지정하지 않으면 USING 조건절의 기준이 되는 컬럼이 먼저 출력된다. 동일한 이름의 컬럼을 하나로 처리하여 표시한다.  

- 예제 2  

```sql
-- 잘못된 사례:
SELECT DEPT.DEPTNO,
       DEPT.DNAME,
       DEPT.LOC,
       DEPT_TEMP.DNAME,
       DEPT_TEMP.LOC
  FROM DEPT JOIN DEPT_TEMP
 USING (DEPTNO);

--ERROR: USING 절의 열 부분은 식별자를 가질 수 없음

-- 바른 사례:
SELECT DEPTNO,
       DEPT.DNAME,
       DEPT.LOC,
       DEPT_TEMP.DNAME,
       DEPT_TEMP.LOC
  FROM DEPT JOIN DEPT_TEMP
 USING (DEPTNO);
```

USING 조건절을 이용한 EQUI JOIN 에서도 NATURAL JOIN 과 마찬가지로 JOIN 컬럼에 대해서 ALIAS 또는 테이블 이름과 같은 접두사를 사용할 수 없다.  




### 6. ON 조건절  

JOIN 서술부 (ON 조건절) 와 비 JOIN 서술부 (WHERE 조건절) 를 분리하기 쉽고, 컬럼명이 달라도 JOIN 조건을 사용할 수 있는 장점이 있다.  

```sql
SELECT E.EMPNO, E.ENAME, E.DEPTNO, D.DNAME
  FROM EMP E JOIN DEPT D
    ON (E.DEPTNO = D.DEPTNO);
```

NATURAL JOIN 의 JOIN 조건은 기본적으로 동일한 이름을 가진 모든 컬럼에 대한 동등 조건이지만, 임의의 JOIN 조건을 지정하거나 컬럼명이 다른 컬럼을 JOIN 조건으로 사용하거나 JOIN 컬럼을 명시하기 위해서는 ON 조건절을 사용한다. ON 조건절에 사용된 괄호는 옵션사항이다.  

USING 조건절을 이용한 JOIN 에서는 JOIN 컬럼에 대해 ALIAS 나 테이블명과 같은 접두사를 사용하면 SYNTAX 에러가 발생하지만, ON 조건절에서는 접두사를 사용하여 SELECT 에 사용되는 컬럼을 논리적으로 명확하게 지정해주어야 한다. ON 조건절은 WHERE 절의 JOIN 조건과 같은 기능을 하면서도 명시적으로 JOIN 의 조건을 구분할 수 있으므로 가장 많이 사용될 수 있다. 다만 FROM 절에 테이블이 많이 사용될 경우 다소 복잡하게 보여 가독성으로 떨어지는 단점이 있다.  


#### 1) WHERE 절과의 혼용  

```sql
SELECT E.ENAME, E.DEPTNO, D.DEPTNO, D.DNAME
  FROM EMP E JOIN DEPT D
    ON (E.DEPTNO = D.DEPTNO)
 WHERE E.DEPTNO = 30;
```

ON 조건절과 WHERE 검색 조건은 충돌 없이 사용할 수 있다.  


#### 2) ON 조건절 + 데이터 검증 조건 추가  

ON 조건절에 JOIN 조건 외이도 데이터 검색 조건을 추가할 수 있으나, 검색 조건 목적인 경우는 WHERE 절을 사용하는 것이 좋다. 다만, OUTER JOIN 에서 조인의 대상을 제한하기 위한 목적인 경우에는 ON 절에 표기되어야 한다.  

```sql
SELECT E.ENAME, E.MGR, D.DEPTNO, D.DNAME
  FROM EMP E JOIN DEPT D
    ON (E.DEPTNO = D.DEPTNO AND E.MGR = 7698);
```

위 SQL과 아래 SQL은 같은 결과를 얻을 수 있다.  

```sql
SELECT E.ENAME, E.MGR, D.DEPTNO, D.DNAME
  FROM EMP E JOIN DEPT D
    ON (E.DEPTNO = D.DEPTNO)
 WHERE E.MGR  = 7698;
```


#### 3) ON 조건절 예제  

```sql
SELECT TEAM_NAME, TEAM.STADIUM_ID, STADIUM_NAME
  FROM TEAM JOIN STADIUM
    ON TEAM.STADIUM_ID = STADIUM.STADIUM_ID
 ORDER BY STADIUM_ID;
```

위 SQL은 STADIUM_ID라는 공통된 칼럼이 있기 때문에 아래처럼 USING 조건절로 구현할 수도 있다.  

```sql
SELECT TEAM_NAME, STADIUM_ID, STADIUM_NAME
  FROM TEAM JOIN STADIUM
 USING (STADIUM_ID)
 ORDER BY STADIUM_ID;
```

위 SQL은 고전적인 방식인 WHERE 절의 INNER JOIN으로 구현할 수도 있다.  

```sql
SELECT TEAM_NAME,
       TEAM.STADIUM_ID,
       STADIUM_NAME
  FROM TEAM, STADIUM
 WHERE TEAM.STADIUM_ID = STADIUM.STADIUM_ID
 ORDER BY STADIUM_ID;
```


#### 4) 다중 테이블 JOIN  

```sql
SELECT E.EMPNO, D.DEPTNO, D.DNAME, T.DNAME New_DNAME
  FROM EMP E JOIN DEPT D
    ON (E.DEPTNO = D.DEPTNO)
  JOIN DEPT_TEMP T
    ON (E.DEPTNO = T.DEPTNO);
```

위 SQL은 고전적인 방식인 WHERE 절의 INNER JOIN으로 구현할 수도 있다.  

```sql
SELECT E.EMPNO, D.DEPTNO, D.DNAME, T.DNAME New_DNAME
  FROM EMP E, DEPT D, DEPT_TEMP T
 WHERE E.DEPTNO = D.DEPTNO
   AND E.DEPTNO = T.DEPTNO;
```




### 7. CROSS JOIN  

CROSS JOIN 은 E.F.CODD 박사가 언급한 일반 집합 연산자의 PRODUCT 개념으로, 테이블 간 JOIN 조건이 없는 경우 생길 수 있는 모든 데이터의 조합을 말한다. 두 개의 테이블에 대산 CARTESIAN PRODUCT 또는 CROSS PRODUCT 와 같은 표현으로, 결과는 양 쪽 집합의 M*N 건 데이터 조합이 발생한다.  

```sql
SELECT ENAME, DNAME
  FROM EMP CROSS JOIN DEPT
 ORDER BY ENAME;
```

NATURAL JOIN 의 경우 WHERE 절에서 JOIN 조건을 추가할 수 없지만, CROSS JOIN 의 경우 WHERE 절에 JOIN 조건을 추가할 수 있다. 그러나 이 경우는 CROSS JOIN 이 아니라 INNER JOIN 과 같은 결과를 얻기 때문에 권고하지 않는다.  

```sql
SELECT ENAME, DNAME
  FROM EMP CROSS JOIN DEPT
 WHERE EMP.DEPTNO = DEPT.DEPTNO;
```

위 SQL과 아래 SQL은 같은 결과를 얻을 수 있다.  

```sql
SELECT ENAME, DNAME
  FROM EMP INNER JOIN DEPT
 WHERE EMP.DEPTNO = DEPT.DEPTNO;
```

정상적인 데이터 모델이라면 CROSS PRODUCT 가 필요한 경우는 많지 않지만, 간혹 튜닝이나 리포트 작성을 위해 고의적으로 사용하는 경우가 있을 수 있다. 그리고 데이터 웨어하우스의 개별 DIMENSION (차원) 을 FACT (사실) 컬럼과 JOIN 하기 전에 모든 DIMENSION 의 CROSS PRODUCT 를 먼저 구할 때 유용하게 사용할 수 있다.  




### 8. OUTER JOIN  

INNER (내부) JOIN 과 대비하여 OUTER (외부) JOIN 이라고 불리며, JOIN 조건에서 동일한 값이 없는 행도 반환할 때 사용할 수 있다.  

![sqld-2-2-3](https://drive.google.com/uc?id=1k4fzT0BOuSGVkY2GfcuAXDSaToDnvGg1)  

[그림 II-2-3] 은 TAB1 테이블이 TAB2 테이블을 JOIN 하되, TAB2 의 JOIN 데이터가 있는 경우는 TAB2 의 데이터를 함께 출력하고, 데이터가 없는 경우에도 TAB1 의 모든 데이터를 표시하고 싶은 경우이다. TAB1 의 모든 값에 대해 TAB2 의 데이터가 반드시 존재한다는 보장이 없는 경우 OUTER JOIN 을 사용하여 해결이 가능하다.  

과거 OUTER JOIN 을 위해 Oracle 은 JOIN 컬럼 뒤에 '(+)' 를 표시하였고, Sybase 는 비교연산자의 앞이나 뒤에 '(+)' 를 표시했었는데, JOIN 조건과 WHERE 절 검색 조건의 불명확, IN 이나 OR 연산자 사용 시 에러 발생, '(+)' 표시가 누락된 컬럼 존재 시 OUTER JOIN 오류 발생, FULL OUTER JOIN 미지원 등 불편함이 많았다.  

STANDARD JOIN 을 사용함으로써 OUTER JOIN 의 많은 문제점을 해결할 수 있고, 대부분의 관계형 DBMS 간에 호환성을 확보할 수 있으므로 명시적인 OUTER JOIN 을 사용할 것을 적극적으로 권장한다.  
추가로 OUTER JOIN 역시 JOIN 조건을 FROM 절에서 정의하겠다는 표시이므로, USING 조건절이나 ON 조건절을 필수적으로 사용해야 한다. 그리고 LEFT/RIGHT OUTER JOIN 의 경우에는 기준이 되는 테이블이 조인 수행 시 무조건 드라이빙 테이블이 된다. 옵티마이저는 이 원칙에 위배되는 다른 실행계획을 고려하지 않는다.  


#### 1) LEFT OUTER JOIN  

JOIN 수행 시 먼저 표기된 좌측 테이블에 해당하는 데이터를 먼저 읽은 후, 나중에 표기된 우측 테이블에서 JOIN 대상 데이터를 읽어온다. 즉, 테이블 A 와 B 가 있을 때 A 와 B 를 비교해서 B 의 JOIN 컬럼에서 같은 값이 있을 때 해당 데이터를 가져오고, B 의 JOIN 컬럼에서 같은 값이 없는 경우에는 B 에서 가져오는 컬럼이 NULL 값으로 채워진다. LEFT JOIN 으로 OUTER 키워드를 생략해서 사용할 수 있다.  

```sql
SELECT STADIUM_NAME, STADIUM.STADIUM_ID, SEAT_COUNT, HOMETEAM_ID, TEAM_NAME
  FROM STADIUM
  LEFT OUTER JOIN TEAM
    ON STADIUM.HOMETEAM_ID = TEAM.TEAM_ID
 ORDER BY HOMETEAM_ID;
```

OUTER는 생략 가능한 키워드이므로 아래 SQL은 같은 결과를 얻을 수 있다.  

```sql
SELECT STADIUM_NAME, STADIUM.STADIUM_ID, SEAT_COUNT, HOMETEAM_ID, TEAM_NAME
  FROM STADIUM
  LEFT JOIN TEAM
    ON STADIUM.HOMETEAM_ID = TEAM.TEAM_ID
 ORDER BY HOMETEAM_ID;
```


#### 2) RIGHT OUTER JOIN  

JOIN 수행 시 오른쪽에 표기된 테이블을 기준으로 결과를 생성한다. 즉, 테이블 A 와 B 가 있을 때 A 의 JOIN 컬럼에서 같은 값이 있을 때 해당 데이터를 가져오고, 없는 경우에는 A 테이블의 컬럼들을 NULL 값으로 채운다. RIGHT JOIN 으로 OUTER 키워드를 생략해서 사용할 수 있다.  

```sql
SELECT E.ENAME, D.DEPTNO, D.DNAME
  FROM EMP E
 RIGHT OUTER JOIN DEPT D
    ON E.DEPTNO = D.DEPTNO;
```

OUTER는 생략 가능한 키워드이므로 아래 SQL은 같은 결과를 얻을 수 있다.  

```sql
SELECT E.ENAME, D.DEPTNO, D.DNAME, D.LOC
  FROM EMP E
 RIGHT JOIN DEPT D
    ON E.DEPTNO = D.DEPTNO;
```


#### 3) FULL OUTER JOIN  

JOIN 수행 시 좌측, 우측 테이블의 모든 데이터를 읽어 JOIN 하여 결과를 생성한다. 즉, 테이블 A  와 B 가 있을 때 RIGHT OUTER JOIN 과 LEFT OUTER JOIN 의 결과를 합집합으로 처리한 결과와 동일하다. 단, UNION ALL 이 아닌 UNION 가능과 동일하기 때문에 중복되는 데이터는 삭제한다. FULL JOIN 으로 OUTER 키워드를 생략해서 사용할 수 있다.  

```sql
SELECT *
  FROM DEPT
  FULL OUTER JOIN DEPT_TEMP
    ON DEPT.DEPTNO = DEPT_TEMP.DEPTNO;
```

OUTER는 생략 가능한 키워드이므로 아래 SQL은 같은 결과를 얻을 수 있다.  

```sql
SELECT *
  FROM DEPT
  FULL JOIN DEPT_TEMP
    ON DEPT.DEPTNO = DEPT_TEMP.DEPTNO;
```

위 SQL과 아래 SQL은 같은 결과를 얻을 수 있다.  

```sql
SELECT L.DEPTNO, L.DNAME, L.LOC, R.DEPTNO, R.DNAME, R.LOC
  FROM DEPT L
  LEFT OUTER JOIN DEPT_TEMP R
    ON L.DEPTNO = R.DEPTNO

 UNION

SELECT L.DEPTNO, L.DNAME, L.LOC, R.DEPTNO, R.DNAME, R.LOC
  FROM DEPT L
 RIGHT OUTER JOIN DEPT_TEMP R
    ON L.DEPTNO = R.DEPTNO;
```




### 9. INNER vs OUTER vs CROSS JOIN 비교  

![sqld-2-2-4](https://drive.google.com/uc?id=1WkWix1sRGtw5PIKsfWjj30foGdFinlfG)  

1) INNER JOIN  
  -- 양쪽 테이블에 모두 존재하는 (키 값이 B-B, C-C 인 2건) 데이터가 출력된다.  

2) LEFT OUTER JOIN  
  -- TAB1 을 기준으로 키 값이 B-B, C-C, D-NULL, E-NULL 인 4건이 출력된다.  

3) RIGHT OUTER JOIN  
  -- TAB2 를 기준으로 키 값이 NULL-A, B-B, C-C 인 3건이 출력된다.  

4) FULL OUTER JOIN  
  -- 양쪽 테이블을 기준으로 키 값이 NULL-A, B-B, C-C, D-NULL, E-NULL 인 5건이 출력된다.  

5) CROSS JOIN (CARTESIAN PRODUCT)  
  -- JOIN 가능한 모든 경우의 수를 표시하지만, OUTER JOIN 은 제외한다.  
  -- 양쪽 테이블 TAB1 과 TAB2 의 데이터를 곱한 개수인 4*3=12 건이 추출된다.  
  (키 값이 B-A, B-B, B-C, C-A, C-B, C-C, D-A, D-B, D-C, E-A, E-B, E-C 인 12건이 출력된다.)  
