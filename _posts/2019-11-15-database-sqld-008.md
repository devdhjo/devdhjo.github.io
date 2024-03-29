---
layout: post
title: "[SQLD] 008# 데이터 모델과 성능 ⑵ 대량 데이터베이스"
tags: [database, SQLD, MODELING]
categories: sqld
---


### Ⅰ. 대량 데이터에 따른 성능  

### 1. 대량 데이터 발생에 따른 테이블 분할 개요  

한 테이블에 데이터가 대량으로 집중되거나 하나의 테이블에 여러 개의 컬럼이 존재하여 디스크에 많은 블럭을 점유하는 경우 성능저하를 유발할 수 있다. 대량의 데이터가 하나의 테이블에 존재하게 되면 인덱스를 생성할 때 인덱스의 용량이 커지기 때문에 조회 성능에는 물론이고, 입력/수정/삭제하는 트랜잭션의 경우에도 성능 저하를 유발하게 된다.  

![sqld-1-2-18](https://drive.google.com/uc?id=1mDvoh9MAhFM-TiYpj6NRmI-5riw3iC2K)  

컬럼이 많은 테이블의 경우에는 로우체이닝과 로우마이그레이션이 많아지면서 데이터베이스 메모리에서 불필요한 I/O 가 많이 발생하여 성능이 저하된다.

- 로우체이닝 (Row Chaining) : 로우 길이가 너무 길어 데이터 블록 하나에 모두 저장되지 않고, 두 개 이상의 블록에 걸쳐 하나의 로우가 저장되어 있는 형태  
- 로우마이그레이션 (Row Migration) : 데이터 블록에서 수정이 발생하면 수정된 데이터를 해당 데이터 블록에서 저장하지 못하고 다른 블록의 빈 공간을 찾아 저장하는 방식  




### 2. 한 테이블에 많은 수의 컬럼을 가지고 있는 경우  

많은 컬럼을 가지고 있는 테이블에 대해서는 트랜잭션이 발생할 때 어떤 컬럼에 대해 집중적으로 발생하는지 분석하여 테이블을 쪼개어 주면 디스크 I/O 가 감소하면서 성능이 개선된다.  

![sqld-1-2-21](https://drive.google.com/uc?id=1JQYipnO9QaPRvNmutcL__2RzBQVc1nR0)  

[그림 I-2-21] 을 보면, 도서정보 테이블에서 전자출판유형과 대체제품에 대한 트랜잭션이 독립적으로 발생되는 경우가 많아 1:1 관계로 분리하였다. 분리된 테이블은 디스크에 적어진 컬럼이 저장되므로 로우마이그레이션과 로우체이닝이 감소할 수 있다. 따라서 동일한 SQL 구문에 대해서도 디스크 I/O 가 줄어들어 성능이 개선된다.  




### 3. 대량 데이터 저장 및 처리로 인한 성능  

테이블에 많은 양의 데이터가 예상될 경우 파티셔닝을 적용하거나 PK에 의해 테이블을 분할하는 방법을 적용할 수 있다. 오라클의 경우 RANGE PARTITION (범위), LIST PARTITION (특정값 지정), HASH PARTITION (해쉬 적용), COMPOSITE PARTITION (범위와 해쉬의 복합) 등이 가능하다.  


#### 1) RANGE PARTITION 적용  

가장 많이 사용하는 파티셔닝의 기준이 RANGE PARTITION 이다. 대상 테이블이 날짜 또는 숫자값으로 분리가 가능하고, 각 영역별로 트랜잭션이 분리된다면 RANGE PARITITION 을 적용한다. 또한 데이터 보관 주기에 따라 테이블에서 데이터를 쉽게 지울 수 있기 때문에 테이블 관리가 용이하다.  

![sqld-1-2-23](https://drive.google.com/uc?id=12SQZStrQkVehLhmKcFO3sbr0BQYI2mQF)  

[그림 I-2-23] 에서는 요금 테이블의 PK인 요금일자의 년+월 을 이용하여 12개의 파티션 테이블을 만들었다. SQL 문장을 처리할 때는 마치 하나의 테이블처럼 보이지만, DBMS 내부적으로는 SQL WHERE 절에 비교된 요금일자에 의해 각 파티션 테이블을 찾아가기 때문에 성능이 개선될 수 있다.  


#### 2) LIST PARTITION 적용  

핵심적인 코드값 등으로 PK가 구성된 경우 그 값에 의해 파티셔닝 되는 LIST PARTITION 을 적용할 수 있다.  

![sqld-1-2-24](https://drive.google.com/uc?id=11IlNGbfPX46QUmB5msc34NQcAO74TyvJ)  

[그림 I-2-24] 에서는 지역을 나타내는 PK 사업소코드 별로 LIST PARTITION 을 적용하였다.  


#### 3) HASH PARTITION 적용  

기타 HASH PARTITION 은 지정된 HASH 조건에 따라 알고리즘이 적용되어 테이블이 분리되며, 설계자는 테이블 데이터가 정확히 어떤 기준으로 분리되는지 알 수 없다.  

데이터량이 대용량이 되면 파티셔닝 적용은 필수적으로 파티셔닝 기준을 나눌 수 있는 조건에 따라 적절한 파티셔닝 방법을 선택하여 성능을 향상시키도록 한다.  




### 4. 테이블에 대한 수평분할/수직분할의 절차  

⑴ 데이터 모델링을 완성한다.  
⑵ 데이터베이스 용량을 산정한다. (어느 테이블의 데이터 양이 대용량이 되는지 분석하는 것)  
⑶ 대량 데이터가 처리되는 테이블에 대해 트랜잭션 처리 패턴을 분석한다.  
⑷ 컬럼 단위로 집중화 된 처리가 발생하는지, 로우 단위로 발생하는지 분석하여 집중화 된 단위로 테이블을 분리하는 것을 검토한다.  




### Ⅱ. 데이터베이스 구조와 성능  

### 1. 슈퍼타입/서브타입 모델의 성능고려 방법  

#### 1) 슈퍼/서브 타입 데이터 모델의 개요  

Extended ER 모델이라고 부르는 이른바 슈퍼/서브 타입 데이터 모델은 업무를 구성하는 데이터를 공통점과 차이점의 특징을 고려하여 효과적으로 표현할 수 있다. 즉, 공통의 부분은 슈퍼타입으로 모델링하고, 공통으로부터 상속받아 다른 엔터티와 차이가 있는 속성에 대해서는 별도의 서브엔터티로 구분하여 업무의 모습을 정확하게 표현하면서 물리적 데이터 모델로 변환할 때 선택의 폭을 넓힐 수 있는 장점이 있다.  
슈퍼/서브 타입 데이터 모델은 논리적 데이터 모델에서 이용되는 형태이며, 분석/설계 단계를 구분하자면 분석 단계에서 많이 쓰는 모델이다. 따라서 물리적 데이터 모델을 설계하는 단계에서는 슈퍼/서브 타입 데이터 모델을 일정한 기준에 의해 변환해야 한다. 아무런 기준 없이 변환하는 것 자체로 성능이 저하될 수 있다.  


#### 2) 슈퍼/서브 타입 데이터 모델의 변환  

슈퍼/서브 타입에 대한 변환을 잘못하면 성능이 저하되는 이유는 트랜잭션 특성을 고려하지 않고 테이블이 설계되었기 때문이다.  

⑴ 트랜잭션은 항상 일괄로 처리하는데, 테이블은 개별로 유지되어 Union 연산에 의해 성능이 저하될 수 있다.  
⑵ 트랜잭션은 항상 서브타입 개별로 처리하는데, 테이블은 하나로 통합되어 있어 불필요하게 많은 양의 데이터가 집약되어 성능이 저하될 수 있다.  
⑶ 트랜잭션은 항상 슈퍼+서브 타입을 공통으로 처리하는데, 개별로 유지되어 있거나 하나의 테이블로 집약되어 있어 성능이 저하되는 경우가 있다.  

이러한 경우로 인해 성능이 중요한 트랜잭션이 빈번하게 처리되는 기준에 따라 테이블을 설계해야 성능저하를 예방할 수 있다. 슈퍼/서브 타입을 성능을 고려한 물리적 데이터 모델로 변환하는 기준은 데이터 양과 해당 테이블에 발생되는 트랜잭션의 유형에 따라 결정된다.  

![sqld-1-2-25](https://drive.google.com/uc?id=1IwWjbM2hkkR1P6hZ35B-CqlP7xQ5AihE)  

데이터의 양이 소량이 경우 성능에 영향을 미치지 않기 때문에 데이터 처리의 유연성을 고려하여 가급적 1:1 관계를 유지하는 것이 바람직하다.  그러나 데이터의 용량이 많거나, 해당 업무적 특징이 성능에 민감한 경우는 트랜잭션이 해당 테이블에 어떻게 발생되는지에 따라 상황에 맞게 변환해야 한다.  


#### 3) 슈퍼/서브 타입 데이터 모델의 변환 기술  

논리적 데이터 모델에서 설계한 슈퍼/서브 타입 모델을 물리적 데이터로 전환할 떄 주로 어떤 유형의 트랜잭션이 발생하는지 검증해야 한다. 데이터의 양이 작고, 운영중에도 증가하지 않는다면 하나의 테이블로 묶어도 좋지만, 데이터량이 많고 지속적으로 증가한다면 슈퍼/서브 타입에 대해 물리적 데이터 모델로 변환하는 세 가지 유형에 대해 세심하게 적용해야 한다.  

⑴ 개별로 발생되는 트랜잭션에 대해 개별 테이블로 구성  

업무적으로 발생되는 트랜잭션이 슈퍼타입과 서브타입 각각에 대해 발생하는 것이다.  

![sqld-1-2-27](https://drive.google.com/uc?id=1k3CzSgI_Y9O6GrX8vSCtulNH9h_fu7sz)  

[그림 I-2-27] 은 슈퍼타입 테이블인 당사자 정보를 미리 조회하고 원하는 내용을 클릭하면 거기에 따라서 서브타입인 세부정보를 조회하는 형식이다. 위와 같이 각각에 대해 독립적으로 트랜잭션이 발생된다면, 슈퍼/서브 타입에 꼭 필요한 속성만 가지게 하기 위해 모두 분리하여 1:1 관계를 갖도록 한다.  


⑵ 슈퍼+서브 타입에 대해 발생되는 트랜잭션은 슈퍼+서브 타입 테이블로 구성  

![sqld-1-2-28](https://drive.google.com/uc?id=1nVr_pjU1PAqEkCsl3o2pC1xpaGSgEBRb)  

[그림 I-2-28] 은 슈퍼타입과 서브타입이 모두 하나의 테이블로 통합되어 있는 경우, 예를 들어 대리인에 대한 데이터만 개별적인 처리가 필요하다면 전체 데이터를 모두 읽어 처리하는 경우가 발생할 수 있다. 이렇게 슈퍼타입과 서브타입을 묶어 트랜잭션이 발생하는 업무 특징을 가지고 있을 때는 슈퍼타입+각서브타입을 하나로 묶어 별도의 테이블로 구성하는 것이 효율적이다.  


⑶ 전체를 하나로 묶어 트랜잭션이 발생할 때는 하나의 테이블로 구성  

개별로 분리되어 있는 테이블의 각각의 데이터를 항상 통합하여 처리한다고 하면, 불필요한 조인을 유발하거나 불필요한 UNION ALL과 같은 SQL 구문이 작성되어 성능이 저하된다. 비록 슈퍼타입과 서브타입의 테이블을 하나로 묶었을 때 각각의 속성별로 제약사항(NULL여부, 기본값, 체크값)을 정확하게 지정하지 못할지라도 대용량이고 성능 향상이 필요하다면 하나의 테이블로 묶어서 만들어 준다.  


#### 4) 슈퍼/서브 타입 데이터 모델의 변환 타입 비교  

![sqld-i-2-4](https://drive.google.com/uc?id=1EVvceyo699Zz-DPf9o9GofWuMG6xmbp5)  

[표 I-2-4]와 같이 각 성능이 좋을 수도 나쁠 수도 있기 때문에 변환모델의 선택은 철저하게 데이터베이스에 발생되는 트랜잭션의 유형에 따라 선택해야 한다.  




### 2. 인덱스 특성을 고려한 PK/FK 데이터베이스 성능 향상  

#### 1) PK/FK 컬럼 순서와 성능 개요  

⑴ PK/FK 컬럼 순서와 성능 개요 데이터를 조회할 때 가장 효과적으로 처리될 수 있도록 접근 경로를 제공하는 오브젝트는 인덱스이다. 일반적으로 데이터베이스 테이블에서는 균형 잡힌 트리구조의 B*Tree 구조를 많이 사용한다. 이 특징에 따라 설계에 반영해야 할 요소에 대해서 반드시 알고있어야 좋은 데이터 모델을 만들어 낼 수 있다.  
PK/FK 설계는 데이터를 접근할 때 경로를 제공하는 성능 측면에서 중요한 의미를 가지고 있기 때문에 설계 단계 말에 컬럼의 순서를 조정할 필요가 있다. 성능 저하 현상의 많은 부분이 복합식별자인 경우 PK순서에 대해 고려하지 않고 데이터 모델링을 한 경우에 해당된다. 특히 물리적 데이터 모델링 단계에서는 스스로 생성된 PK 순서 이외에 다른 엔터티로부터 상속 받아 발생되는 PK 순서까지 항상 주의하여 표시하도록 해야 한다.  
PK 순서는 인덱스를 가장 효율적으로 이용할 수 있도록 지정해야 한다. 앞쪽에 위치한 속성 값이 '=' 또는 'BETWEEN', '<>' 가 와야 인덱스를 이용할 수 있게 된다.  
데이터 모델링 때 결정한 PK 순서와 다르게 DDL 문장을 통해 생성할 수 있다. 그러나 대부분 유지보수의 용이성을 위해 데이터 모델의 PK 순서에 따라 PK를 생성한다.  


#### 2) PK 컬럼의 순서에 따른 성능 저하 이유  

인덱스의 정렬 구조로 인해 데이터에 접근하는 트랜잭션의 특징에 효율적이지 않은 인덱스가 생성되어 인덱스의 범위를 넓게 이용하거나, Full Scan 을 유발하여 성능이 저하된다.  




### 3. 물리적인 테이블에 FK 제약이 걸리지 않은 경우 인덱스 미생성으로 인한 성능 저하  

데이터 모델 관계에 의해 상속받은 FK 속성들은 SQL WHERE 절에서 JOIN 조건으로 이용되는 경우가 많으므로 FK 인덱스를 생성하면 성능이 향상될 수 있다. 물리적 테이블에서 두 테이블 간에 FK 참조 무결성 관계가 결려있지 않은 경우 상속받은 컬럼에 대해 인덱스를 생성하지 않기 때문에 FULL TABLE SCAN 이 발생되어 성능이 저하될 수 있다.  
그러므로 물리적인 테이블에 FK 제약 걸었을 때는 반드시 FK인덱스를 생성하고, 트랜잭션에 의해 거의 활용되지 않았을 때에만 FK 인덱스를 지우는 방법으로 하는 것이 적절한 방법이 된다.  
