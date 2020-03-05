---
layout: post
title: "[SQLD] 013# SQL 기본 ⑷ TCL"
tags: [database, SQLD, SQL, TCL]
categories: sqld
---


### 1. 트랜잭션 개요  

트랜잭션은 데이터베이스의 논리적 연산 단위이다. 트랜잭션 (TRANSACTION) 이란, 분리될 수 없는 한 개 이상의 데이터베이스 조작, 즉 분할할 수 없는 최소의 단위이다. TRANSACTION은 ALL OR NOTHING 의 개념인 것이다.  
하나의 논리적인 작업 단위를 구성하는 세부적인 연산들의 집합을 트랜잭션이라 한다. 이런 관점에서 데이터베이스 응용 프로그램은 트랜잭션의 집합으로 정의할 수 있다.  
트랜잭션이 완료된 데이터를 데이터베이서에 반영시키는 것을 COMMIT, 트랜잭션 시작 이전의 상태로 되돌리는 것을 ROLLBACK 이라고 하며, 저장점 (SAVEPOINT) 기능과 함께 3가지 명령어를 TCL (TRANSACTION CONTROL LANGUAGE) 라고 한다. 트랜잭션의 대상이 되는 SQL 문은 UPDATE, INSERT, DELETE 등 데이터를 수정하는 DML 문이다. SELECT 문은 직접적인 트랜잭션의 대상은 아니지만, SELECT FOR UPDATE 등 배타적 LOCK 을 요구하는 SELECT 문장은 트랜잭션의 대상이 될 수 있다.  

