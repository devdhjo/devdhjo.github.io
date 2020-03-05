---
layout: post
title: "[SQLD] 016# SQL 기본 ⑺ 함수 (FUNCTION)"
tags: [database, SQLD, SQL, FUNCTION]
categories: sqld
---


### 1. 내장 함수 (BUILT-IN FUNCTION) 개요  

- 함수  
	-- 내장함수 (Built-in Function)  
    -- 사용자 정의 함수 (User Defined Function)  


-  내장함수  
	-- 단일행 함수 (Single-Row Function)  
    -- 다중행 함수 (Multi-Row Function)  
  - 집계함수 (Aggregate Function)  
  - 그룹함수 (Group Function)  
  - 윈도우함수 (Window Function)   


#### ◇ 단일행 함수  

단일행 함수는 처리하는 데이터의 형식에 따라서 문자형, 숫자형, 날짜형, 변환형, NULL 관련 함수로 나눌 수 있다.  

![sqld-ii-1-24](https://drive.google.com/uc?id=1aah-H8Oex1KjHx0kOVfDKSXK6BKb8x9j)  

- 단일행 함수의 특징  
1) SELECT, WHERE, ORDER BY 절에 사용 가능하다.  
2) 각 행에 대해 개별적으로 작용하여 데이터 값들을 조작하고, 각 행에 대한 조작 결과를 리턴한다.  
3) 여러 인자 (Argument) 를 입력해도 단 하나의 결과만 리턴한다.  
4) 함수의 인자로 상수, 변수, 표현식이 사용 가능하고, 여러 개의 인수를 가질 수 있다.  
5) 특별한 경우가 아니면 함수의 인자를 함수로 사용하는 함수의 중첩이 가능하다.  




### 2. 문자형 함수  

문자형 함수는 문자 데이터를 매개 변수로 받아 문자나 숫자 값의 결과를 돌려주는 함수이다.  

