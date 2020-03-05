---
layout: post
title: "[SQLD] 020# SQL 활용 ⑶ 계층형 질의와 셀프 조인"
tags: [database, SQLD, SQL, HIERARCHY, JOIN]
categories: sqld
---



### Ⅰ. 계층형 질의 (Hierarchical Query)  

테이블에 계층형 데이터가 존재하는 경우 데이터를 조회하기 위해 계층형 질의를 사용한다. 계층형 데이터란 동일 테이블에 계층적으로 상위와 하위 데이터가 포함된 데이터를 말한다. 예를 들어, 부서 테이블에는 상위 부서와 하위 부서 관계가 존재한다.  
엔터티를 순환관계 데이터 모델로 설계할 경우 계층형 데이터가 발생한다. 순환관계 데이터 모델의 예로는 조직, 사원, 메뉴 등이 있다.  

![sqld-2-2-6](https://drive.google.com/uc?id=1k8MUfHHgSRZYhuT_ook2isegOorZZUUI)  

[그림 II-2-6] 은 사원에 대한 순환관계 데이터 모델을 표현한 것이다.  
(2)계층형 구조에서 A 의 하위 사원은 B, C 이고, B 는 하위 사원이 없으며, C의 하위 사원은 D, E 가 있다.  
(3)샘플 데이터는 계층형 구조를 데이터로 표현한 것이다.  


### 1. Oracle 계층형 질의  

![sqld-2-2-7](https://drive.google.com/uc?id=1GbzmHsDJlGYDrJ1UJwma6i7TSG1HFWvq)  

- START WITH 절은 계층 구조 전개의 시작 위치를 지정하는 구문이다. 즉, 루트 데이터를 지정한다. (ACCESS)  

- CONNECT BY 절은 다음에 전개 될 자식 데이터를 지정하는 구문이다. 자식 데이터는 CONNECT BY 절에 주어진 조건을 만족해야 한다. (JOIN)  

- PRIOR : CONNECT BY 절에 사용되며, 현재 읽은 컬럼을 지정한다. 'PRIOR 자식 = 부모' 형태를 사용하면 계층 구조에서 자식 → 부모 방향으로 순방향 전개를 한다. 그리고 'PRIOR 부모 = 자식' 형태를 사용하면 반대로 부모 → 자식 방향으로 전개하는 역방향 전개를 한다.  

- NOCYCLE : 데이터를 전개하면서 이미 나타났던 동일한 데이터가 전개 중에 다시 나타난다면 이것을 사이클 (Cycle) 이 형성되었다 라고 말한다. 사이클이 발생한 데이터는 런타임 오류가 발생한다. 그렇지만 NOCYCLE 을 추가하면 사이클이 발생한 이후의 데이터는 전개하지 않는다.  

- ORDER SIBLINGS BY : 형제 노드 (동일한 LEVEL) 사이에서 정렬을 수행한다.  

- WHERE : 모든 전개를 수행한 후에 지정된 조건을 만족하는 데이터만 추출한다. (필터링)  

Oracle 은 계층형 질의를 사용할 때 다음과 같은 가상 컬럼 (Pseudo Column) 을 제공한다.  

![sqld-ii-2-2](https://drive.google.com/uc?id=1nB0coO0ymOSBK7X30ribvXexBDPdZIXS)  


#### 1) 순방향 전개  

다음은 [그림 II-2-6] 의 (3)샘플 데이터를 계층형 질의 구문을 이용해서 조회한 것이다. 여기서는 결과 데이터를 들여쓰기 하기 위해 LPAD 함수를 사용하였다.  

```sql
 SELECT LEVEL,
        LPAD(' ', 4 * (LEVEL-1)) || 사원 사원,
        관리자,
        CONNECT_BY_ISLEAF ISLEAF
   FROM 사원
  START WITH 관리자 IS NULL
CONNECT BY PRIOR 사원 = 관리자;
```

-- A 는 루트 데이터이기 때문에 레벨이 1이다. A 의 하위 데이터 B, C 는 레벨 2, C 의 하위 데이터 D, E 는 레벨 3이다.  
-- 리프 데이터는 B, D, E 이다.  
-- 관리자 → 사원 방향 전개이기 때문에 순방향 전개이다.  

[그림 II-2-8] 은 계층형 질의에 대한 논리적인 실행 모습이다.  

![sqld-2-2-8](https://drive.google.com/uc?id=1SoUTiL3hJDBatO8lQcixjZugPeRVxpRA)  


#### 2) 역방향 전개  

다음 예제는 사원 'D' 로부터 자신의 상위 관리자를 찾는 역방향 전개이다.  

```sql
 SELECT LEVEL,
        LPAD(' ', 4 * (LEVEL-1)) || 사원 사원,
        관리자,
        CONNECT_BY_ISLEAF ISLEAF
   FROM 사원
  START WITH 사원        = 'D'
CONNECT BY PRIOR 관리자 = 사원;
```

-- 본 예제는 역방향 전개이기 때문에 하위 데이터에서 상위 데이터로 전개된다. 결과를 보면 내용을 제외하고 표시 형태는 순방향 전개와 동일하다.  
-- D 는 루트 데이터이기 때문에 레벨이 1이다. D 의 상위 데이터인 C 는 레벨이 2, C 의 상위 데이터인 A 는 레벨 3 이다.  
-- 리프 데이터는 A 이다.  
-- 루트 및 레벨은 전개되는 방향에 따라 반대가 됨을 알 수 있다.  

[그림 II-2-9] 는 역방향 전개에 대한 계층형 질의에 대한 논리적인 실행 모습이다.  

![sqld-2-2-9](https://drive.google.com/uc?id=121sZpRE5c5hcRwD2LHGD2XjwiT7RJWeR)  


#### 3) 계층형 질의 함수  

Oracle 은 계층형 질의를 사용할 때 사용자 편의성을 제공하기 위해 [표 II-2-3] 과 같은 함수를 제공한다.  

![sqld-ii-2-3](https://drive.google.com/uc?id=1BkBGa81Sg3AIBpqJByyIDXN_nt8MiRLM)  

SYS_CONNECT_BY_PATH, CONNECT_BY_ROOT 를 사용한 예는 다음과 같다.  

```sql
 SELECT CONNECT_BY_ROOT 사원 루트사원,
        SYS_CONNECT_BY_PATH(사원, '/') 경로,
        사원,
        관리자
   FROM 사원
  START WITH 관리자     IS NULL
CONNECT BY PRIOR 사원 = 관리자;
```

-- START WITH 을 통해 추출된 루트 데이터가 1건이기 때문에 루트 사원은 모두 A 이다.  
-- 경로는 루트로부터 현재 데이터까지의 경로를 표시한다. 예를 들어, D 의 경로는 A → C → D 이다.  




### 2. SQL Server 계층형 질의  

SQL Server 2000 버전까지는 계층적 구조를 가진 데이터는 저장 프로시저를 재귀 호출하거나 While 루프 문에서 임시 테이블을 사용하는 등 순수한 쿼리가 아닌 프로그램 방식으로 전개해야 했다. 그러나 SQL Server 2005 버전부터는 하나의 질의로 원하는 결과를 얻을 수 있게 되었다.  

```sql
WITH EMPLOYEES_ANCHOR AS
   ( SELECT EMPLOYEEID,
            LASTNAME,
            FIRSTNAME,
            REPORTSTO,
            0 AS LEVEL
       FROM EMPLOYEES
      WHERE REPORTSTO IS NULL
      UNION ALL    /* 재귀 호출의 시작점 */
     SELECT R.EMPLOYEEID,
            R.LASTNAME,
            R.FIRSTNAME,
            R.REPORTSTO,
            A.LEVEL + 1
       FROM EMPLOYEES_ANCHOR A, EMPLOYEES R
      WHERE A.EMPLOYEEID = R.REPORTSTO )
SELECT LEVEL,
       EMPLOYEEID,
       LASTNAME,
       FIRSTNAME,
       REPORTSTO
  FROM EMPLOYEES_ANCHOR GO ;
```

WITH 절의 CTE 쿼리를 보면, UNION ALL 연산자로 쿼리 두 개를 결합하였다. 둘 중 위에 있는 쿼리를 '앵커 멤버' (Anchor Member) 라고 하고, 아래에 있는 쿼리를 '재귀 멤버' (Recursive Member) 라고 한다.  

아래는 재귀적 쿼리의 처리 과정이다.  

1) CTE 식을 앵커 멤버와 재귀 멤버로 분할한다.  
2) 앵커 멤버를 실행하여 첫 번째 호출 또는 기본 결과 집합 (T0) 을 만든다.  
3) Ti 는 입력으로 사용하고, Ti+1 은 출력으로 사용하여 재귀 멤버를 실행한다.  
4) 빈 집합이 반환될 때 까지 3단계를 반복한다.  
5) 결과 집합을 반환한다. 이것은 T0 ~ Tn 까지의 UNION ALL 이다.  




### Ⅱ. 셀프 조인  

셀프 조인 (Self Join) 이란 동일 테이블 사이의 조인을 말한다. 따라서 FROM 절에 동일 테이블이 두 번 이상 나타난다. 동일 테이블 사이의 조인을 수행하면 테이블과 컬럼 이름이 모두 동일하기 때문에 식별을 위해 반드시 테이블 별칭 (ALIAS) 을 사용해야 한다.  

셀프 조인에 대한 기본적인 사용법은 다음과 같다.  

```sql
SELECT E1.사원,
       E1.관리자,
       E2.관리자 차상위_관리자
  FROM 사원 E1,
       사원 E2
 WHERE E1.관리자 = E2.사원
 ORDER BY E1.사원;
```

![sqld-2-2-11](https://drive.google.com/uc?id=1efFi68b1hJ4JjeFkU8k1uptIO3FfUBDU)  

- 셀프 조인은 동일한 테이블 이지만, [그림 II-2-11] 과 같이 개념적으로는 두 개의 서로 다른 테이블 (사원, 관리자) 을 사용하는 것과 동일하기 때문에 테이블 별칭을 사용한다.  

- 자신과 자신의 직속 관리자는 동일한 행에서 데이터를 구할 수 있으나 차상위 관리자는 바로 구할 수 없다. 차상위 관리자를 구하기 위해서는 자신의 직속 관리자를 기준으로 사원 테이블과 한번 더 셀프 조인을 수행해야 한다.  

- 결과 표시를 위해 SELECT 절에 2개의 '관리자' 컬럼을 사용하였다. 한 명은 자신의 직속 관리자 (E1.관리자) 이고, 다른 한 명은 자신의 차상위 관리자 (E2.관리자) 이다.  

- B 와 C 의 관리자는 A 이고 차상위 관리자는 없다. D 와 E 의 관리자는 C 이고 차상위 관리자는 A 이다.  

- 내부 조인 (INNER JOIN) 을 사용할 경우 자신의 관리자가 존재하지 않는 경우에는 관리자 (E2) 테이블에서 조인할 대상이 존재하지 않기 때문에 해당 데이터는 결과에서 누락된다. 이를 방지하기 위해서는 OUTER JOIN 을 사용해야 한다.  

다음은 OUTER JOIN 을 사용한 예제이다. 관리자가 존재하지 않는 데이터까지 모두 결과에 표시된다.  

```sql
SELECT E1.사원,
       E1.관리자,
       E2.관리자 차상위_관리자
  FROM 사원 E1
  LEFT OUTER JOIN 사원 E2
    ON (E1.관리자 = E2.사원)
 ORDER BY E1.사원;
```
