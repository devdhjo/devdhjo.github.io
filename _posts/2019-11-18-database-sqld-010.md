---
layout: post
title: "[SQLD] 010# SQL 기본 ⑴ 관계형 데이터베이스 개요"
tags: [database, SQLD, SQL]
categories: sqld
---


#### 1. 데이터베이스  

넓은 의미에서의 데이터베이스는 일상적인 정보들을 모아 놓은 것 자체를 의미한다. 그러나 일반적으로 데이터베이스라고 말할 때는 특정 기업이나 조직 또는 개인이 필요에 의해 (예를 들면, 부가 가치가 발생하는) 데이터를 일정한 형태로 저장해 놓은 것을 의미한다.  
이 데이터를 위한 기본적인 요구상항을 만족시키고, 보다 효율적으로 관리할 뿐만 아니라 예기치 못한 사건으로 인한 데이터의 손상을 피하고, 필요시 데이터를 복구하기 위한 시스템을 DBMS (Database Management System) 이라고 한다.  

- 데이터베이스의 발전  
	-- 1960년대 : 플로우차트 중심의 개발 방법을 사용하셨으며, 파일 구조를 통해 데이터를 저장 및 관리하였다.  
    -- 1970년대 : 데이터베이스 관리 기법이 처음 태동되던 시기였으며, 계층형 (Hierarchical) 데이터베이스, 망형 (Network) 데이터베이스 같은 제품들이 상용화되었다.  
    -- 1980년대 : 현재 대부분의 기업에서 사용되고 있는 관계형 데이터베이스가 상용화 되었으며, Oracle, Sybase, DB2 와 같은 제품이 상용화되었다.  
    -- 1990년대 : Oracle, Sybase, Informix, DB2, Teradata, SQL Server 외 많은 제품들이 보다 향상된 기능으로 정보 시스템의 확실한 핵심 솔루션으로 자리잡게 되었으며, 인터넷 환경의 급속한 발전과 객체 지향 정보를 지원하기 위해 객체 관계형 데이터베이스로 발전하였다.  


- 관계형 데이터베이스 (Relational Database)  
	-- 1970년 영국의 수학자였던 E.F. Codd 박사의 논문에서 처음 관계형 데이터베이스가 소개된 이후, IBM 의 SQL 개발 단계를 거쳐 Oracle 을 선발로 여러 회사에서 상용화 된 제품을 내놓았다. 이후 관계형 데이터베이스의 여러 장점이 알려지면서 기존의 파일 시스템과 계층형, 망형 데이터베이스를 대부분 대체하며 주력 데이터베이스가 되었다.  
    파일 시스템의 경우, 하나의 파일을 여러 사용자가 동시에 검색할 수는 있지만 동시에 입력/수정/삭제할 수 없기 때문에 정보 관리가 어려워 원래 데이터 파일을 여러 개 복사하여 사용하게 된다, 이렇게 되면 동일한 데이터가 여러 곳에 저장되는 문제가 발생하고, 서로 다른 정보를 가진 파일이 존재하기 때문에 데이터의 불일치성이 발생한다. 따라서 데이터 정합성을 보장하기 힘들게 된다.  
    이러한 문제에 대해 관계형 데이터베이스는 정규화를 통한 합리적인 테이블 모델링을 통해 이상 현상을 제거하고, 데이터 중복을 피할 수 있으며, 동시성 관리, 병행 제어를 통해 많은 사용자들이 동시에 데이터를 공유 및 조작할 수 있는 기능을 제공하고 있다. 또한 메타 데이터를 총괄 관리할 수 있기 때문에 데이터의 성격, 속성, 표현방법 등을 체계화할 수 있고, 데이터 표준화를 통한 데이터 품질을 확보할 수 있는 장점을 가지고 있다.  
    또한 DBMS 는 인증된 사용자만이 참조할 수 있도록 보안 기능을 제공한다. 테이블 생성 시에 사용할 수 있는 다양한 제약조건을 이용하여 사용자가 실수로 조건에 위배되는 데이터를 입력하거나, 관계를 연결하는 중요 데이터를 삭제하는 것을 방지하여 데이터무결성 (Integrity) 을 보장할 수 있다.  
    DBMS 는 시스템의 갑작스런 장애로부터 사용자가 입력/수정/삭제 하던 데이터가 제대로 반영될 수 있도록 보장해주는 기능과 시스템 다운, 재해 등의 상황에서도 데이터를 복구할 수 있는 기능을 제공한다.  




#### 2. SQL (Structured Query Language)  

