---
layout: post
title: "[SQLD] 004# 데이터 모델링의 이해 ⑶ 관계"
tags: [database, SQLD, MODELING, RELATIONSHIP]
categories: sqld
---


### 1. 관계의 개념  

#### 1) 관계의 정의  

- 인스턴스와 인스턴스 사이의 논리적인 연관성이 부여된 상태  

![sqld-1-1-30](https://drive.google.com/uc?id=1pk44L-BwmNednpLp8d0yF_niOf_ytSf7)  


#### 2) 관계의 패어링  

- 패어링 : 인스턴스와 인스턴스 사이에 관계가 설정되어 있는 형태  
-- 엔터티는 인스턴스의 집합을 논리적으로 표현하였다면, 관계는 관계 패어링의 집합을 논리적으로 표현한 것이다.  


### 2. 관계의 분류  

(1) 존재에 의한 관계  
(2) 행위에 의한 관계  

![sqld-1-1-32](https://drive.google.com/uc?id=1E-mZQOKQmFEk8NLe07wO-vu-Te1kucRo)  


### 3. 관계의 표기법  

- 관계명 (Membership) : 관계의 이름  
- 관계차수 (Cardinality) : 1:1, 1:M, M:N  
- 관계선택사양 (Optionality) : 필수관계, 선택관계  

#### 1) 관계명 (Membership)  

관계명은 엔터티가 관계에 참여하는 형태를 지칭한다.  
각각의 관계는 두 가지의 관점으로 표현되며, 두 개의 관계명을 가지고 있다.  

![sqld-1-1-33](https://drive.google.com/uc?id=1yUiUCaDCg4q3O5VgDnz9491kingCstei)  

- 관계명 명명규칙  
-- 구체적으로 표현한다. 어떤 행위 또는 상태가 있는지 파악할 수 있도록 표현한다.  
-- 현재형으로 표현한다. '-했다', '-할 것이다' 로 표현하지 않는다.  


#### 2) 관계차수 (Cardinality)  

- 1:1 (ONE TO ONE) 관계 표시  
-- 각 엔터티는 다른 엔터티에 대해 단 하나의 관계만을 가지고 있다.  

![sqld-1-1-34](https://drive.google.com/uc?id=1YqgQ-lcJYeOLclJqEpvktvKZyfb5f5lZ)  

- 1:M (ONE TO MANY) 관계 표시  
-- 각 엔터티는 관계를 맺는 다른 엔터티에 대해 하나 이상의 수와 관계를 가지고 있다.  
-- 그러나 반대의 관점에서는 단 하나의 관계를 가지고 있다.  

![sqld-1-1-35](https://drive.google.com/uc?id=11F_fk3RBEoyt7Dhe_gb2mvNHR8WPwAw6)  

- M:M (MANY TO MANY) 관계 표시  
-- 엔터티에 대해 하나 이상의 수와 관계를 가지며, 반대의 관점도 동일하게 관계에 참여하고 있다.  
-- M:N 관계 데이터 모델은 이후 두 개의 주식별자를 상속받은 관계엔터티를 이용하여 3개의 엔터티로 구분된다.  

![sqld-1-1-36](https://drive.google.com/uc?id=138sNpOgMFJHN7ZuCyD78H_AY1GzGqPHO)  


#### 3) 관계선택사양 (Optionality)  

- 필수참여 (Mandatory Membership)  
-- 참여하는 모든 참여자가 반드시 관계를 가지는, 타 엔터티의 참여자와 연결이 되어야 하는 관계  

- 선택참여 (Optional Membership)  
-- 정보로서 관련은 있지만, 서로가 필수적인 관계는 아닌 관계  
-- Foreign Key로 연결된 경우 Null 을 허용할 수 있는 항목이 된다.

- 만약 관계가 표시된 양 쪽 엔터티에 모두 선택참여가 표시된다면, 즉 0:0 관계라면 잘못 설정된 관계인지 검토해야 한다.  

![sqld-1-1-38](https://drive.google.com/uc?id=1Gxxbz3XyOvRqK0PTZiGbrmejgwlvo829)  


### 4. 관계의 정의 및 읽는 방법  

#### 1) 관계 체크사항  

두 개의 엔터티 사이에서 관계를 정의할 때 다음 사항을 체크해보도록 한다.  
- 두 개의 엔터티 사이에 관심 있는 연관 규칙이 존재하는가?  
- 두 개의 엔터티 사이에 정보의 조합이 발생되는가?  
- 업무기술서, 장표에 관계 연결에 대한 규칙이 서술되어 있는가?  
- 업무기술서, 장표에 관계 연결을 가능하게 하는 동사(Verb)가 있는가?  


#### 2) 관계 읽기  

⑴ 기준(Source) 엔터티를 한 개 또는 각(Each) 으로 읽는다.  
⑵ 대상(Target) 엔터티의 관계참여도, 즉 개수(하나 이상) 를 읽는다.  
⑶ 관계선택사양과 관계명을 읽는다.  

![sqld-1-1-39](https://drive.google.com/uc?id=1kM05geUlFkNg-7-aIFDM1uvZgT4k-Au-)  
