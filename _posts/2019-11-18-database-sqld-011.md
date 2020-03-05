---
layout: post
title: "[SQLD] 011# SQL 기본 ⑵ DDL"
tags: [database, SQLD, SQL, DDL]
categories: sqld
---


### 1. 데이터 유형  

데이터 유형은 데이터베이스 테이블에 특정 컬럼에 입력할 수 있는 자료의 유형을 규정하는 기준이다. 선언한 유형이 아닌 다른 데이터가 들어오면 데이터베이스는 에러를 발생시킨다. 또한 데이터 유형과 더불아 지정한 크기 (SIZE) 도 중요한 기능을 제공한다. 선언 당시에 지정한 데이터 크기를 넘어선 자료가 입력되는 상황도 에러를 발생시키는 중요한 요인이 된다.  

데이터베이스에서 사용하는 데이터 유형은 다양한 형태로 제공된다. 벤더별 데이터 유형과 내장형 함수 부분에서 큰 차이가 있다. 숫자 타입을 예로 들어보면 ANSI/ISO 기준에서는 NUMERIC Type 의 하위 개념으로 NUMERIC, DECIMAL, DEC, SMALLINT, INTEGER, INT, BIGINT, FLOAT, REAL, DOUBLE PRECISION 을 소개하고 있다. SQL Server 와 Sybase 는 ANSI/ISO 기준의 하위 개념에 맞추어 작은 정수형, 정수형, 큰 정수형, 실수형 등 여러 숫자 타입을 제공하고 있으며 ORALE 은 숫자형 타입에 대해 NUMBER 한 가지 유형만 지원한다.  

벤더에서 ANSI/ISO 표준을 사용할 때는 기능을 중심으로 구현하므로, 일반적인 표준과 다른 (예를 들면, NUMERIC → NUMBER, WINDOW FUNCTION → ANALYTIC/RANK FUNCTION) 용어를 사용하는 것은 현실적으로 허용된다.  

테이블의 컬럼이 가지고 있는 대표적인 4가지 데이터 유형은 아래와 같다.  

![sqld-ii-1-7](https://drive.google.com/uc?id=1gqDZqlQ-yplWQ5ucPlWXvlfS2s2JbuXY)  

VARCHAR 유형은 가변길이이므로 길이가 다양한 컬럼과, 정의된 길이와 실제 데이터 길이에 차이가 있는 컬럼에 적합하다. 저장 측면에서도 CHAR 유형보다 작은 영역에 저장할 수 있다.  
또한 CHAR 는 문자열을 비교할 때 짧은 쪽의 끝에 공백을 추가 하여 두 개의 데이터가 같은 길이가 되도록 한 후 앞에서부터 한 문자씩 비교하기 때문에 맨 끝 공백만 다른 문자열은 같은 것으로 판단된다. 그에 반해 VARCHAR 유형은 맨 처음부터 한 문자씩 비교하고, 공백도 하나의 문자로 취급하기 때문에 맨 끝 공백이 다르면 다른 문자로 판단한다.  
- 예) CHAR : 'AA' ＝ 'AA ' / VARCHAR : 'AA' ≠ 'AA '  




### 2. CREATE TABLE  

#### 1) 테이블과 컬럼 정의  

테이블에 존재하는 모든 데이터를 고유하게 식별할 수 있으면서 반드시 값이 존재하는 단일 컬럼이나 컬럼의 조합들 (후보키) 중에 하나를 선정하여 기본키 컬럼으로 지정한다. 기본키는 단일 컬럼이 아닌 여러 개의 컬럼으로도 만들어 질 수 있다.  
또한 테이블 간에 정의된 관계는 기본키 (PRIMARY KEY) 와 외부키 (FOREIGN KEY) 를 활용해서 설정하도록 한다.  


#### 2) CREATE TABLE

테이블을 생성하는 구문 형식은 다음과 같다.  

```sql
CREATE TABLE 테이블명
( 컬럼명1 DATATYPE [DEFAULT 형식],
  컬럼명2 DATATYPE [DEFAULT 형식],
  ...
);
```

