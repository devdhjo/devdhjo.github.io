---
layout: post
title: "[SQLD] 005# 데이터 모델링의 이해 ⑷ 식별자"
tags: [database, SQLD, MODELING, IDENTIFIER]
categories: sqld
---


### 1. 식별자의 개념  

- 엔터티 내에서 인스턴스들을 구분 할 수 있는 구분자  
-- 하나의 엔터티는 반드시 하나의 유일한 식별자가 존재해야 한다.  


### 2. 식별자의 특징  

![sqld-i-1-7](https://drive.google.com/uc?id=1k8Oxw0_KZG4Me_kGw847nAJC6uHUPT5P)  


### 3. 식별자의 분류 및 표기법  

#### 1) 식별자 분류  

- 대표성 여부  
-- 주식별자 (Primary Identifier)  
-- 보조식별자 (Alternate Identifier)  

- 스스로 생성 여부  
-- 내부식별자  
-- 외부식별자 (Foreign Identifier)  

- 단일속성여부  
-- 단일식별자 (Single Identifier)  
-- 복합식별자 (Composit Identifier)  

- 대체여부  
-- 본질식별자  
-- 인조식별자  

![sqld-i-1-8](https://drive.google.com/uc?id=1XwRxU2YQRqXePgBYgR_4UcbWJdCXSL9I)  


#### 2) 식별자 표기법  

식별자에 대한 위의 분류방법을 데이터 모델에서 [그림 I-1-42]와 같이 표현할 수 있다.  
![sqld-1-1-42](https://drive.google.com/uc?id=1mb15tJlQ1F_JPzZA9RIUinPrMigK5-Ax)  


### 4. 주식별자 도출기준  

- 해당 업무에서 자주 이용되는 속성을 주식별자로 지정한다.  

- 명칭, 내역 등과 같이 이름으로 기술되는 것은 주식별자로 지정하지 않는다.  
-- 일련번호나 코드 등을 사용하여 정확히 기술하기 쉬운 것으로 사용한다.  

- 복합으로 주식별자를 구성할 경우 너무 많은 속성이 포함되지 않도록 한다.  
-- 주식별자의 속성이 많은 경우 새로운 인조식별자(Artificial Identifier)를 생성하여 데이터 모델을 구성한다.  

![sqld-1-1-45](https://drive.google.com/uc?id=1O4sHfJ6AzLW3tTZt4VHhSs1y0ONr4wK-)  


### 5. 식별자 관계와 비식별자 관계에 따른 식별자  

#### 1) 식별자 관계와 비식별자 관계의 결정  

- 외부 식별자 (Foreign Identifier) : 다른 엔터티와의 관계를 통해 자식 엔터티에 생성되는 속성  
- 자식 엔터티에서 외부식별자를 자신의 주식별자로 이용할것인지, 또는 속성으로만 이용할 것인지 결정해야 한다.  

![sqld-1-1-46](https://drive.google.com/uc?id=1rDXlXqQ9QIqetSWlbqEek2tiAIJCVBdW)  


#### 2) 식별자 관계 (Identifying Relationship)  

- 부모로부터 받은 식별자를 자식엔터티의 주식별자로 이용하는 경우는 Null 값이 올 수 없으므로, 반드시 부모엔터티가 생성되어야 자식엔터티도 생성된다.  
- 부모에게 받은 속성을 자식엔터티가 모두 사용하고, 그것만을 주식별자로 사용한다면 부모엔터티와 자식엔터티는 1:1의 관계가 될 것이고, 부모엔터티에서 받은 속성과 함께 스스로 가지고 있는 속성으로 주식별자가 구성되는 경우 1:M 관계가 된다.  

![sqld-1-1-47](https://drive.google.com/uc?id=1QliDsOA6UbIYjXdULS-rsX7uaIacQ-O_)  

- 자식 엔터티의 주식별자로 부모의 주식별자가 상속이 되는 경우를 식별자 관계라고 한다.  


#### 3) 비식별자 관계 (Non-Identifying Relationship)  

- 부모로부터 받은 속성을 주식별자로 사용하지 않고 속성으로만 사용하는 경우를 비식별자 관계라고 한다.  

- 비식별자 관계에 의한 외부 속성이 생성되는 경우  
  -- 자식 엔터티에서 받은 속성이 필수값이 아닌 경우 부모엔터티가 없는 자식 엔터티가 생성된다.  
  -- 엔터티별로 데이터의 생명주기(Life Cycle)를 다르게 관리하는 경우, 부모엔터티가 먼저 소멸될 수 있다.  
  -- 여러 엔터티가 하나의 엔터티로 통합되어 표현되어 있지만, 각각의 엔터티가 별도의 관계를 가지는 경우도 이에 해당한다.  
![sqld-1-1-48](https://drive.google.com/uc?id=1Y8YupdEwTIRo9XKM3XPodSAMf6eFgOTD)  
  -- 자식 엔터티에서 별도의 주식별자를 생성하는 것이 더 유리하다고 판단될 때는 비식별자 관계에 의한 외부식별자로 표현한다.  
![sqld-1-1-49](https://drive.google.com/uc?id=1GdP1Ha2xRuk0ZCC0k_X0WHfvwdZmcxXF)  


#### 4) 식별자 관계로만 설정할 경우의 문제점  

데이터 모델의 흐름이 길어질수록 PK 속성의 수가 증가하는데, 주식별자가 많아질수록 개발 복잡성과 오류 가능성을 유발시킬 수 있다.  

![sqld-1-1-50](https://drive.google.com/uc?id=1BwSIXeEM0emIF9ylTfxBGkskEATbRgtZ)  


#### 5) 비식별자 관계로만 설정할 경우의 문제점  

부모엔터티를 상속받을 때는 주로 PK 속성을 상속받게 되는데, 이에 따라 데이터를 조회할 때 조건으로 사용된다.  
그런데 이 속성을 비식별자 관계로 설정하게 되면 자식엔터티의 데이터를 처리할 때 부모엔터티까지 찾아야 하는 경우가 발생한다.  

![sqld-i-1-9](https://drive.google.com/uc?id=1DZFfkfCl0ILQln68U1WbKcoxpPVkgXZk)  

비식별자 관계로만 데이터 모델링을 전개하다 보면 SQL 구문에 많은 조인이 걸리게 되고, 그에 따라 복잡성이 증가하고 성능이 저하된다.  


#### 6) 식별자 관계와 비식별자 관계 모델링  

- 비식별자관계 선택 프로세스  
식별자 관계로 모든 관계가 연결되면서, 다음 조건에 해당할 경우 비식별자 관계로 조정하면 된다.  
여기에서 가장 중요한 요인은 자식 엔터티의 독립된 주식별자 구성이 필요한지 분석하는 부분이다. 독립적으로 주식별자를 구성한다는 의미는 업무적 필요성과 성능상 필요여부를 모두 포함하는 의미로 이해하면 된다.

![sqld-1-1-53](https://drive.google.com/uc?id=1dKQWdUe6pyJK1EohRJ1Cp3Uy5xXSRT7u)  


- 식별자와 비식별자 관계 비교  

![sqld-i-1-10](https://drive.google.com/uc?id=1Mmg96xNJG7L1ykfrgX4GNibyIpAYg4jg)  


- 식별자와 비식별자를 적용한 데이터 모델  
[그림 I-1-54] 의 모델은 업무의 특성에 따라 식별자 관계와 비식별자 관계를 적절하게 선택함으로써 데이터 모델의 균형감을 갖추었다고 볼 수 있다.  

![sqld-1-1-54](https://drive.google.com/uc?id=1QWNojXevOuZGd2O75tXYds4eWxhG0Oml)  