![sqld-ii-1-25](https://drive.google.com/uc?id=1v4ELh_7zmjSmXBM2fBrC5YBSE8WydGGR)  




### 3. 숫자형 함수  

숫자형 함수는 숫자를 입력받아 숫자 값의 결과를 리턴하는 함수이다.  

![sqld-ii-1-27](https://drive.google.com/uc?id=15T2XEvOqIMHVT_YCEizd5_tunJYQbKkj)  

![sqld-ii-1-28](https://drive.google.com/uc?id=1NiCh6kLx9GsiBr_89aTsKdYIT5Ow6VPK)  




### 4. 날짜형 함수  

날짜형 함수는 DATE 타입의 값을 연산하는 함수이다. Oracle 의 TO_NUMBER(TO_CHAR( )) 함수의 경우 변환형 함수로 구분할 수 있으나, SQL Server 의 YEAR, MONTH, DAY 함수와 매핑하기 위해 날짜형 함수에서 설명한다.  

![sqld-ii-1-29](https://drive.google.com/uc?id=1VyZrUZ7kIkP9DK2SKkxhJXu2pVwRcMF3)  

![sqld-ii-1-30](https://drive.google.com/uc?id=1w96shVHGPDVPGZ8AdDD3Cc9wqFrP7S70)  

DATE 변수는 날짜를 저장할 때 내부적으로 세기 (Century), 년 (Year), 월 (Month), 일 (Day), 시 (Hours), 분 (Minutes), 초 (Seconds) 와 같은 숫자 형식으로 변환하여 저장한다. 그렇기 때문에 덧셈, 뺄셈 같은 산술 연산자로 계산이 가능하다.  




### 5. 변환형 함수  

변환형 함수는 특정 데이터 타입을 다양한 형식으로 출력하고 싶은 경우에 사용한다.  

![sqld-ii-1-31](https://drive.google.com/uc?id=161vDK4ht1c-xObow2RPqVaf2c_fJ-Dui)  

변환형 함수는 크게 두 가지 방식이 있다. 암시적 데이터 유형 변환의 경우 성능 저하가 발생할 수 있으며, 데이터베이스가 자동으로 계산하지 않는 경우도 있기 때문에 명시적인 데이터 유형 변환 방법을 사용하는 것이 바람직하다.  

명시적 데이터 유형 변환에 사용되는 대표적인 변환형 함수는 다음과 같다.  

![sqld-ii-1-32](https://drive.google.com/uc?id=1bazUitqrtKdgWmsebbXz4tNUOA4L6F0O)  




### 6. CASE 표현  

CASE 표현은 IF-THEN-ELSE 논리와 유사한 방식으로 표현식을 작성하여 SQL의 비교 연산 기능을 보완한다. ANSI/ISO SQL 표준에서 CASE Expression 이라고 되어있지만, 함수와 같은 성격을 가지며 Oracle 의 DECODE 함수와 같은 기능을 한다.  

![sqld-ii-1-33](https://drive.google.com/uc?id=1vkWOCIFkF3KQ6K0ole-PUnMERJ0mA_Cc)  

CASE 표현을 하기 위해 조건절을 표현하는 두 가지 방법이 있다.  

#### 1) Simple Case Expression  

```sql
CASE 기준값 WHEN 비교값1 THEN 결과값1
            WHEN 비교값2 THEN 결과값2
            ...
            ELSE 결과값n
END
```

표현식 판단에서 EQUI(=) 조건만 사용한다면 SIMPLE CASE EXPRESSION 로 간단하게 사용할 수 있다. Oracle 의 DECODE 함수와 기능면에서 동일하다.  


#### 2) Searched Case Expression  

```sql
CASE WHEN 기준값1 = 비교값1 THEN 결과값1
     WHEN 기준값2 = 비교값2 THEN 결과값2
     ...
     ELSE 결과값n
END
```

표현식 판단에서 EQUI(=) 를 포함한 다양한 조건을 사용할 때는 SEARCHED_CASE_EXPRESSION 을 이용한다.  




### 7. NULL 관련 함수  

#### 1) NVL / ISNULL 함수  

- NULL 의 특성  
	-- NULL 은 아직 정의되지 않은 값으로, 0 (숫자) 또는 공백 (문자) 과는 다르다.  
    -- 테이블 생성 시 NOT NULL 또는 PRIMARY KEY 로 정의되지 않은 데이터 유형은 NULL 값을 포함할 수 있다.  
    -- NULL 을 포함하는 연산의 경우 결과값도 NULL 이다.  
    -- 결과값이 NULL 인 경우 다른 값을 얻고자 할 때 NVL/ISNULL 함수를 사용한다. 주로 산술연산에서 값이 NULL 인 경우 사용하며, 다중행 함수는 전체 중 일부의 NULL 은 대상에서 제외하기 때문에 사용하지 않는다.  

![sqld-ii-1-35](https://drive.google.com/uc?id=1fF-OO05KcjXPs_PVsiBUICTiFBdosbxf)  


#### 2) NULL 과 공집합  

SELECT 문의 결과로 선택된 행의 데이터가 NULL 인 경우 NVL/ISNULL 함수를 사용할 수 있지만, SELECT 문의 결과로 선택된 행이 없다면 공집합이 발생하여 NVL/ISNULL 함수를 사용할 수 없다. 공집합의 NVL/ISNULL 함수 사용 결과 역시 공집합이다. 반면 집계 함수의 결과에는 공집합인 경우에도 NVL/ISNULL 함수 사용이 가능하다.  


#### 3) NULLIF  

```sql
NULLIF(표현식1, 표현식2)
```

NULLIF 함수는 표현식1과 표현식2 가 일치하면 NULL을, 일치하지 않으면 표현식1 을 리턴한다. 특정값을 NULL 로 대체하는 경우에 유용하게 사용할 수 있다.  


#### 4) 기타 NULL 관련 함수 (COALESCE)  

```sql
COALESCE (인수1, 인수2, ...)
```

COALESCE 함수는 인수의 개수와 관계 없이, NULL 이 아닌 최초의 인수를 리턴한다. 모든 인수가 NULL 인 경우에는 NULL 을 리턴한다.  