다음은 테이블 생성 시 주의해야 할 규칙이다.  

- 테이블명은 객체를 의미할 수 있는 적절한 이름을 사용한다. 가능한 단수형을 권고한다.  
- 테이블명은 다른 테이블과 중복되지 않아야 한다.  
- 한 테이블 내에서는 컬럼명이 중복될 수 없다.  
- 테이블 이름을 지정하고 각 컬럼은 괄호 '()'로 묶어 지정한다.  
- 각 컬럼은 콤마 ','로 구분되고, 테이블 생성문의 끝은 항상 세미콜론 ';' 으로 끝난다.  
- 컬럼에 대해서는 다른 테이블까지 고려하여 데이터베이스 내에서 일관성있게 사용하는 것이 좋다. (데이터 표준화 관점)  
- 컬럼 뒤에 데이터 유형은 반드시 지정해야 한다.  
- 테이블명과 컬럼명은 반드시 문자로 시작해야 하고, 벤더별 길이에 대한 한계가 있다.  
- 벤더에서 사전에 정의한 예약어 (Reserved word) 는 쓸 수 없다.  
- A-Z, a-z, 0-9, '\_', '$', '#' 문자만 허용된다.  
- DATETIME 데이터 유형에는 크기를 지정하지 않지만, 문자 데이터 유형은 반드시 가질 수 있는 최대 길이를 표시해야 한다.


#### 3) 제약조건 (CONSTRAINT)  

제약조건이란, 사용자가 원하는 조건의 데이터만 유지하기 위한, 즉 데이터의 무결성을 유지하기 위한 데이터베이스의 보편적인 방법으로, 테이블의 특정 컬럼에 설정하는 제약이다.

