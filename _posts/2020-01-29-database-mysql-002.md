---
layout: post
title: "[MySQL] 002# Database 생성 및 권한부여"
tags: [database, MySQL]
categories: mysql
---

### 1. Database 생성  

#### 1) 콘솔창에서 다음 명령을 실행합니다.  

- MySQL 관리자 계정인 root 로 데이터베이스 관리 시스템에 접속한다는 의미입니다.  

```bash
mysql -uroot -p
```

-- 또는 MySQL Command Line Client 프로그램을 실행합니다.  

#### 2) 설치 시 입력했던 root 의 암호를 입력합니다.  

-- MAC 사용자는 암호가 없기 때문에 그냥 엔터를 입력하시면 됩니다.  

- 접속이 잘 되었다면 'mysql>' 로 시작하는 프롬프트가 보입니다.  

![mysql_createdb_001](https://drive.google.com/uc?id=1kGM8--ePH6ohQgDPNcXB_CXqNY4m3cSL)  

#### 3) 데이터베이스 생성 명령을 실행합니다.  

```bash
mysql> CREATE DATABASE {DB이름};
```

- 저는 TESTDB 라는 이름으로 데이터베이스를 생성하였습니다.  

```sql
mysql> CREATE DATABASE TESTDB;
```


### 2. DATABASE 사용자 생성  

- DATABASE 를 생성했다면, 해당 DATABASE 를 사용하는 계정을 생성해야 합니다.  

#### 1) 사용자 생성 명령을 실행합니다.  

```sql
mysql> CREATE USER '{username}'@'localhost' IDENTIFIED BY '{password}';
mysql> CREATE USER '{username}'@'%' IDENTIFIED BY '{password}';
```

-- @'%' 는 어떤 클라이언트에서든 접근이 가능하다는 의미이고, @'localhost' 는 해당 컴퓨터에서만 접근이 가능하다는 의미입니다.  

예를 들어, TESTUSER 라는 이름과 1234 라는 비밀번호로 계정을 생성한다면 다음과 같이 실행합니다.  

```sql
mysql> CREATE USER 'TESTUSER'@'localhost' IDENTIFIED BY '1234';
```

#### 2) 생성한 계정에 권한을 부여합니다.  

```sql
mysql> GRANT ALL PRIVILEGES ON {database}.* TO '{username}'@'localhost';
mysql> FLUSH PRIVILEGES;
```

-- DB 이름 뒤의 * 는 모든 권한을 의미합니다.  
-- FLUSH PRIVILEGES 는 DBMS 에 적용하라는 의미입니다. 해당 명령은 반드시 실행해주어야 합니다.  

![mysql_createdb_002](https://drive.google.com/uc?id=1jC26Ju26k4dmTqcyCSCCSbI8_zY51UVX)  

### 3. DATABASE 접속  

#### 1) 콘솔창에서 다음 명령을 실행합니다.  

```bash
mysql -h127.0.0.1 -u{username} -p {database}
```

예를 들어, 생성한 계정 'TESTUSER' 을 사용하여 'TESTDB' 라는 Database 에 접속한다면 다음과 같이 실행합니다.  

```bash
mysql -h127.0.0.1 -uTESTUSER -p TESTDB
```

![mysql_createdb_003](https://drive.google.com/uc?id=1BLkst46-7Rk0jnGVilwH1IkFCLdPx2wE)  

다음과 같은 화면이 보이면 접속이 완료된 것입니다.  

#### 2) MySQL 접속 종료  

```sql
mysql> quit
mysql> exit
```

접속을 종료할 때는 quit 또는 exit 라고 입력합니다.  

![mysql_exit](https://cphinf.pstatic.net/mooc/20180131_246/15173642579241u1LW_PNG/2_8_1_Mysql.png?type=w760)  

위와 같이 Bye 라는 메세지가 나오면 연결이 종료된 것입니다.  



### 4. HOW TO USE  

- 쿼리는 대소문자를 구분하지 않습니다.  

#### 1) 현재 날짜 구하기  

```sql
mysql> SELECT CURRENT_DATE;
+--------------+
| CURRENT_DATE |
+--------------+
| 2020-01-29   |
+--------------+
1 row in set (0.01 sec)
```

#### 2) 쿼리를 이용한 계산식  

```sql
mysql> SELECT (4+1)*5;
+---------+
| (4+1)*5 |
+---------+
|      25 |
+---------+
1 row in set (0.01 sec)
```

#### 3) 여러 문장을 세미콜론(;) 으로 구분하여 연속 실행 가능  

```sql
mysql> SELECT CURRENT_DATE; SELECT NOW();
+--------------+
| CURRENT_DATE |
+--------------+
| 2020-01-29   |
+--------------+
1 row in set (0.00 sec)

+---------------------+
| NOW()               |
+---------------------+
| 2020-01-29 15:36:24 |
+---------------------+
1 row in set (0.00 sec)
```

#### 4) 한 문장을 여러 줄로 입력 가능  

세미콜론으로 문장을 구분하기 때문에, 세미콜론이 입력될 때 까지 한 문장으로 실행됩니다.  

```sql
mysql> SELECT
    -> USER(),
    -> CURRENT_DATE;
+--------------------+--------------+
| USER()             | CURRENT_DATE |
+--------------------+--------------+
| TESTUSER@localhost | 2020-01-29   |
+--------------------+--------------+
1 row in set (0.01 sec)
```

#### 5) SQL 입력 도중 취소하기  

'\c' 를 입력하면 SQL 입력이 취소됩니다.  

```sql
mysql> SELECT
    -> USER()
    -> \c
mysql>
```

#### 6) 현재 서버의 데이터베이스 확인하기  

```sql
mysql> SHOW DATABASES;
+-----------------------+
| Database              |
+-----------------------+
| information_schema    |
| testdb                |
+-----------------------+
2 rows in set (0.00 sec)
```

#### 7) 사용중인 데이터베이스 전환하기  

```sql
mysql> USE {database};
```

데이터베이스를 전환하려면 이미 데이터베이스가 존재해야 하며, 현재 접속중인 계정이 해당 데이터베이스를 사용할 수 있는 권한이 있어야 합니다.  

#### 8) 현재 데이터베이스의 테이블 목록 확인하기  

```sql
mysql> SHOW TABLES;

Empty set (0.02 sec)
```

생성된 테이블이 없는 경우 Empty set 메세지를 출력합니다.  

#### 9) 테이블 구조 확인하기  

```sql
mysql> DESCRIBE {tablename};
mysql> DESC {tablename};
```
