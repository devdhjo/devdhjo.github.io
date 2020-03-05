---
layout: post
title: "[SQLD] 024# SQL 활용 ⑺ DCL"
tags: [database, SQLD, SQL, DCL]
categories: sqld
---



### 1. DCL 개요  

- SQL 문장 분류  
-- DDL (Data Definition Language) : 테이블 생성과 조작  
-- DML (Data Manipulation Language) : 데이터 조작  
-- TCL (Transaction Control Language) : TRANSACTION 제어  
-- DCL (Data Control Language) : 유저 생성 및 권한 제어  




### 2. 유저와 권한  

데이터베이스는 데이터 보호와 보안을 위해 유저와 권한을 관리하고 있다. 예를 들어 Oracle 을 설치하면 기본적으로 제공되는 유저인 SYS, SYSTEM, SCOTT 유저에 대해 [표 II-2-8] 에서 확인할 수 있다.  

![sqld-ii-2-8](https://drive.google.com/uc?id=1u8f7QQpxCM_6Ti72ZcjxQaaQgWoFAZOO)  

Oracle 과 SQL Server 의 사용자에 대한 아키텍처는 다른 면이 많다.  

Oracle 은 유저를 통해 데이터베이스에 접속하는 형태이다. 즉, 아이디와 비밀번호로 인스턴스에 접속하고 그에 해당하는 스키마에 오브젝트 생성 등의 권한을 부여받게 된다.  

SQL Server 는 인스턴스에 접속하기 위해 로그인이라는 것을 생성하게 되며, 인스턴스 내에 존재하는 다수의 데이터베이스에 연결하여 작업하기 위해 유저를 생성한 후 로그인과 유저를 매핑해주어야 한다. 특정 유저는 특정 데이터베이스 내에 특정 스키마에 대한 권한을 부여받을 수 있다.  

SQL Server 로그인은 두 가지 방식으로 가능하다.  

첫 번째, Windows 인증 방식으로 Windows에 로그인한 정보를 가지고 SQL Server 에 접속하는 방식이다. Microsoft Windows 사용자 계정을 통해 연결되면 SQL Server 는 운영체제의 Windows 보안 주체 토큰을 사용하여 계정 이름과 암호가 유효한지 확인한다. 즉, Windows 에서 사용자 ID 를 확인한다. SQL Server 는 암호를 요청하지 않으며 ID 의 유효성을 검사하지 않는다. Windows 인증는 기본 인증 모드이며 SQL Server 인증보다 훨씬 더 안전하다.  
Windows 인증은 Kerberos 보안 프로토콜을 사용하고, 암호 정책을 적용하여 강력한 암호에 대해 적합한 복잡성 수준을 유지하도록 하며, 계정 잠금 및 암호 만료를 지원한다. SQL Server 가 Windows 에서 제공하는 자격 증명을 신뢰하므로 Windows 인증을 사용한 연결을 트러스트 된 연결이라고도 한다.  

두 번째, 혼합 모드 (Windows 인증 또는 SQL 인증) 방식으로 기본적으로 Windows 인증으로도 SQL Server 에 접속 가능하며, Oracle 의 인증과 같은 방식으로 사용자 아이디와 비밀번호로 SQL Server 에 접속하는 방식이다. SQL 인증을 사용할 때는 강력한 암호 (숫자+문자+특수문자 등을 혼합) 를 사용해야 한다.  
예를 들어 [그림 II-1-16] 을 보면 SCOTT 이라는 LOGIN 이름으로 인스턴스 INST1 에 접속하여 미리 매핑되어 있는 SCOTT 이라는 유저를 통해 PRODUCT 라는 스키마에 속해있는 ITEM 이라는 테이블의 데이터에 엑세스 하고있다.  

![sqld-2-2-16](https://drive.google.com/uc?id=1p2LjEc9PXZVuOWMr6srA37yipfSWqTMN)  


#### 1) 유저 생성과 시스템 권한 부여  

사용자가 실행하는 모든 DDL 문장 (CREATE, ALTER, DROP, RENAME 등) 은 그에 해당하는 시스템 권한이 있어야만 문장을 실행할 수 있다.  

#### ◇ Oracle

- [예제] SCOTT 유저로 접속하여 PJS 유저 (패스워드 KOREA7) 를 생성해본다.  

```sql
CONN SCOTT/TIGER
-- 연결되었다.

CREATE USER PJS IDENTIFIED BY KOREA7;
-- ERROR: 권한이 불충분하다
```

현재 SCOTT 유저는 유저 생성 권한이 없기 때문에 오류가 발생한다. Oracle 의 DBA 권한을 가지고 있는 SYSTEM 유저로 접속하면 다른 유저에게 권한을 부여할 수 있다.  

- [예제] SYSTEM 유저로 접속하여 SCOTT 유저에게 권한을 부여한 후 다시 PJS 유저를 생성한다.  

```sql
CONN SYSTEM/MANAGER
-- 연결되었다.

GRANT USER TO SCOTT;
-- 권한이 부여되었다.

CONN SCOTT/TIGER
-- 연결되었다.

CREATE USER PJS IDENTIFIED BY KOREA7;
-- 사용자가 생성되었다.
```

- [예제] 생성된 PJS 유저로 로그인한다.  

```sql
CONN PJS/KOREA7;
-- ERROR: 사용자 PJS는 CREATE SESSION 권한을 가지고 있지 않음; 로그온이 거절되었다.
```

- PJS 유저가 생성되었지만 권한이 없기 때문에 로그인을 하려면 CREATE SESSION 권한을 부여받아야 한다.  

- [예제] PJS 유저가 로그인 할 수 있도록 CREATE SESSION 권한을 부여한다.  

```sql
CONN SCOTT/TIGER
-- 연결되었다.

GRANT CREATE SESSION TO PJS;
-- 권한이 부여되었다.

CONN PJS/KOREA7
-- 연결되었다.
```

- [예제] PJS 유저로 테이블을 생성한다.  

```sql
CREATE TABLE MENU
( MENU_SEQ  NUMBER       NOT NULL,
  TITLE     VARCHAR2(10)
);
-- ERROR: 권한이 불충분하다.
```

- [예제] SYSTEM 유저를 통해 PJS 유저에게 CREATE TABLE 권한을 부여한 후 다시 테이블을 생성한다.  

```sql
CONN SYSTEM/MANAGER
-- 연결되었다.

GRANT CREATE TABLE TO PJS;
-- 권한이 부여되었다.

CONN PJS/KOREA7
-- 연결되었다.

CREATE TABLE MENU
( MENU_SEQ  NUMBER       NOT NULL,
  TITLE     VARCHAR2(10)
);
-- 테이블이 생성되었다.
```


#### ◇ SQL Server

- SQL Server 는 유저를 생성하기 전 먼저 로그인을 생성해야 한다. 로그인을 생성할 수 있는 권한을 가진 로그인은 기본적으로 sa 이다.  

- [예제] sa 로 로그인 한 후 SQL 인증을 사용하는 PJS 로그인 (패스워드 KOREA7) 을 생성한다. 로그인 후 최초 접속할 데이터베이스는 AdventureWorks 로 설정한다.  

```sql
CREATE LOGIN PJS WITH PASSWORD = 'KOREA7', DEFAULT_DATABASE = AdventureWorks
```

- SQL Server 는 데이터베이스마다 유저가 존재한다. 따라서 유저를 생성하기 위해서는 해당 유저가 속할 데이터베이스로 이동한 후 처리해야 한다.  

```sql
USE ADVENTUREWORKS;
GO
CREATE USER PJS FOR LOGIN PJS WITH DEFAULT_SCHEMA = dbo;
```

- [예제] 생성된 PJS 유저로 로그인하여 테이블을 생성한다.  

```sql
CREATE TABLE MENU
( MENU_SEQ  INT         NOT NULL,
  TITLE     VARCHAR(10)
);
-- ERROR: 데이터베이스 'AdventureWorks' 에서 CREATE TABLE 사용 권한이 거부되었다.
```

- [예제] SYSTEM 유저를 통해 PJS 유저에게 CREATE TABLE 권한을 부여한 후 다시 테이블을 생성한다.  

```sql
/* SYSTEM 로그인 */

GRANT CREATE TABLE TO PJS;
-- 권한이 부여되었다.

GRANT Control ON SCHEMA::dbo TO PJS
-- 권한이 부여되었다.

/* PJS 로그인 */

CREATE TABLE MENU
( MENU_SEQ  INT         NOT NULL,
  TITLE     VARCHAR(10)
);
-- 테이블이 생성되었다.
```


#### 2) OBJECT 에 대한 권한 부여  

OBJECT 권한은 특정 OBJECT 인 테이블, 뷰 등에 대한 SELECT, INSERT, DELETE, UPDATE 작업 명령어 권한을 의미한다.  

![sqld-ii-2-9](https://drive.google.com/uc?id=1As4WVji-qrK-mD06PErIaYxCqfd9Gm24)  

테이블과 같은 OBJECT 는 유저가 소유하는 것이 아니고 스키마가 소유하게 되며, 유저는 스키마에 대해 특정한 권한을 가지는 것이다.  

- [예제] SCOTT 유저로 접속하여 PJS 가 생성한 MENU 테이블을 조회한다.  

```sql
/* Oracle */

CONN SCOTT/TIGER
-- 연결되었다.

SELECT * FROM PJS.MENU;
-- ERROR: 테이블 또는 뷰가 존재하지 않는다.  
```

-- Oracle 은 다른 유저가 소유한 객체에 접근하기 위해 객체 앞에 해당 유저의 이름을 붙인다.  

```sql
/* SQL Server */

-- SCOTT 로그인

SELECT * FROM dbo.MENU;
-- ERROR: 개체이름 'dbo.MENU' 이(가) 잘못되었다.  
```

-- SQL Server 는 다른 유저가 소유한 객체에 접근할 때 객체가 속한 스키마 이름을 붙인다.  

- SCOTT 유저는 MENU 테이블을 SELECT 할 수 있는 권한을 부여받지 않았기 때문에 MENU 테이블을 조회할 수 없다.  

- [예제] PJS 유저로 접속하여 SCOTT 유저에게 MENU 테이블 SELECT 권한을 부여한다.  

```sql
/* Oracle */

CONN PJS/KOREA7
-- 연결되었다.

INSERT INTO MENU VALUES (1, '화이팅');
-- 1개 행이 만들어졌다.

COMMIT;
-- 커밋이 완료되었다.

GRANT SELECT ON MENU TO SCOTT;
-- 권한이 부여되었다.
```

```sql
/* SQL Server */

-- PJS 로그인

INSERT INTO MENU VALUES (1, '화이팅');
-- 1개 행이 만들어졌다.

GRANT SELECT ON MENU TO SCOTT;
-- 권한이 부여되었다.
```

- 권한을 부여받은 SCOTT 유저로 접속하면 PJS.MENU 테이블을 조회할 수 있다.  

```sql
SELECT * FROM PJS.MENU;

UPDATE PJS.MENU SET TITLE = '코리아' WHERE MENU_SEQ = 1;
-- ERROR: 개체 'MENU', 데이터베이스 'AdventureWorks', 스키마 'dbo' 에 대한 UPDATE 권한이 거부되었다.
```

- UPDATE, INSERT, DELETE 등의 작업 또한 각각의 권한이 부여되어야 한다.  




### 3. ROLE 을 이용한 권한 부여  

데이터베이스 관리자는 유저가 생성될 때마다 각각의 권한들을 유저에게 부여하고, 관리해야 한다. 이러한 관리를 더욱 편리하게 할 수 있도록 많은 데이터베이스에서는 ROLE 기능을 제공한다. 데이터베이스 관리자는 ROLE 을 생성하고, ROLE 에 각 권한을 부여한 후 ROLE 을 다른 ROLE 이나 유저에게 부여할 수 있다.  

- [예제] LOGIN_TABLE 이라는 ROLE 을 만들고, 해당 ROLE 을 PJS 유저에게 권한을 부여한다. 권한을 취소할 때는 REVOKE 를 사용한다.  

```sql
/* Oracle */

CONN SYSTEM/MANAGER
-- 연결되었다.

REVOKE CREATE SESSION, CREATE TABLE FROM PJS;
-- 권한이 취소되었다.

CREATE ROLE LOGIN_TABLE;
-- 롤이 생성되었다.

GRANT CREATE SESSION, CREATE TABLE TO LOGIN_TABLE;
-- 권한이 부여되었다.

GRANT LOGIN_TABLE TO PJS;
-- 권한이 부여되었다.
```

이와 같이 ROLE 을 사용하면 권한 관리를 빠르고 안전하게 유저를 관리할 수 있다. Oracle 에서는 기본적으로 몇가지 ROLE 을 제공한다. CONNECT 는 CREATE SESSION 과 같은 로그인 권한이 포함되어 있고, RESOURCE 는 CREATE TABLE 과 같은 오브젝트 생성 권한이 포함되어 있다. 일반적으로 유저를 생성할 때 CONNECT 와 RESOURCE ROLE 을 사용하여 기본 권한을 부여한다.  

![sqld-ii-2-11](https://drive.google.com/uc?id=1RtdQuXuVR73p7o-W1fxvMs0aFz4bCWeD)  

- 유저를 삭제하는 명령어를 DROP USER 이고, CASCADE 옵션을 주면 해당 유저가 생성한 오브젝트를 먼저 삭제한 후 유저를 삭제한다.  

- [예제] CASCADE 옵션을 사용하여 PJS 유저를 삭제한 후, 새로운 유저 JISUNG 을 생성 및 기본적인 ROLE 을 부여한다.  

```sql
CONN SYSTEM/MANAGER
-- 연결되었다.

DROP USER PJS CASCADE;
-- 사용자가 삭제되었다. (이 때 PJS 유저가 만든 MENU 테이블도 함께 삭제된다.)

CREATE USER JISUNG IDENTIFIED BY KOREA7;
-- 사용자가 생성되었다.

GRANT CONNECT, RESOURCE TO JISUNG;
-- 권한이 부여되었다.

CONN JISUNG/KOREA7
-- 연결되었다.

CREATE TABLE MENU
( MENU_SEQ  NUMBER       NOT NULL,
  TITLE     VARCHAR2(10)
);
-- 테이블이 생성되었다.
```

SQL Server 에서는 위와 같이 ROLE 을 생성하기 보다는 기본적으로 ROLE 에 멤버로 참여하는 방식으로 사용한다. 특정 로그인이 멤버로 참여할 수 있는 서버 수준 ROLE 은 [표 II-2-12] 와 같다.  

![sqld-ii-2-12](https://drive.google.com/uc?id=1a8U3xkvML4lQ93aA0FXUgLhrFT8rKTFA)  

데이터베이스에 존재하는 유저에 대해서는 아래와 같은 데이터베이스 역할의 멤버로 참여할 수 있다.  

![sqld-ii-2-13](https://drive.google.com/uc?id=1uSCaqbrbZIHp-iDF-p2T9aQsCbLgR9y7)  

SQL Server 에서는 Oracle 과 같이 ROLE 을 사용하는 대신 서버 수준 역할 및 데이터베이스 수준 역할을 이용하여 로그인 및 사용자 권한을 제어한다. 인스턴스 수준의 작업이 필요한 경우 서버 수준 역할을 부여하고, 그보다 작은 개념인 데이터베이스 수준의 권한이 필요한 경우 해당 수준의 역할을 부여하면 된다.  