![sqld-ii-1-10](https://drive.google.com/uc?id=1ibelnvgd7aKzdfVe9vg2JSDQB-9idRzH)  

- NULL 의 의미  
	-- NULL (ASCII 00) 은 공백 (BLANK, ASCII 32) 이나 숫자 0 (ZERO, ASCII 48) 과는 전혀 다른 값으로, 조건에 맞는 데이터가 없을 때의 공집합과도 다르다. NULL 은 '아직 정의되지 않은 미지의 값' 또는 '현재 데이터를 입력하지 못하는 경우'를 의미한다.  

- DEFAULT 의 의미  
	-- 데이터의 기본값 (DEFAULT) 을 사전에 설정할 수 있다. 데이터 입력 시 명시된 값을 지정하지 않은 경우 NULL 값이 입력되고, DEFAULT 값을 정의했다면 해당 컬럼에 기본 값이 자동으로 입력된다.  


#### 4) 생성된 테이블 구조 확인  
ORACLE : 'DESCRIBE 테이블명;' 또는 'DESC 테이블명;'  
SQL Server : 'sp_help "dbo.테이블명"'  


#### 5) SELECT 문장을 통한 테이블 생성  

기존 테이블을 이용한 CTAS (Create Table ~ As Select ~) 방법을 이용하면 컬럼별 데이터 유형을 재정의하지 않아도 되는 장점이 있다. 그러나 CTAS 사용 시 주의할 점은 기존 테이블의 제약조건 중 NOT NULL 만 새로운 복제 테이블에 적용되고, 기본키, 고유키, 외래키, CHECK 등의 다른 제약 조건은 없어진다는 점이다.  제약조건을 추가하기 위해서는 ALTER TABLE 기능을 사용해야 한다.  
SQL Server 에서는 'Select ~ Into ~' 를 활용하여 위와 같은 결과를 얻을 수 있으며, 컬럼 속성에 Identity 를 사용했다면 그 속성까지 같이 적용이 된다.  




### 3. ALTER TABLE  

#### 1) ADD COLUMN  

다음은 기존 테이블에 필요한 컬럼을 추가하는 명령이다.  

```sql
ALTER TABLE 테이블명 ADD 추가할컬럼명 데이터유형;
```


#### 2) DROP COLUMN  

DROP COLUMN 은 테이블에서 불필요한 컬럼을 삭제하는 명령이다. 데이터 유무에 관계 없이 삭제 가능하며, 한 번에 하나의 컬럼만 삭제된다. 컬럼 삭제 후 테이블에 최소 하나 이상의 컬럼이 존재해야 하며, 한 번 삭제된 컬럼은 복구가 불가능하다.  

```sql
ALTER TABLE 테이블명 DROP COLUMN 삭제할컬럼명;
```


#### 3) MODIFY COLUMN  

테이블에 존재하는 컬럼에 대해 ALTER TABLE 명령으로 컬럼의 테이터  유형, DEFAULT 값, NOT NULL 제약조건에 대해 변경할 수 있다.  

```sql
-- ORACLE
ALTER TABLE 테이블명 MODIFY
( 컬럼명1 데이터유형 [DEFAULT] [NOT NULL],
  컬럼명2 데이터유형 ...
);

-- SQL Server
ALTER TABLE 테이블명 ALTER
( 컬럼명1 데이터유형 [DEFAULT] [NOT NULL],
  컬럼명2 데이터유형 ...
);
```

- 컬럼 변경 시 고려사항  
	-- 해당 컬럼의 크기 증가는 가능하지만 감소는 불가능하다. 기존 데이터 훼손 가능성이 있기 때문이다.  
	-- 해당 컬럼이 NULL 값만 가지고 있거나, 테이블에 아무 행도 없으면 컬럼의 폭을 줄일 수 있다.  
	-- 해당 컬럼이 NULL 값만 가지고 있으면 데이터 유형을 변경할 수 있다.  
	-- 해당 컬럼의 DEFAULT 값을 변경하면, 변경 이후에 발생하는 행 삽입에만 영향을 미친다.  
	-- 해당 컬럼에 NULL 값이 없는 경우 NOT NULL 제약조건을 추가할 수 있다.  

- RENAME COLUMN  
	-- 테이블을 생성하면서 만들었던 컬럼명을 불가피하게 변경해야 하는 경우 사용할 수 있는 명령이다.  

```sql
-- ORACLE
ALTER TABLE 테이블명 RENAME COLUMN 변경전컬럼명 TO 변경후컬럼명;

-- SQL Server
sp_rename 'dbo.테이블명.변경전컬럼명', '변경후컬럼명', 'COLUMN';
```

-- 컬럼명이 변경되면, 해당 컬럼과 관계된 제약조건도 자동으로 변경된다.  
-- ORACLE 등 일부 DBMS 에서만 지원하는 기능이다.  


#### 4) DROP CONSTRAINT  

테이블 생성 시 부여했던 제약조건을 삭제하는 명령어는 다음과 같다.  

```sql
ALTER TABLE 테이블명 DROP CONSTRAINT 제약조건명;
```


#### 5) ADD CONSTRAINT  

테이블 생성 이후 특정 컬럼에 제약조건을 추가하는 명령어는 다음과 같다.  

```sql
ALTER TABLE 테이블명 ADD CONSTRAINT 제약조건명 제약조건 (컬럼명);
```




### 4. RENAME TABLE  

RENAME 명령어를 사용하여 테이블의 이름을 변경할 수 있다.  

```sql
-- ORACLE
RENAME 변경전테이블명 TO 변경후테이블명;

-- SQL Server
sp_rename 변경전테이블명, 변경후테이블명;
```




### 5. DROP TABLE  

불필요한 테이블을 삭제하는 명령어는 다음과 같다.  

```sql
DROP TABLE 테이블명 [CASCADE CONSTRAINT];
```




### 6. TRUNCATE TABLE  

TRUNCATE TABLE 은 테이블 자체가 삭제되는 것이 아니고, 해당 테이블에 들어있던 모든 행이 제거되고, 저장 공간을 재사용 가능하도록 해제한다.  

```sql
TRUNCATE TABLE 테이블명;
```