SQL 은 관계형 데이터베이스에서 데이터 정의, 조작, 제어를 하기 위해 사용하는 언어이다. SQL 의 최초 이름이 SEQUEL (Structured English QUEry Language) 이었기 때문에 '시큐얼' 로 읽는 경우도 있지만, 표준은 SQL 이므로 '에스큐엘' 로 읽는 것을 권고한다.  
1986년부터 ANSI/ISO 를 통해 표준화되고 정의된 SQL 기능은 벤더별 DBMS 개발의 목표가 된다. 일부 구체적인 용어는 다르더라도 대부분의 관계형 데이터베이스에서 ANSI/ISO 표준을 최대한 따르고 있기 때문에, SQL 에 대한 지식은 재활용할 수 있고, ANSI/ISO SQL-99, SQL-2003 이후 기준이 적용된 SQL 이라면 프로그램의 이식성을 높이는 데에도 공헌한다.  
SQL 문장은 단순 스크립트가 아니라 독립된 하나의 개발 언어이다. 하지만 일반적인 프로그래밍 언어와 달리 SQL 은 관계형 데이터베이스에 대한 전담 접속 용도로 사용되며 (다른 언어는 관계형 데이터베이스에 접속할 수 없다.), 집합 논리에 입각하한 것이므로, SQL 도 데이터를 집합으로써 취급한다.  
이렇게 특정 데이터들의 집합에서 필요로 하는 데이터를 꺼내 조회하고, 새로운 데이터를 입력/수정/삭제하는 행위를 통해 사용자는 데이터베이스와 대화하게 된다. SQL 은 이러한 대화를 가능하도록 매개 역할을 한다.  

![sqld-ii-1-1](https://drive.google.com/uc?id=1XtGi-z-Zh6UKpWXLrPxsuXTcoHYkU_QO)  




#### 3. TABLE  

관계형 데이터베이스에서 데이터는 테이블 형태로 저장된다. 테이블 (TABLE) 은 데이터를 저장하는 객체 (Object) 로서 관계형 데이터베이스의 기본 단위이다. 테이블의 세로 방향을 컬럼 (Column), 가로 방향을 행 (Row) 이라 하고, 컬럼과 행이 겹치는 하나의 공간을 필드 (Field) 라고 한다.  

![sqld-2-1-4](https://drive.google.com/uc?id=15eP_-3EZskdhIN_OrlC6iYveIBZ5L194)  

![sqld-ii-1-5](https://drive.google.com/uc?id=129rwAZUp5KWhEcD_8Bl2bAccrRkkhgex)   

각 행을 한 가지 의미로 특정할 수 있는 한 개 이상의 컬럼을 기본키 (Primary Key) 라고 하며, 데이터의 불필요한 중복을 줄이기 위해 테이블을 분할하는 것을 정규화 (Normalization) 라고 한다. 분할된 테이블은 공통된 컬럼의 값에 의해 연결되는데, 이 컬럼을 외부키 (Foreign Key) 라고 한다.  

![sqld-2-1-5](https://drive.google.com/uc?id=176jEujmutp7_UUofGs-7czdTaS0DO7ZP)  

![sqld-ii-1-6](https://drive.google.com/uc?id=1Tky0cmNuROdV37UG2hPG7rjfJ9Iqi4DN)   




#### 4. ERD (Entity Relationship Diagram)  

테이블 간 서로의 상관 관계를 그림으로 도식화 한 것을 E-R 다이어그램, 간단히 ERD 라고 한다. ERD 의 구성요소는 엔터티 (Entity), 관계 (Relationship), 속성 (Attribute) 3가지이다.  

![sqld-2-1-6](https://drive.google.com/uc?id=1W98GIxcSokH2itt59kQYjU2gWebu9rkD)  

[그림 II-1-6] 에서 팀과 선수는 '소속' 이라는 관계로 연결되어 있다.  

K-리그 테이블을 예로 들어 IE 표기법과 Barker 표기법을 사용한 ERD 는 다음과 같다.  
	-- 하나의 팀은 여러 명의 선수를 포함할 수 있다. : 한 명의 선수는 하나의 팀에 반드시 속한다.  
    -- 하나의 팀은 하나의 전용 구장을 반드시 가진다. : 하나의 운동장은 하나의 홈팀을 가질 수 있다.  
    -- 하나의 운동장은 여러 게임의 스케쥴을 가질 수 있다. : 하나의 스케쥴은 하나의 운동장에 반드시 배정된다.  

![sqld-2-1-8](https://drive.google.com/uc?id=17BFIFogLB6XbiCiTiKNK1XiLH9riUj5F)  