![sqld-ii-1-14](https://drive.google.com/uc?id=1WuZFGk6lrspMbTycudAJVq5ngtqQk7OE)  

기본적으로 트랜잭션이 수행하는 동안, 다른 트랜잭션이 동시에 접근하지 못하도록 제한하는 것을 LOCKING 이라고 표현한다. 트랜잭션의 특성, 특히 원자성을 충족시키기 위해 제공하는 기능으로, LOCKING 을 실행한 트랜잭션만 독점적으로 해당 데이터에 접근할 수 있고, 다른 트랜잭션으로부터 간섭이나 방해를 받지 않는 것이 보장된다. LOCKING 이 걸린 데이터는 잠금을 수행한 트랜잭션만이 해제할 수 있다.  




### 2. COMMIT  

처리한 데이터에 대해 문제가 없다고 판단되는 경우, COMMIT 명령어를 통해 트랜잭션을 완료한다.  
COMMIT 이나 ROLLBACK 이전의 데이터 상태는 다음과 같다.  

- 메모리 BUFFER 에만 영향을 받았기때문에 데이터의 변경 이전 상태로 복구가 가능하다.  
- 현재 사용자는 SELECT 문장으로 결과를 확인할 수 있으며, 다른 사용자는 현재 사용자가 수행한 명령의 결과를 볼 수 없다.  
- 변경된 행은 잠금 (LOCKING) 이 설정되어 다른 사용자가 변경할 수 없다.  

COMMIT 명령어는 INSERT, UPDATE, DELETE 문장을 사용한 후, 이 변경 작업이 완료되었음을 데이터베이스에 알려주기 위해 사용한다.  
COMMIT 이후의 데이터 상태는 다음과 같다.  

- 데이터에 대한 변경 사항이 데이터베이스에 반영된다.  
- 이전 데이터는 잃어버리게 된다.  
- 모든 사용자는 결과를 볼 수 있다.  
- 관련된 행에 대한 LOCKING 이 풀리고, 다른 사용자가 행을 조작할 수 있게 된다.  


#### ◇ SQL Server 의 COMMIT  

ORACLE 은 DML 을 실행하는 경우 DBMS 가 트랜잭션을 내부적으로 실행하며 DML 문장 수행 후 사용자가 임의로 COMMIT 혹은 ROLLBACK 을 수행해주어야 트랜잭션이 종료된다. (일부 툴에서는 AUTO COMMIT 을 옵션으로 선택할 수 있다.) 하지만 SQL Server 는 기본적으로 AUTO COMMIT 모드이기 때문에, DML 구문이 성공이면 자동으로 COMMIT 이 되고, 오류가 발생할 경우 자동으로 ROLLBACK 처리가 된다.  
SQL Server 의 트랜잭션의 3가지 방식은 다음과 같다.  

- AUTO COMMIT  
  -- SQL Server 의 기본 방식이며, DML, DDL을 수행할 때 마다 DBMS 가 트랜잭션을 컨트롤 하는 방식이다. 명령어가 성공적으로 수행되면 자동으로 COMMIT 을 수행하고, 오류가 발생하면 자동으로 ROLLBACK 을 수행한다.  

- 암시적 트랜잭션  
  -- ORACLE 과 같은 방식으로 처리된다. 트랜잭션의 시작은 DBMS 가 처리하고, 트랜잭션의 끝은 사용자가 명시적으로 COMMIT 또는 ROLLBACK 으로 처리한다. 인스턴스 단위 또는 세션 단위로 설정할 수 있다. 인스턴스 단위로 설정하려면 서버 속성 창의 연결화면에서 기본연결 옵션 중 암시적 트랜잭션에 체크하면 된다. 세션 단위로 설정하기 위해서는 세션 옵션 중 SET IMPLICIT TRANSCATION ON 을 사용하면 된다.  

- 명시적 트랜잭션  
  -- 트랜잭션의 시작과 끝을 모두 사용자가 명시적으로 지정하는 방식이다. BEGIN TRANSACTION (BEGIN TRAN) 으로 트랜잭션을 시작하고, COMMIT 또는 ROLLBACK 으로 트랜잭션을 종료한다. ROLLBACK 수행 시 최초 BEGIN TRANSACTION 시점까지 모두 ROLLBACK 이 수행된다.  




### 3. ROLLBACK  

ROLLBACK 은 데이터 변경 사항이 COMMIT 이전의 상태로 복구되며, 관련된 행에 대한 ROCKING 이 풀리고 다른 사용자들이 데이터를 변경할 수 있게 된다.  

#### ◇ SQL Server 의 ROLLBACK  

SQL Server AUTO COMMIT 이 기본 방식이므로, 임의적으로 ROLLBACK 을 수행하려면 명시적으로 트랜잭션을 선언해야 한다.  
ROLLBACK 후의 데이터 상태는 다음과 같다.  

- 데이터에 대한 변경 사항이 취소된다.  
- 이전 데이터는 다시 재저장된다.  
- 관련된 행에 대한 LOCKING 이 풀리고, 다른 사용자들이 행을 조작할 수 있게 된다.  

COMMIT 과 ROLLBACK 을 사용함으로써 다음과 같은 효과를 볼 수 있다.  

- 데이터 무결성 보장
- 영구적인 변경을 하기 전 데이터의 변경사항 확인 가능  
- 논리적으로 연관된 작업을 그룹핑하여 처리 가능  




### 4. SAVEPOINT  

SAVEPOINT 를 정의하면 ROLLBACK 시 트랜잭션에 포함된 전체 작업을 ROLLBACK 하는 것이 아니라, 현 시점에서 SAVEPOINT 까지 트랜잭션의 일부만 롤백할 수 있다. 따라서 복잡한 대규모 트랜잭션에서 에러가 발생했을 때 SAVEPOINT 까지의 트랜잭션만 롤백하고, 실패한 부분에 대해서만 다시 실행할 수 있다. (일부 툴에서는 지원하지 않을 수 있다.) 복수의 저장점을 정의할 수 있으며, 동일한 이름으로 정의한 경우 나중에 정의한 SAVEPOINT 만 유효하다.  

```sql
/* 1. 저장점 정의 */
-- ORACLE
SAVEPOINT 저장점명;

-- SQL Server
SAVE TRAN 저장점명;

/* 2. 저장점까지 되돌리기 */
-- ORACLE
ROLLBACK TO 저장점명;

-- SQL Server
ROLLBACK TRAN 저장점명;
```


![sqld-2-1-11](https://drive.google.com/uc?id=1uBRRVT85ayfodSZ65rEn4-iHLYO6tw3g)  

[그림 II-1-11] 에서 처럼, SAVAPOINT A로 되돌린 수 다시 B와 같이 미래 방향으로 되돌릴 수는 없다. 특정 저장점까지 ROLLBACK 하면 그 저장점 이후에 설정한 내용은 무효가 되기 때문이다. 즉, 'ROLLBACK TO A' 를 실행한 시점에서 SAVEPOINT B 는 존재하지 않는다.  
저장점 없이 ROLLBACK 을 실행한 경우, 반영되지 않은 모든 변경 사항을 취소하고 트랜잭션 시작 위치로 되돌아간다.




### ◇ 정리  

- 데이터의 변경을 발생시키는 작업 수행 시 해당 데이터의 무결성을 보장하는 것이 COMMIT 과 ROLLBACK 의 목적이다. SAVEPOINT 는 데이터 변경을 사전에 지정한 저장점까지만 롤백하라는 의미이다.  

- ORACLE 의 트랜잭션은 SQL 문장을 실행하면 자동으로 시작되고, COMMIT 또는 ROLLBACK 을 실행한 시점에 종료된다. 단, 다음의 경우에는 COMMIT 과 ROLLBACK 을 실행하지 않아도 자동으로 트랜잭션이 종료된다.  
    -- CREATE, ALTER, DROP, RENAME, TRUNCATE TABLE 등 DDL 문장을 실행하면 그 전후 시점에 자동으로 커밋된다. 즉, DML 문장 이후 COMMIT 없이 DDL 문장이 실행되면 DDL 수행 전에 자동으로 COMMIT 된다.  
    -- 데이터베이스를 정상적으로 접속을 종료하면 자동으로 트랜잭션이 커밋된다.  
    -- 애플리케이션의 이상 종료로 데이터베이스와 접속이 단절되었을 때는 트랜잭션이 자동으로 롤백된다.  

SQL Server 의 트랜잭션은 DBMS 가 트랜잭션을 컨트롤 하는 방식인 AUTO COMMIT 이 기본 방식이다. 그러나 애플리케이션의 이상 종료로 데이터베이스와 접속이 단절된 경우는 트랜잭션이 자동으로 롤백된다.  
