---
layout: post
title: "[SQLD] 025# SQL 활용 ⑻ 절차형 SQL"
tags: [database, SQLD, SQL, PROCEDURE, TRIGGER]
categories: sqld
---



### 1. 절차형 SQL 개요  

일반적인 개발 언어처럼 SQL 에도 절차지향적인 프로그램이 가능하도록 DBMS 벤더별로 절차형 SQL 을 제공하고 있다.  

DBMS|절차형 SQL
---|---
Oracle|PL(Procedural Language)/SQL
DB2|SQL/PL
SQL Server|T-SQL

절차형 SQL 을 이용하면 SQL 문의 연속적인 실행이나 조건에 따른 분기처리를 이용하여 특정 기능을 수행하는 저장 모듈을 생성할 수 있다.  




### 2. PL/SQL 개요  

#### 1) PL/SQL 특징  

Oracle 의 PL/SQL 은 Block 구조로 되어있고, Block 내에는 DML 문장과 QUERY 문장, 그리고 절차형 언어 (IF, LOOP) 등을 사용할 수 있으며, 절차적 프로그래밍을 가능하게 하는 트랜잭션 언어이다. 이런 PL/SQL 을 이용하여 다양한 저장 모듈 (Stored Module) 을 개발할 수 있다.  
저장 모듈이란 PL/SQL 문장을 데이터베이스 서버에 저장하여 사용자와 애플리케이션 사이에서 공유할 수 있도록 만든 일종의 SQL 컴포넌트 프로그램이며, 독립적으로 실행되거나 다른 프로그램으로부터 실행될 수 있는 완전한 실행 프로그램이다. Oracle 의 저장 모듈에는 Procedure, User Defined Function, Trigger 가 있다.  

- PL/SQL 의 특징  
-- PL/SQL 은 Block 구조로 되어있어 각 기능별로 모듈화가 가능하다.  
-- 변수, 상수 등을 선언하여 SQL 문장 간의 값을 교환한다.  
-- IF, LOOP 등의 절차형 언어를 사용하여 절차적인 프로그램이 가능하도록 한다.  
-- DBMS 정의 에러나 사용자 정의 에러를 정의하여 사용할 수 있다.  
-- PL/SQL 은 Oracle 에 내장되어 있으므로 Oracle 과 PL/SQL 을 지원하는 어떤 서버로도 프로그램을 옮길 수 있다.  
-- PL/SQL 은 응용 프로그램의 성능을 향상시킨다.  
-- PL/SQL 은 여러 SQL 문장을 Block 으로 묶고 한번에 Block 전부를 서버로 보내기 때문에 통신량을 줄일 수 있다.  

