---
layout: post
title: "[SQLD] 003# 데이터 모델링의 이해 ⑵ 속성"
tags: [database, SQLD, MODELING, ATTRIBUTE]
categories: sqld
---


### 1. 속성의 개념  

- 속성의 정의  
-- 업무에서 필요로 하는 인스턴스에서 관리하고자 하는, 의미상 더 이상 분리되지 않는 최소의 데이터 단위  
-- 엔터티를 설명하고, 인스턴스의 구성요소가 된다.  


### 2. 엔터티, 인스턴스와 속성, 속성값에 대한 내용 및 표기법  

#### 1) 엔터티, 인스턴스, 속성, 속성값의 관계  

엔터티에는 두 개 이상의 인스턴스가 존재하고, 각 엔터티에는 고유의 성격을 표현하는 속성 정보를 두 개 이상 존재한다.  

-- 예를 들어, 사원이라는 엔터티에 속한 인스턴스들의 성격을 구체적으로 나타내는 항목 (이름, 주소, 연락처, 직책 등) 이 속성이다.  

각각의 인스턴스는 속성의 집합으로 설명될 수 있으며, 속성은 관계로 기술될 수 없고, 속성이 속성을 가질 수 없다.  

![sqld-1-1-25](https://drive.google.com/uc?id=1twxHQzV5u3-EDPgo5HAjB25hrVl35KIo)  

엔터티, 인스턴스, 속성, 속성값에 대한 관계를 분석하면 다음과 같다.  

(1) 한 개의 엔터티는 두 개 이상의 인스턴스의 집합이어야 한다.  
(2) 한 개의 엔터티는 두 개 이상의 속성을 가진다.  
(3) 한 개의 속성은 한 개의 속성값을 가진다.  


#### 2) 속성의 표기법  

속성의 표기법은 엔터티 내에 이름을 포함하여 표현하면 된다.  

![sqld-1-1-26](https://drive.google.com/uc?id=1HNMtgzjWmOAlh13qDYTw53Z8nAGrDvMc)  


### 3. 속성의 특징  

(1) 엔터티와 마찬가지로 반드시 해당 업무에서 필요하고 관리하고자 하는 정보여야 한다.  

(2) 정규화 이론에 근간하여 정해진 주식별자에 함수적 종속성을 가져야 한다.  

(3) 한 개의 속성에는 한 개의 값만을 가진다. 하나의 속성에 여러 개의 값이 있는 다중값일 경우 별도의 엔터티를 이용하여 분리한다.  


### 4. 속성의 분류  

#### 1) 특성에 따른 분류  

- 기본 속성 (Basic Attribute)  
-- 업무로부터 추출한 모든 속성 (코드성 데이터, 엔터티 식별을 위한 데이터, 다른 속성에 의해 생성된 속성 제외)  

- 설계 속성 (Designed Attribute)  
-- 업무상 필요한 데이터 외에 데이터 모델링이나 업무 규칙화를 위해 속성을 새로 만들거나 변형하여 정의하는 속성  
	- 대게 코드성 속성은 원래 속성을 업무상 필요에 의해 변형하여 만든 설계속성이고, 일련번호와 같은 속성은 단일(Unique)한 식별자를 부여하기 위해 모델상에서 새로 정의하는 설계속성이다.  

- 파생 속성 (Derived Attribute)  
-- 다른 속성으로부터 계산이나 변형이 되어 생성되는 속성 (예치 기간에 따른 이자율, 총 금액 등의 데이터)  
	- 다른 속성에 영향을 받기 때문에 프로세스 설계 시 데이터 정합성을 유지하기 위한 유의점이 많다.  


#### 2) 엔터티 구성 방식에 따른 분류  

![sqld-1-1-28](https://drive.google.com/uc?id=1F0yPfCFTxc2Uw53RnTLvc3vy-0gU-g_a)  

- PK (Primary Key) 속성 : 엔터티를 식별할 수 있는 속성  
- FK (Foreign Key) 속성 : 다른 엔터티와의 관계에서 포함된 속성  
- 일반 속성 : 엔터티에 포함되어 있고, PK, FK에 포함되지 않은 속성  
  -- 복합 속성 (Composite Attribute) : 여러 세부 속성들로 구성된 속성 (ex - 시, 구, 동, 번지로 구성된 주소 속성)  
  -- 단순 속성 (Simple Attribute) : 더 이상 다른 속성들로 구성될 수 없는 속성 (ex - 나이, 성별 등)  
  -- 단일값 속성 (Single-Valued Attribute) : 반드시 하나의 값만 존재하는 속성 (ex - 주민등록번호 등)  
  -- 다중값 속성 (Multi-Valued Attribute) : 여러 개의 값을 가지는 속성 (ex - 집, 휴대전화, 회사 전화번호와 같이 여러 개의 값을 가질 수 있는 전화번호 속성)  
  - 다중값 속성의 경우 하나의 엔터티에 포함될 수 없으므로 1차 정규화를 하거나, 별도의 엔터티를 만들어 관계로 연결해야 한다.  


### 5. 도메인 (Domain)  

- 각 속성이 가질 수 있는 값의 범위  
- 엔터티 내에서 속성에 대한 데이터 타입과 크기, 제약사항을 지정하는 것  
-- 학생의 엔터티 중 학점이라는 속성의 도메인은 0.0~4.0 사이의 실수값이다.  
-- 주소라는 속성의 도메인은 길이가 20자 이내인 문자열로 정의할 수 있다.  


### 6. 속성의 명명  

![sqld-1-1-29](https://drive.google.com/uc?id=1_j7qQSzNT8MH_dcL049V_m6fUD_4KIzz)  