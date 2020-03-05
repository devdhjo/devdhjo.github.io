---
layout: post
title: "[SQLD] 012# SQL 기본 ⑶ DML"
tags: [database, SQLD, SQL, DML]
categories: sqld
---


### 1. INSERT  

테이블에 데이터를 입력하는 유형은 두 가지 유형이 있으며, 한 번에 한 건만 입력된다.  

```sql
INSERT INTO 테이블명 (COLUMN_LIST) VALUES (VALUE_LIST);

INSERT INTO 테이블명 VALUES (전체 컬럼의 VALUE_LIST);
```

해당 컬럼명과 입력되어야 하는 값을 서로 1:1로 매핑하여 입력한다. 문자형 데이터는 「'」 (SINGLE QUOTATION) 을 붙여 입력하고, 숫자 유형은 붙이지 않고 입력한다.  
첫번째 유형의 INSERT 문에서 정의하지 않은 컬럼에는 NULL 값이 입력된다. 단, PK 나 NOT NULL 로 지정된 컬럼은 NULL 이 허용되지 않는다. 두번째 유형은 컬럼의 순서대로 빠짐없이 모든 데이터를 입력해야 한다.  




### 2. UPDATE  

입력한 정보를 수정해야 하는 경우 UPDATE 문을 이용한다.  

```sql
UPDATE 테이블명 SET 수정할컬럼명 = 수정할값;
```




### 3. DELETE  

테이블의 정보가 불필요한 경우 데이터를 삭제한다.  

```sql
DELETE [FROM] 테이블명 [WHERE 삭제할 데이터의 조건];
```

DDL (CREATE, ALTER, RENAME, DROP) 명령어는 해당 작업이 즉시 완료 (AUTO COMMIT) 되지만, DML (INSERT, UPDATE, DELETE, SELECT) 명령어는 조작하는 테이블을 메모리 버퍼에 올려놓고 작업을 하기 때문에 실제 테이블에 반영되기 위해서는 COMMIT 명령어를 입력하여 TRANSACTION 을 종료해야 한다. 그러나 SQL Server 의 경우는 DML 의 경우도 AUTO COMMIT 으로 처리된다.  
테이블 전체를 삭제하는 경우, 시스템 활용 측면에서 삭제된 데이터를 로그로 저장하는 DELETE TABLE 보다는 시스템 부하가 적은 TRUNCATE TABLR 을 권고한다. 단, TRUNCATE TABLE 의 경우 ROLLBACK 이 불가능하므로 주의해야 한다. 그러나 SQL Server 의 경우 사용자가 임의로 트랜잭션을 시작한 후 TRUNCATE TABLE 을 이용하여 삭제한 데이터를 다시 복구하려면 ROLLBACK 문을 이용하여 되돌릴 수 있다.  




### 4. SELECT  

사용자가 입력한 데이터는 SELECT 문으로 조회할 수 있다.  

```sql
SELECT [COLUMN_LIST] FROM 테이블명;
```

- DISTINCT 옵션  
	-- 테이블의 특정 컬럼의 값을 중복 없이 조회할 수 있다.  

```sql
SELECT DISTINCT 컬럼명 FROM 테이블명;
```

- WILDCARD 사용하기  
	-- 테이블의 모든 컬럼 정보를 조회할 수 있다.  

```sql
SELECT * FROM 테이블명;
```

- ALIAS 부여하기  
	-- 조회된 결과의 컬럼 별명을 지정할 수 있다.  
    - 컬럼명 바로 뒤에 기입한다.  
    - 컬럼명과 ALIAS 사이에 AS 키워드를 사용할 수 있다  
    - ALIAS 가 공백, 특수문자, 대소문자 구분이 포함된 경우 이중 인용부호 (DOUBLE QUOTATION) 를 사용한다.  

```sql
SELECT 컬럼명 [AS] 컬렴별명, ... FROM 테이블명;
```




### 5. 산술 연산자와 합성 연산자  

#### 1) 산술 연산자  

산술연산자는 NUMBER 와 DATE 자료형에 대해 적용되며, 일반적인 사칙연산과 동일하며, 괄호를 이용하여 우선순위를 적용할 수 있다.  

![sqld-ii-1-13](https://drive.google.com/uc?id=1asxyzSWL2RoCUCv8O4KYFbHPtzINiTGi)  


#### 2) 합성 (CONCATENATION) 연산자  

문자와 문자를 연결하는 합성 (CONCATENATION) 연산자를 사용하여 데이터를 출력할 수 있다.  

```sql
-- ORACLE
SELECT 컬럼1 || '문자열' || 컬럼2 ... FROM 테이블명;

-- SQL Server
SELECT 컬럼1 + '문자열' + 컬럼2 ... FROM 테이블명;
```

- 문자와 문자를 연결하는 경우 \|\| (ORACLE) 또는 + (SQL Server) 를 사용한다.  
- 두 벤더 모두 공통적으로 CONCAT (String1, String2) 함수를 사용할 수 있다.  
- 컬럼과 문자 또는 다른 컬럼을 연결한다.  
- 문자 표현식의 결과에 의해 새로운 컬럼을 생성한다.  