![sqld-2-2-18](https://drive.google.com/uc?id=1QCmlivmVApgCGLhPYzUMvEIyNQj69yUf)  

[그림 II-2-18] 은 PL/SQL Architecture 이다. PL/SQL Block 프로그램을 입력받으면 SQL 문장과 프로그램 문장을 구분하여 처리한다. 즉, 프로그램 문장은 PL/SQL 엔진이 처리하고, SQL 문장은 Oracle 서버의 SQL Statement Executor 가 실행하도록 작업을 분리하여 처리한다.  




#### 2) PL/SQL 구조  

다음은 PL/SQL 의 블록 구조를 표현한 내용이다.  

![sqld-2-2-19](https://drive.google.com/uc?id=1fv10KvPKE5x3duVVPqe3pnbiJeSHn780)  

- DECLARE : BEGIN ~ END 절에서 사용될 변수와 인수에 대한 정의 및 데이터 타입을 선언하는 선언부이다.  

- BEGIN ~ END : 개발자가 처리하고자 하는 SQL 문과 여러가지 비교문, 제어문을 이용하여 필요한 로직을 처리하는 실행부이다.  

- EXCEPTION : BEGIN ~ END 절에서 실행되는 SQL문이 에러가 발생하면 그 에러를 어떻게 처리할 것인지 정의하는 예외 처리부이다.  




#### 3) PL/SQL 기본 문법 (Syntax)  

앞으로 살펴볼 User Defined Function 이나 Trigger 의 생성 방법과 사용 목적은 다르지만 기본적인 문법은 비슷하기 때문에 여기에서는 Stored Procedure 를 통해서 PL/SQL 에 대한 기본적인 문법을 정리한다.  

```sql
CREATE [OR REPLACE] PROCEDURE [Procedure_name]
( arg1 [mode] data_type1,
  arg2 [mode] data_type2, ...
)
IS [AS] ...
BEGIN ...
EXCEPTION ...
END; /
```

- CREATE TABLE 명령어로 데이터베이스 내에 프로시저를 생성할 수 있다. 생성된 프로시저는 데이터베이스 내에 저장된다.   

- 프로시저는 개발자가 자주 실행해야 하는 로직을 절차적인 언어를 이용하여 작성한 프로그램 모듈이기 때문에 필요할 때 호출하여 실행할 수 있다.  

- [OR REPLACE] 절은 데이터베이스 내에 같은 이름의 프로시저가 있는 경우, 기존의 프로시저에 새로운 내용을 덮어쓰기 하겠다는 의미이다.  

- Argument 는 프로시저가 호출될 때 어떤 값이 들어오거나, 혹은 프로시저에서 처리한 결과값을 운영 체제로 리턴시킬 매개 변수를 지정할 때 사용한다.  

- [mode] 에 지정할 수 있는 매개 변수의 유형은 3가지가 있다.  
	-- IN : 운영체제에서 프로시저로 전달 될 변수  
    -- OUT : 프로시저에서 처리된 결과가 운영체제로 전달되는 변수  
    -- INOUT : IN, OUT 두 기능을 동시에 수행한다.  

- 마지막에 있는 '/' 는 데이터베이스에게 프로시저를 컴파일하라는 명령어이다.  

다음은 생성된 프로시저를 삭제하는 명령어이다.  

```sql
DROP PROCEDURE [Procedure_name];
```




### 3. T-SQL 개요  

#### 1) T-SQL 특징  

T-SQL 은 근본적으로 SQL Server 를 제어하기 위한 언어로서, T-SQL 은 엄격히 말하자면 MS 사에서 ANSI/ISO 표준의 SQL 에 약간의 기능을 더 추가하여 보완적으로 만든 것이다.  

T-SQL 의 프로그래밍 기능은 다음과 같다.  

- 변수 선언 기능 @@ 이라는 전역변수(시스템 함수)와 @ 이라는 지역변수가 있다.  

- 지역변수는 사용자가 자신의 연결 시간 동안만 사용하기 위해 만들어지는 변수이며 전역변수는 이미 SQL 서버에 내장된 값이다.  

- 데이터 유형 (Data Type) 을 제공한다. 즉, int, float, varchar 등의 자료형을 의미한다.  

- 연산자 (Operator) 산술연산자 (+, -, \*, /) 비교연산자 (=, <, >, <>) 논리연산자 (AND, OR, NOR) 사용이 가능하다.  

- 흐름 제어 기능 IF-ELSE 와 WHILE, CASE-THEN 사용이 가능하다.  

- 주석 기능 -- (한 줄 주석), /* \*/ (범위 주석) 형태를 사용한다.  




#### 2) T-SQL 구조  

다음은 T-SQL 의 구조를 표현한 내용이다. PL/SQL 과 유사하다.  

![sqld-2-2-20](https://drive.google.com/uc?id=1Cm-I5KlhyfE1AcJicCgQ28g9yf2sByYx)  

- DECLARE : BEGIN ~ END 절에서 사용될 변수와 인수에 대한 정의 및 데이터 타입을 선언하는 선언부이다.  

- BEGIN ~ END : 개발자가 처리하고자 하는 SQL 문과 여러가지 비교문, 제어문을 이용하여 필요한 로직을 처리하는 실행부이다. T-SQL 에서는 BEGIN, END 문을 반드시 사용해야 하는것은 아니지만 블록 단위로 처리하고자 할 때는 반드시 작성해야 한다.  

- ERROR 처리 : BEGIN ~ END 절에서 실행되는 SQL 문이 에러가 발생하면 그 에러를 어떻게 처리할 것인지를 정의하는 예외처리부이다.  




#### 3) T-SQL 기본 문법 (Syntax)  

앞으로 살펴볼 User Defined Function 이나 Trigger 의 생성 방법과 사용 목적은 다르지만 기본적인 문법은 비슷하기 때문에 여기에서는 Stored Procedure 를 통해서 PL/SQL 에 대한 기본적인 문법을 정리한다.  

```sql
CREATE PROCEDURE [schema_name.]Procedure_name
 @param1 data_type1 [mode],
 @param2 data_type2 [mode], ...
WITH AS ...
BEGIN ...
ERROR 처리 ...
END;
```

다음은 생성된 프로시저를 삭제하는 명령어이다.  

```sql
DROP PROCEDURE [schema_name.]Procedure_name;
```

- CREATE TABLE 명령어로 데이터베이스 내에 프로시저를 생성할 수 있다. 생성된 프로시저는 데이터베이스 내에 저장된다.  

- 프로시저는 개발자가 자주 실행해야 하는 로직을 절차적인 언어를 이용하여 작성한 프로그램 모듈이기 때문에 필요할 때 호출하여 실행할 수 있다.  

- 프로시저의 변경이 필요한 경우 Oracle 은 [CREATE OR REPLACE] 와 같이 하나의 구문으로 처리하지만, SQL Server 는 CREATE 구문을 ALTER 구문으로 변경하여야 한다.  

- @parameter 는 프로시저가 호출될 때 어떤 값이 들어오거나, 혹은 프로시저에서 처리한 결과값을 운영 체제로 리턴시킬 매개 변수를 지정할 때 사용한다.  

- [mode] 에 지정할 수 있는 매개 변수 (@parameter) 의 유형은 4가지가 있다.  
	-- VARYING : 결과 집합이 출력 매개변수로 사용되도록 지정한다. CURSOR 매개변수에만 적용된다.  
    -- DEFAULT : 프로시저를 호출할 당시 매개변수가 지정되지 않은 경우 기본값으로 처리한다. 즉, 기본값이 지정되어 있으면 해당 매개변수를 지정하지 않아도 프로시저가 지정된 기본값으로 정상 수행된다.  
    -- OUT, OUTPUT : 프로시저에서 처리된 결과 값을 EXECUTE 문 호출 시 반환한다.  
    -- READONLY : 프로시저 본문 내에서 매개변수를 업데이트 하거나 수정할 수 없음을 나타낸다. 매개변수 유형이 사용자 정의 테이블 형식인 경우 READONLY 를 지정해야 한다. 자주 사용하지는 않는다.  

- WITH 부분에 지정할 수 있는 옵션은 3가지가 있다.  
    -- RECOMPILE : 데이터베이스 엔진에서 현재 프로시저의 계획을 캐시하지 않고 프로시저가 런타임에 컴파일된다. 데이터베이스 엔진에서 저장 프로시저 안에 있는 개별 쿼리에 대한 계획을 삭제하려 할 때 RECOMPILE 쿼리 힌트를 사용한다.  
    -- ENCRYPTION : CREATE PROCEDURE 문의 원본 텍스트가 알아보기 어려운 형식으로 변환된다. 변조된 출력은 SQL Server 의 카탈로그 뷰 어디에서도 직접 표시되지 않는다. 원본을 볼 수 있는 방법이 없기 때문에 반드시 원본은 백업을 해두어야 한다.  
    -- EXECUTE AS : 해당 저장 프로시저를 실행할 보안 컨텍스트를 지정한다.  




### 4. Procedure 의 생성과 활용  

[그림 Ⅱ-2-21]은 앞으로 생성할 Procedure의 기능을 Flow Chart로 나타낸 그림이다.  

![sqld-2-2-21](https://drive.google.com/uc?id=13mB-oteEucfsrY5r1uA2gowsZmTkMYlf)  

- [예제] SCOTT 유저가 소유하고 있는 DEPT 테이블에 새로운 부서를 등록하는 Procedure 를 작성한다.  

![sqld-ii-2-14](https://drive.google.com/uc?id=1P6aGDpYbxPoFrZ7F1B8uLCn0TMxOi_rb)  


#### ◇ Oracle  

```sql
CREATE OR REPLACE Procedure p_DEPT_insert
-- ① DEPT 테이블에 들어갈 칼럼 값(부서코드, 부서명, 위치)을 입력 받는다.
( v_DEPTNO in number,
  v_dname in varchar2,
  v_loc in varchar2,
  v_result out varchar2 )

IS cnt number := 0;

BEGIN
-- ② 입력 받은 부서코드가 존재하는지 확인한다.
  SELECT COUNT(*) INTO CNT
    FROM DEPT WHERE DEPTNO = v_DEPTNO AND ROWNUM = 1;

-- ③ 부서코드가 존재하면 '이미 등록된 부서번호입니다'라는 메시지를 출력 값에 넣는다.
  if   cnt > 0 then v_result := '이미 등록된 부서번호이다';
-- ④ 부서코드가 존재하지 않으면 입력받은 필드 값으로 새로운 부서 레코드를 입력한다.
  else
       INSERT INTO DEPT (DEPTNO, DNAME, LOC)
       VALUES (v_DEPTNO, v_dname, v_loc);
-- ⑤ 새로운 부서가 정상적으로 입력됐을 경우에는 COMMIT 명령어를 통해서 트랜잭션을 종료한다.
       COMMIT;

       v_result := '입력 완료!!';
  end if;

-- ⑥ 에러가 발생하면 모든 트랜잭션을 취소하고 'ERROR 발생'라는 메시지를 출력값에 넣는다.
  EXCEPTION WHEN OTHERS THEN ROLLBACK;
  v_result := 'ERROR 발생';

END; /
```


#### ◇ SQL Server

```sql
CREATE Procedure dbo.p_DEPT_insert

-- ① DEPT 테이블에 들어갈 칼럼 값(부서코드, 부서명, 위치)을 입력 받는다.
@v_DEPTNO int,
@v_dname varchar(30),
@v_loc varchar(30),
@v_result varchar(100) OUTPUT
  AS DECLARE @cnt int
         SET @cnt = 0

BEGIN

-- ② 입력 받은 부서코드가 존재하는지 확인한다.
  SELECT @cnt=COUNT(*)
    FROM DEPT WHERE DEPTNO = @v_DEPTNO

-- ③ 부서코드가 존재하면 '이미 등록된 부서번호입니다'라는 메시지를 출력 값에 넣는다.
  IF @cnt > 0
    BEGIN SET @v_result = '이미 등록된 부서번호이다' RETURN END

-- ④ 부서코드가 존재하지 않으면 입력받은 필드 값으로 새로운 부서 레코드를 입력한다.
  ELSE
    BEGIN
      BEGIN TRAN
	    INSERT INTO DEPT (DEPTNO, DNAME, LOC)
      VALUES (@v_DEPTNO, @v_dname, @v_loc)

-- ⑥ 에러가 발생하면 모든 트랜잭션을 취소하고 'ERROR 발생'라는 메시지를 출력값에 넣는다.
	    IF @@ERROR<>0
	      BEGIN ROLLBACK
            SET @v_result = 'ERROR 발생'
		    RETURN
	      END

-- ⑤ 새로운 부서가 정상적으로 입력됐을 경우에는 COMMIT 명령어를 통해서 트랜잭션을 종료한다.
	    ELSE
	      BEGIN COMMIT
            SET @v_result = '입력 완료!!'
		    RETURN
		  END
    END
END
```

DEPT 테이블은 DEPTNO 컬럼이 PRIMARY KEY 로 설정되어 있으므로, DEPTNO 컬럼에는 유일한 값을 넣어야 한다.  

- 프로시저 작성 시 주의해야 할 문법요소  

⑴ PL/SQL 및 T-SQL 에서는 다양한 변수가 있다. 예제에 나온 cnt 라는 변수를 SCALAR 변수라고 한다. SCALAR 변수는 사용자의 임시 데이터를 하나만 저장할 수 있으며, 거의 모든 데이터 유형을 지정할 수 있다.  

⑵ PL/SQL 에서 사용하는 SELECT 문장은 결과값이 반드시 하나 있어야 한다. 조회 결과가 없거나 하나 이상인 경우 에러를 발생시킨다. T-SQL 에서는 결과 값이 없어도 에러가 발생하지 않는다.  

⑶ T-SQL 을 비롯한 일반적인 대입 연산자는 '=' 를 사용하지만, PL/SQL 에서는 ':=' 를 사용한다.  

⑷ 에러 처리를 담당하는 EXCEPTION 에는 WHEN ~ THEN 절을 사용하여 에러의 종류별로 적절히 처리한다. OTHERS 를 이용하여 모든 에러를 처리할 수 있지만 정확하게 에러를 처리하는 것이 좋다.  




### 5. User Defined Function 의 생성과 활용  

User Defined Function 은 Procedure 처럼 절차형 SQL 을 로직과 함께 데이터베이스 내에 저장해 놓은 명령문의 집합을 의미한다. Function 이 Procedure 와 다른 점은 RETURN 을 사용하여 하나의 값을 반드시 되돌려주어야 한다는 것이다.  




### 6. Trigger 의 생성과 활용  

Trigger 란 특정 테이블에 DML 문이 수행되었을 때, 데이터베이스에서 자동으로 동작하도록 작성된 프로그램이다. 즉, 사용자가 직접 호출하여 사용하는 것이 아니고 데이터베이스에서 자동적으로 수행하게 된다.  
Trigger 는 테이블과 뷰, 데이터베이스 작업을 대상으로 정의할 수 있으며, 전체 트랜잭션 작업에 대해 발생되는 Trigger 와 각 행에 대해서 발생되는 Trigger 가 있다.  


- [예제] Trigger 를 사용하여 주문 정보 (ORDER_LIST) 가 입력될 때마다 집계 테이블 (SALES_PER_DATE) 에 일자별 상품별 판매수량, 판매금액을 집계한 자료를 생성한다.  

#### ◇ Oracle  

```sql
/* ① Trigger를 선언한다.
CREATE OR REPLACE Trigger SUMMARY_SALES : Trigger 선언문
AFTER INSERT : 레코드가 입력이 된 후 Trigger 발생
ON ORDER_LIST : ORDER_LIST 테이블에 Trigger 설정
FOR EACH ROW : 각 ROW마다 Trigger 적용
*/
CREATE OR REPLACE TRIGGER SUMMARY_SALES
 AFTER INSERT ON ORDER_LIST FOR EACH ROW

/*② o_date(주문일자), o_prod(주문상품) 값을 저장할 변수를 선언하고, 신규로 입력된 데이터를 저장한다.
:NEW는 신규 입력된 레코드의 정보를 가지고 있는 구조체
:OLD는 수정, 삭제되기 전의 레코드를 가지고 있는 구조체
*/
DECLARE
  o_date ORDER_LIST.order_date%TYPE;
  o_prod ORDER_LIST.product%TYPE;
BEGIN
  o_date := :NEW.order_date;
  o_prod := :NEW.product;

-- ③ 먼저 입력된 주문 내역의 주문 일자와 주문 상품을 기준으로 SALES_PER_DATE 테이블에 업데이트한다.
  UPDATE SALES_PER_DATE
     SET qty = qty + :NEW.qty,
         amount = amount + :NEW.amount
   WHERE sale_date = o_date
     AND product = o_prod;

-- ④ 처리 결과가 SQL%NOTFOUND이면 해당 주문 일자의 주문 상품 실적이 존재하지 않으며, SALES_ PER_DATE 테이블에 새로운 집계 데이터를 입력한다.
  if SQL%NOTFOUND then
    INSERT INTO SALES_PER_DATE
    VALUES (o_date, o_prod, :NEW.qty, :NEW.amount);
  end if;
END; /
```


![sqld-ii-2-17](https://drive.google.com/uc?id=1XOw3lwHZztDalf9iYaHEw7Q0bIum7rKn)  

#### ◇ SQL Server  

```sql
-- SQL Server
/* ① Trigger를 선언한다.
CREATE Trigger SUMMARY_SALES : Trigger 선언문
ON ORDER_LIST : ORDER_LIST 테이블에 Trigger 설정
AFTER INSERT : 레코드가 입력이 된 후 Trigger 발생
*/
CREATE TRIGGER dbo.SUMMARY_SALES
    ON ORDER_LIST AFTER INSERT AS

/* ② o_date(주문일자), o_prod(주문상품), qty(수량), amount(금액) 값을 저장할 변수를 선언하고, 신규로 입력된 데이터를 저장한다.
inserted는 신규 입력된 레코드의 정보를 가지고 있는 구조체
deleted는 수정, 삭제되기 전의 레코드를 가지고 있는 구조체
*/
DECLARE
  @o_date DATETIME,
  @o_prod INT,
  @qty int,
  @amount int
BEGIN
  SELECT @o_date=order_date, @o_prod=product, @qty=qty, @amount=amount
    FROM inserted

-- ③ 먼저 입력된 주문 내역의 주문 일자와 주문 상품을 기준으로 SALES_PER_DATE 테이블에 업데이트한다.
  UPDATE SALES_PER_DATE
     SET qty = qty + @qty,
         amount = amount + @amount
   WHERE sale_date = @o_date
     AND product = @o_prod;

-- ④ 처리 결과가 0건이면 해당 주문 일자의 주문 상품 실적이 존재하지 않으며, SALES_PER_DATE 테이블에 새로운 집계 데이터를 입력한다.
  IF @@ROWCOUNT=0
    INSERT INTO SALES_PER_DATE
    VALUES (@o_date, @o_prod, @qty, @amount)
END
```


![sqld-ii-2-18](https://drive.google.com/uc?id=1J8zDAd24UTZuWvkCIQZDTtfZMxy_xTgO)  


- ROLLBACK 을 하면 Trigger 로 입력된 정보까지 하나의 트랜잭션으로 인식하여 두 테이블에서 모두 입력이 취소된다.  

- Trigger 는 데이터베이스 보안의 적용, 유효하지 않은 트랜잭션의 예방, 업무 규칙 자동 적용 제공 등에 사용될 수 있다.   




### 7. 프로시저와 트리거의 차이점  

- 프로시저는 BEGIN ~ END 절 내에 COMMIT, ROLLBACK 과 같은 트랜잭션 종료 명령어를 사용할 수 있지만, 데이터베이스 트리거는 사용할 수 없다.  


![sqld-ii-2-19](https://drive.google.com/uc?id=1WHLpBsaqAhJ8pvEbnuOZJL6_9DvadgtO)  
