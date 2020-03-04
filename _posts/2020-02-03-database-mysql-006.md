---
layout: post
title: "[MySQL] 006# DML ⑵ 데이터 입력/수정/삭제 (INSERT/UPDATE/DELETE)"
tags: [database, MySQL, DML]
categories: mysql
---

> [[부스트코스] 웹 프로그래밍](https://www.edwith.org/boostcourse-web/joinLectures/12943) 강의를 수강한 학습 내용입니다.  
> -- 출처 : [웹 프로그래밍 > 2.2) DML(select, insert, update, delete)](https://www.edwith.org/boostcourse-web/lecture/16721/)



### 1. 데이터 입력 (INSERT)  

```sql
INSERT INTO 테이블명(필드1, 필드2, 필드3, 필드4, … ) 
VALUES ( 필드1의 값, 필드2의 값, 필드3의 값, 필드4의 값, … );

INSERT INTO 테이블명
VALUES ( 필드1의 값, 필드2의 값, 필드3의 값, 필드4의 값, … );
```

- DEFAULT 값이 설정되는 필드는 생략할 수 있다.  
- 필드명을 생략한 경우에는 모든 필드의 값을 반드시 입력해야 한다.  

#### ◇ 예제  
-- ROLE 테이블에 role_id 는 200, description 은 'CEO' 로 한 건의 데이터를 입력한다.  

```sql
INSERT INTO ROLE (role_id, description) VALUES (200, 'CEO');
```

![mysql_insert_001](https://cphinf.pstatic.net/mooc/20180131_67/1517380899399UDtk6_PNG/2_8_2___%28INSERT%29.png?type=w760)  


### 2. 데이터 수정 (UPDATE)  

```sql
UPDATE 테이블명
   SET 필드1=필드1의값, 필드2=필드2의값, 필드3=필드3의값, …
 WHERE 조건식;
```

- 조건식을 통해 특정 row 만 변경할 수 있다.  
- 조건식을 입력하지 않으면 전체 row 에 영향을 줄 수 있다.  

#### ◇ 예제  
-- ROLE 테이블에 role_id 가 200인 row의 description을 'CTO'로 수정한다.  

```sql
UPDATE ROLE
   SET description = 'CTO'
 WHERE role_id = 200;
```

![mysql_update_001](https://cphinf.pstatic.net/mooc/20180131_267/15173811095845ybA5_PNG/2_8_2___%28UPDATE%29.png?type=w760)  


### 3. 데이터 삭제 (DELETE)  

```sql
DELETE FROM 테이블명
 WHERE 조건식;
```

- 조건식을 통해 특정 row 만 삭제할 수 있다.  
- 조건식을 입력하지 않으면 전체 row 가 삭제된다.  

#### ◇ 예제  
-- ROLE 테이블에 role_id 가 200인 row를 삭제한다.  

```sql
DELETE FROM ROLE
 WHERE role_id = 200;
```

![mysql_delete_001](https://cphinf.pstatic.net/mooc/20180131_20/1517381432397wRROo_PNG/2_8_2___%28DELETE%29.png?type=w760)  

