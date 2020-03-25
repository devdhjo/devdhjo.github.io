---
layout: post
title: "[nodejs] 023# MySQL ⑴ 모듈의 기본 사용방법"
tags: [nodejs, opentutorials, webapp, MySQL]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js > Node.js - MySQL ([https://opentutorials.org/course/3347](https://opentutorials.org/course/3347))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ Node.js 의 MySQL 모듈 사용방법을 학습합니다.  

### ☞ 시작하기 전에  

---

#### 1) MySQL 을 설치하고, 사용할 데이터베이스와 계정을 생성해야 합니다.  

(1) [Dev.log > [MySQL] 001# MySQL 설치하기 - Windows](https://devdhjo.github.io/mysql/2020/01/28/database-mysql-001.html)  
(2) [Dev.log > [MySQL] 002# Database 생성 및 권한부여](https://devdhjo.github.io/mysql/2020/01/29/database-mysql-002.html)  

-- 저는 위의 글에서 생성한 TESTDB 와 TESTUSER 를 사용하여 학습을 진행하겠습니다.  

#### 2) 테스트에 필요한 데이터를 입력합니다.  

```sql
-- Table structure for table `topic`
CREATE TABLE `topic` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `title` varchar(30) NOT NULL,
  `description` text,
  `created` datetime NOT NULL,
  `author_id` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
);

-- Dumping data for table `topic`
INSERT INTO `topic` VALUES (1,'MySQL','MySQL is...','2018-01-01 12:10:11',1);
INSERT INTO `topic` VALUES (2,'Oracle','Oracle is ...','2018-01-03 13:01:10',1);
INSERT INTO `topic` VALUES (3,'SQL Server','SQL Server is ...','2018-01-20 11:01:10',2);
INSERT INTO `topic` VALUES (4,'PostgreSQL','PostgreSQL is ...','2018-01-23 01:03:03',3);
INSERT INTO `topic` VALUES (5,'MongoDB','MongoDB is ...','2018-01-30 12:31:03',1);
```

---

### 1. npm 에서 제공하는 mysql 모듈을 설치합니다.  

- 참고자료 ([npm > mysql > install](https://www.npmjs.com/package/mysql#install))  

```bash
npm install mysql
```

![nodejs_mysql_001](https://drive.google.com/uc?id=1i3lbgsdfVb00ueESs7ekg2i-AqsCsHsL)  

- 저는 '--save' 옵션을 사용하였는데, ('-S' 와 동일한 명령어) 이 옵션은 모듈을 설치할 때 package.json 의 dependency 항목에 자동으로 추가하는 명령어입니다.  
-- 하지만 npm@5 부터는 '--save' 옵션이 기본이 되어, package.json 파일이 존재하는 상태에서는 'npm install 모듈명' 으로만 실행해도 설치된 모듈은 자동으로 dependency 항목에 추가됩니다.  
- '-g' 옵션을 사용하면 글로벌 패키지에 추가되어 해당 프로젝트 뿐만 아니라 다른 프로젝트에서도 해당 패키지를 사용할 수 있습니다.  

### 2. 모듈에서 제공하는 기본 예제로 파일을 생성합니다.  

- 참고자료 ([npm > mysql > introduction](https://www.npmjs.com/package/mysql#introduction))  

```javascript
var mysql      = require('mysql');
var connection = mysql.createConnection({
  host     : {호스트주소},
  user     : {계정이름},
  password : {비밀번호},
  database : {데이터베이스}
});

connection.connect();

connection.query({실행할 쿼리}, function (error, results, fields) {
  if (error) {
    console.log(error);
  }
  console.log({응답결과});
});

connection.end();
```

-- 저는 프로젝트에 contents 라는 폴더를 생성하고, 여기에 mysql.js 파일을 작성하였습니다.  
-- 쿼리에는 상단에서 생성한 topic 테이블을 조회하도록 입력하였습니다.  

#### ◇ contents/mysql.js  

-- [생성파일 보기 (devdhjo/nodejs_opentutorials)](https://github.com/devdhjo/nodejs_opentutorials/commit/74c20f7dabfee622177158d23980a0b64fe49f39)  

{% highlight javascript linenos %}
var mysql      = require('mysql');
var connection = mysql.createConnection({
  host     : 'localhost',
  user     : 'TESTUSER',
  password : '1234',
  database : 'TESTDB'
});

connection.connect();

connection.query('SELECT * FROM topic', function (error, results, fields) {
  if (error) {
    console.log(error);
  }
  console.log('The solution is: ', results);
});

connection.end();
{% endhighlight %}

### 3. 생성한 mysql.js 파일을 실행합니다.  


- window + R → cmd 실행  

```
D:\workspace\nodejs\webapp>node contents/mysql.js
```

![nodejs_mysql_004](https://drive.google.com/uc?id=1bOqjqI_BQehg7D0_O5mTbNcI1g7j_1hv)  

-- 입력한 쿼리의 실행 결과가 출력되는 것을 확인할 수 있습니다.  

#### ◇ 실행 시 'ER_NOT_SUPPORTED_AUTH_MODE' 오류가 발생하는 경우  

![nodejs_mysql_002](https://drive.google.com/uc?id=1Qrf0dgPQnd5maczqWOirxZB3eO6d9-TG)  

- MySQL 8.0 에서는 사용자 인증방법이 caching_sha2_password 만 사용됩니다.  
-- 따라서 강제로 mysql_native_password 를 사용하도록 변경하거나,  
-- caching_sha2_password 를 지원하는 connector 를 사용해야 합니다.  

- 새 버전에서 지원하지 않는 구버전의 인증 방법으로 바꾸는 것은 보안상 좋지 않은 방법이지만, 저는 학습을 쉽게 진행하기 위해 이 방법을 사용하였습니다.  

1) 기존 계정의 인증 방법을 변경  

```sql
ALTER USER '{계정이름}'@'localhost' IDENTIFIED WITH mysql_native_password BY '{비밀번호}';
```

2) 새 계정을 생성하여 구버전의 인증방법을 사용하도록 설정  

```sql
CREATE USER '{계정이름}'@'localhost' IDENTIFIED WITH mysql_native_password BY '{비밀번호}';
GRANT ALL PRIVILEGES ON {데이터베이스}.* TO '{계정이름}'@'localhost';
FLUSH PRIVILEGES;
```

- 저는 TESTUSER 계정의 인증 방법을 변경한 후 mysql.js 파일을 실행하니 정상 작동 되었습니다.  

```sql
ALTER USER 'TESTUSER'@'LOCALHOST' IDENTIFIED WITH mysql_native_password BY '1234';
```

![nodejs_mysql_003](https://drive.google.com/uc?id=1FIAK8PG3DTcsT7-JUlNveUBfTl58d2HV)  
