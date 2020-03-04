---
layout: post
title: "[MySQL] 004# DDL - 테이블 생성/수정/삭제"
tags: [database, MySQL, DDL]
categories: mysql
---

> [[부스트코스] 웹 프로그래밍](https://www.edwith.org/boostcourse-web/joinLectures/12943) 강의를 수강한 학습 내용입니다.  
> -- 출처 : [웹 프로그래밍 > 2.3) DDL(create, drop)](https://www.edwith.org/boostcourse-web/lecture/16722/)

### 1. 테이블 생성 (CREATE)  

```sql
CREATE TABLE 테이블명(
  필드명1 타입 [NULL | NOT NULL][DEFAULT ][AUTO_INCREMENT],
  필드명2 타입 [NULL | NOT NULL][DEFAULT ][AUTO_INCREMENT],
  필드명3 타입 [NULL | NOT NULL][DEFAULT ][AUTO_INCREMENT],
  ...
  PRIMARY KEY(필드명)
);
```

- NULL/NOT NULL : 속성값의 빈 값 허용 여부를 설정  
- DEFAULT : 값을 입력하지 않은 속성의 기본값을 지정  
- AUTO_INCREMENT : 입력하지 않고 자동으로 1씩 증가하는 번호


#### ◇ 예제  

-- 테이블명 EMPLOYEE2 를 아래와 같이 생성한다.  

![mysql_create_001](https://cphinf.pstatic.net/mooc/20180131_144/1517387104021xnStV_PNG/2_8_3___.png?type=w760)  

```sql
CREATE TABLE EMPLOYEE2(   
  empno      INTEGER        NOT NULL    PRIMARY KEY,  
  name       VARCHAR(10),   
  job        VARCHAR(9),   
  boss       INTEGER,   
  hiredate   VARCHAR(12),   
  salary     DECIMAL(7, 2),   
  comm       DECIMAL(7, 2),   
  deptno     INTEGER
);
```

-- 생성한 테이블 구조를 확인한다.  

```sql
DESC EMPLOYEE2;
```



### 2. 테이블 수정 (ALTER)  

#### 1) 컬럼 추가  

```sql
ALTER TABLE 테이블명
  ADD 필드명 타입 [NULL | NOT NULL][DEFAULT ][AUTO_INCREMENT];
```

#### ◇ 예제  

-- EMPLOYEE2 테이블에 생일(birthdate) 컬럼을 VARCHAR(12) 형식으로 추가한다.  

```sql
ALTER TABLE EMPLOYEE2
  ADD birthdate VARCHAR(12);
```

![mysql_alter_001](https://cphinf.pstatic.net/mooc/20180131_255/15173873316052tWyD_PNG/2_8_3___%28%29.png?type=w760)  


#### 2) 컬럼 삭제  

```sql
ALTER TABLE 테이블명
 DROP 필드명;
```

#### ◇ 예제  

-- EMPLOYE2 테이블에 생일(birthdate) 컬럼을 삭제한다.  

```sql
ALTER TABLE EMPLOYEE2
 DROP birthdate;
```

![mysql_alter_002](https://cphinf.pstatic.net/mooc/20180131_264/15173874516941y662_PNG/2_8_3___%28%29.png?type=w760)  


#### 3) 컬럼 수정  

```sql
ALTER TABLE 테이블명
  CHANGE 필드명 새필드명 타입 [NULL | NOT NULL][DEFAULT ][AUTO_INCREMENT];
```

#### ◇ 예제  

-- EMPLOYEE2 테이블의 부서번호(deptno) 컬럼명을 dept_no 로 수정한다.  

```sql
ALTER TABLE EMPLOYEE2
  CHANGE deptno dept_no INT(11);
```

![mysql_alter_003](https://cphinf.pstatic.net/mooc/20180131_244/15173875762404JQ0U_PNG/2_8_3___%28%29.png?type=w760)  

#### 4) 테이블명 수정  

```sql
ALTER TABLE 테이블명 RENAME 변경이름;
```

#### ◇ 예제  

-- EMPLOYEE2 테이블의 이름을 EMPLOYEE3 으로 변경한다.  

```sql
ALTER TABLE EMPLOYEE2 RENAME EMPLOYEE3;
```

![mysql_alter_004](https://cphinf.pstatic.net/mooc/20180131_40/1517387645188A8ER4_PNG/2_8_3____.png?type=w760)  



### 3. 테이블 삭제 (DROP)  

```sql
DROP TABLE 테이블명;
```

- 제약조건이 있는 경우 테이블이 삭제되지 않을 수 있다.  
-- 예를 들어, FOREIGN KEY 관계로 다른 테이블에서 NOT NULL 제약조건으로 설정된 테이블을 삭제할 경우 'a foreign key constraint fails' 오류가 발생한다.  

#### ◇ 예제  

-- EMPLOYEE2 테이블을 삭제한다.  

```sql
DROP TABLE EMPLOYEE2;
```

![mysql_drop_001](https://cphinf.pstatic.net/mooc/20180131_181/15173877575931jc56_PNG/2_8_3___.png?type=w760)  
