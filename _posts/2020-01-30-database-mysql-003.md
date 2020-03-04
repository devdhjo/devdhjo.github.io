---
layout: post
title: "[MySQL] 003# MySQL 데이터 타입 (자료형) 정리"
tags: [database, MySQL, DataType, DDL]
categories: mysql
---

> MySQL 8.0 기준으로 정리 된 데이터 자료형입니다.  
> -- 출처 : [MySQL 8.0 Reference Manual > Data Types](https://dev.mysql.com/doc/refman/8.0/en/data-types.html)

### 1. 숫자형  

#### 1) 정수 유형  

:---:|:---
BIT(M)|비트값 타입. 즉, 0과 1로 구성되는 binary 값을 저장한다.<BR>(M : 1~64, 생략 시 기본값은 1 로 설정)  
BOOL|0은 false, 0이 아닌 값은 true 로 간주하는 논리형 데이터<BR>ENUM(Y,N) 또는 TINYINT(1) 로 대체하여 사용하는 것을 권장  
TINYINT(M)|부호 있는 수는 -128 ~ 127<BR>부호 없는 수는 0 ~ 225 까지 표현 (1바이트)  
SMALLINT(M)|부호 있는 수는 -32768 ~ 32767<BR>부호 없는 수는 0 ~ 65535 까지 표현 (2바이트)  
MEDIUMINT(M)|부호 있는 수는 -8388608 ~ 8388607<BR>부호 없는 수는 0 ~ 16777215 까지 표현 (3바이트)  
INT(M)<BR>INTEGER(M)|부호 있는 수는 -2147483648 ~ 2147483647<BR>부호 없는 수는 0 ~ 4294967295 까지 표현 (4바이트)  
BIGINT(M)|부호 있는 수는 -92233720036854775808 ~ 92233720036854775807<BR>부호 없는 수는 0~18446744073709551615 (8바이트)  

#### 2) 고정 소수점 유형  

:---:|:---
DECIMAL(M,D)<BR>NUMERIC|M자리 정수(정밀도)와 D자리 소수점(스케일)으로 표현<BR>최대 65자리까지 표현할 수 있다.  

#### 3) 부동 소수점 유형  

:---:|:---
FLOAT(M,D)|정밀도가 작은 부동소수점을 표현. UNSIGNED 인 경우 음수 값을 허용하지 않는다.<BR>-3.402823466E+38 ~ 3.402823466E+38  
DOUBLE(M,D)|보통 크기의 부동소수점을 표현. UNSIGNED 인 경우 음수 값을 허용하지 않는다.<BR>-1.7976931348623157E+308 ~ 1.7976931348623157E+308  

- FLOAT, DOUBLE 등의 부동 소수점 유형은 MySQL 8.0.17 이후 버전부터 사용되지 않습니다.  
-- 참고문서 : [MySQL 8.0 Reference > Problems with Floating-Point Values](https://dev.mysql.com/doc/refman/8.0/en/problems-with-float.html)  


### 2. 날짜형  

:---:|:---
DATE|날짜를 표현하는 타입 (3바이트)<BR>1000-01-01 ~ 9999-12-31  
DATETIME|날짜와 시간을 같이 나타내는 타입 (8바이트)<BR>1000-01-01 00:00:00 ~ 9999-12-31 23:59:59  
TIMESTAMP|1970-01-01 00:00:00 ~ 2037-01-19 03:14:07<BR>INSERT, UPDATE 연산에 유리하다. (4바이트)  
TIME|시간을 표현하는 타입 (3바이트)<BR>-838:59:59 ~ 838:59:59  
YEAR|연도를 나타낸다. (1바이트)<BR>1901 ~ 2155, 70 ~ 69 (1970~2069)  

- YEAR(4) 와 같이 명시적인 길이를 표기한 데이터 유형은 MySQL 8.0.19 이후 버전부터 사용되지 않습니다.  
- YEAR(2) 와 같이 두 자리로 표기하는 데이터 유형은 MySQL 5.7 이후 버전부터 지원하지 않습니다.  
-- 참고문서 : [MySQL 5.7 Reference > 2-Digit YEAR(2) Limitations and Migrating to 4-Digit YEAR](https://dev.mysql.com/doc/refman/8.0/en/two-digit-years.html)  


### 3. 문자형  

:---:|:---
CHAR(M)|고정 길이를 가지는 문자열을 저장한다. (M : 0~255)  
VARCHAR(M)|가변 길이를 가지는 문자열을 저장하며, 후행 공백을 제거하지 않는다. (M : 0~65,535)<BR>M이 0~255 이면 문자길이+1byte, ~65,535 이면 문자길이+2byte   
TINYBLOB<BR>TINYTEXT|1~255 개의 가변 길이를 가지는 문자열을 저장한다. (문자길이+1byte)  
BLOB<BR>TEXT|1~65,535 개의 가변 길이를 가지는 문자열을 저장한다. (문자길이+2byte)<BR>BLOB 는 바이너리 데이터, TEXT 는 문자 데이터 저장에 유리하다.  
MEDIUMBLOB<BR>MEDIUMTEXT|1~16,777,215 개의 가변 길이를 가지는 문자열을 저장한다. (문자길이+3byte)  
LONGBLOB<BR>LONGTEXT|1~429,496,729 개의 가변 길이를 가지는 문자열을 저장한다. (문자길이+4byte)  
ENUM|문자 형태인 value 를 숫자로 저장하여 최대 65,535 개의 문자열 중 한가지를 반환<BR>255 이하 value 는 1바이트, 65,535 이하 value 는 2바이트  
SET|비트 연산 열거형, ENUM 형과 동일하게 문자열 값을 정수값으로 매핑하여 저장한다.  

- ENUM 은 반드시 하나의 값만 저장되며, SET 은 다중 선택이 가능합니다.  
