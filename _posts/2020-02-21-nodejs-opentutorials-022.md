---
layout: post
title: "[nodejs] 029# MySQL ⑺ JOIN 을 이용한 데이터 활용"
tags: [nodejs, opentutorials, webapp, MySQL, JOIN]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js > Node.js - MySQL ([https://opentutorials.org/course/3347](https://opentutorials.org/course/3347))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  


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

-- Table structure for table `author`
CREATE TABLE `author` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(20) NOT NULL,
  `profile` varchar(200) DEFAULT NULL,
  PRIMARY KEY (`id`)
);

-- Dumping data for table `author`
INSERT INTO `author` VALUES (1,'egoing','developer');
INSERT INTO `author` VALUES (2,'duru','database administrator');
INSERT INTO `author` VALUES (3,'taeho','data scientist, developer');
```
---

#### ◇ MySQL 의 JOIN 을 이용해서 데이터를 조회, 입력, 수정하는 방법을 학습합니다.  

### 1. 화면에서 author 정보를 입력/수정할 수 있도록 template.js 파일을 수정합니다.  

#### ● lib/template.js  
-- 반복문을 이용하여 select 박스를 생성합니다.  
-- authors 목록 중 파라미터로 들어온 author_id 와 일치하는 값이 있다면 selected 상태로 설정합니다.  

```diff
  module.exports = {
    HTML:function(title, list, body, control) {
      ...
    }, list:function(topics) {
      ...
+   }, authorSelect:function(authors, author_id) {
+     var tag = '';
+     var i = 0;
+     while(i < authors.length) {
+       var selected = '';
+       if(authors[i].id === author_id) {
+         selected = ' selected';
+       }
+       tag += `<option value="${authors[i].id}"${selected}>${authors[i].name}</option>`;
+       i++;
+     }
+     return `<select name="author">${tag}</select>`;
    }
  }
```

### 2. main.js 파일의 topic 상세조회 코드를 수정합니다.  

- topic 상세 조회 쿼리에 author 테이블을 JOIN 하는 구문을 추가하여 두 테이블을 함께 조회합니다.  

- 조회된 author 정보가 화면에 출력되도록 html 객체를 수정합니다.  

```diff
var app = http.createServer(function(request,response) {
  ...
  if(pathname == '/') {
    if(queryData.id == undefined) {
      ...
    } else {
      db.query('SELECT * FROM topic', function (error, topics) {
-       db.query(`SELECT * FROM topic WHERE id=?`, [queryData.id], function (error, topic) {
+       db.query(`SELECT * FROM topic LEFT JOIN author ON topic.author_id=author.id WHERE topic.id=?`, [queryData.id], function (error, topic) {
          if(error) throw error;
          var title = topic[0].title;
          var description = topic[0].description;
          var list = template.list(topics);
          var html = template.HTML(title, list,
-           `<h2>${title}</h2>${description}`,
+           `<h2>${title}</h2>${description}<p>by ${topic[0].name}</p>`,
            `<a href="/create">Create</a> <a href="/update?id=${queryData.id}">Update</a>
             <form action="delete_process" method="post">
               <input type="hidden" name="id" value="${queryData.id}">
               <input type="submit" value="Delete">
             </form>
            `);
          response.writeHead(200);
          response.end(html);
        })
      })
    }
  } else if (pathname == '/create') {
    ...
```

### 3. main.js 파일의 topic 데이터 입력 부분을 수정합니다.  

#### 1) '/create' 처리 코드를 수정합니다.  

- author 목록을 조회하는 쿼리를 추가합니다.  

- 조회된 author 목록이 화면에 출력되도록 html 객체에 추가합니다.  

```diff
var app = http.createServer(function(request,response) {
  ...
  if(pathname == '/') {
    ...
  } else if (pathname == '/create') {
    db.query('SELECT * FROM topic', function(error, topics) {
+     db.query('SELECT * FROM author', function(error, authors) {
        var title = 'Create';
        var list = template.list(topics);
        var html = template.HTML(title, list,
          `<form action="/create_process" method="post">
             <p><input type="text" name="title" placeholder="title"></p>
             <p><textarea name="description" placeholder="description"></textarea></p>
+            <p>${template.authorSelect(authors)}</p>
             <p><input type="submit"></p>
           </form>
          `, ``);
        response.writeHead(200);
        response.end(html);
+     })
    })
  } else if (pathname == '/create_process') {
    ...
```

#### 2) '/create_process' 처리 코드를 수정합니다.  

- topic 테이블 INSERT 쿼리에 화면에서 입력한 author 정보를 추가합니다.  

```diff
var app = http.createServer(function(request,response) {
  ...
  if(pathname == '/') {
    ...
  } else if (pathname == '/create_process') {
    ...
    request.on('end', function() {
      var post = qs.parse(body);
      db.query(`
        INSERT INTO topic (title, description, created, author_id) VALUES (?, ?, NOW(), ?);`,
-         [post.title, post.description, 1], function(error, result) {
+         [post.title, post.description, post.author], function(error, result) {
          if(error) throw error;
          response.writeHead(302, {Location: `/?id=${result.insertId}`});
          response.end();
        }
      )
    })
  } else if (pathname == '/update') {
    ...
```

### 4. main.js 파일의 topic 데이터 수정 부분을 수정합니다.  

#### 1) '/update' 처리 코드를 수정합니다.  

- author 목록을 조회하는 쿼리를 추가합니다.  

- 조회된 author 목록이 화면에 출력되도록 html 객체에 추가합니다.  

```diff
var app = http.createServer(function(request,response) {
  ...
  if(pathname == '/') {
    ...
  } else if (pathname == '/update') {
    db.query('SELECT * FROM topic', function (error, topics) {
      db.query(`SELECT * FROM topic WHERE id=?`, [queryData.id], function (error, topic) {
        if(error) throw error;
+       db.query('SELECT * FROM author', function(error, authors) {
          var list = template.list(topics);
          var html = template.HTML(topic[0].title, list,
            `<form action="/update_process" method="post">
               <input type="hidden" name="id" value="${topic[0].id}">
               <p><input type="text" name="title" placeholder="title" value="${topic[0].title}"></p>
               <p><textarea name="description" placeholder="description">${topic[0].description}</textarea></p>
+              <p>${template.authorSelect(authors, topic[0].author_id)}</p>
               <p><input type="submit"></p>
             </form>
            `, `<a href="/create">Create</a> <a href="/update?id=${topic[0].id}">Update</a>`);
          response.writeHead(200);
          response.end(html);
+       })
      })
    })
  } else if (pathname == '/update_process') {
    ...
```

#### 2) '/update_process' 처리 코드를 수정합니다.  

- topic 테이블 UPDATE 쿼리에 화면에서 입력한 author 정보를 추가합니다.  

```diff
var app = http.createServer(function(request,response) {
  ...
  if(pathname == '/') {
    ...
  } else if (pathname == '/update_process') {
    ...
    request.on('end', function(){
      var post = qs.parse(body);
-     db.query(`UPDATE topic SET title=?, description=? WHERE id=?`, [post.title, post.description, post.id], function(error, result) {
+     db.query(`UPDATE topic SET title=?, description=?, author_id=? WHERE id=?`, [post.title, post.description, post.author, post.id], function(error, result) {
        if(error) throw error;
        response.writeHead(302, {Location: `/?id=${post.id}`});
        response.end();
      })
    })
  } else if (pathname == '/delete_process') {
    ...
```

### 5. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/8906a844a9695c7a870ccd55ef81a907f70b42a0))  

{% highlight javascript linenos %}
var http = require('http');
var fs = require('fs');
var url = require('url');
var qs = require('querystring');
var template = require('./lib/template.js');
var path = require('path');
var sanitizeHtml = require('sanitize-html');
var mysql = require('mysql');
var db = mysql.createConnection({
  host     : 'localhost',
  user     : 'TESTUSER',
  password : '1234',
  database : 'TESTDB'
});
db.connect();

var app = http.createServer(function(request,response) {
  var _url = request.url;
  var queryData = url.parse(_url, true).query;
  var pathname = url.parse(_url, true).pathname;
  if(pathname == '/') {
    if(queryData.id == undefined) {
      db.query('SELECT * FROM topic', function(error, topics) {
        var title = 'Welcome';
        var description = 'Hello, Node.js';
        var list = template.list(topics);
        var html = template.HTML(title, list,
          `<h2>${title}</h2><p>${description}</p>`,
          `<a href="/create">Create</a>`);
        response.writeHead(200);
        response.end(html);
      })
    } else {
      db.query('SELECT * FROM topic', function(error, topics) {
        db.query(`SELECT * FROM topic LEFT JOIN author ON topic.author_id=author.id WHERE topic.id=?`, [queryData.id], function (error, topic) {
          if(error) throw error;
          var title = topic[0].title;
          var description = topic[0].description;
          var list = template.list(topics);
          var html = template.HTML(title, list,
            `<h2>${title}</h2>${description}<p>by ${topic[0].name}</p>`,
            `<a href="/create">Create</a> <a href="/update?id=${queryData.id}">Update</a>
             <form action="delete_process" method="post">
               <input type="hidden" name="id" value="${queryData.id}">
               <input type="submit" value="Delete">
             </form>
            `);
          response.writeHead(200);
          response.end(html);
        })
      })
    }
  } else if (pathname == '/create') {
    db.query('SELECT * FROM topic', function(error, topics) {
      db.query('SELECT * FROM author', function(error, authors) {
        var title = 'Welcome - Create';
        var list = template.list(topics);
        var html = template.HTML(title, list,
          `<form action="/create_process" method="post">
             <p><input type="text" name="title" placeholder="title"></p>
             <p><textarea name="description" placeholder="description"></textarea></p>
             <p>${template.authorSelect(authors)}</p>
             <p><input type="submit"></p>
           </form>
          `, ``);
        response.writeHead(200);
        response.end(html);
      })
    })
  } else if (pathname == '/create_process') {
    var body = '';
    request.on('data', function(data) {
      body = body + data;
    })
    request.on('end', function() {
      var post = qs.parse(body);
      db.query(
        `INSERT INTO topic (title, description, created, author_id) VALUES (?, ?, NOW(), ?);`,
        [post.title, post.description, post.author], function(error, result) {
        if(error) throw error;
        response.writeHead(302, {Location: `/?id=${result.insertId}`});
        response.end();
      })
    })
  } else if (pathname == '/update') {
    db.query('SELECT * FROM topic', function (error, topics) {
      db.query(`SELECT * FROM topic WHERE id=?`, [queryData.id], function (error, topic) {
        if(error) throw error;
        db.query('SELECT * FROM author', function(error, authors) {
          var list = template.list(topics);
          var html = template.HTML(topic[0].title, list,
            `<form action="/update_process" method="post">
               <input type="hidden" name="id" value="${topic[0].id}">
               <p><input type="text" name="title" placeholder="title" value="${topic[0].title}"></p>
               <p><textarea name="description" placeholder="description">${topic[0].description}</textarea></p>
               <p>${template.authorSelect(authors, topic[0].author_id)}</p>
               <p><input type="submit"></p>
             </form>
            `, `<a href="/create">Create</a> <a href="/update?id=${topic[0].id}">Update</a>`);
          response.writeHead(200);
          response.end(html);
        })
      })
    })
  } else if (pathname == '/update_process') {
    var body = '';
    request.on('data', function(data) {
      body = body + data;
    })
    request.on('end', function() {
      var post = qs.parse(body);
      db.query(`UPDATE topic SET title=?, description=?, author_id=? WHERE id=?`, [post.title, post.description, post.author, post.id], function(error, result) {
        if(error) throw error;
        response.writeHead(302, {Location: `/?id=${post.id}`});
        response.end();
      })
    })
  } else if (pathname == '/delete_process') {
    var body = '';
    request.on('data', function(data) {
      body = body + data;
    })
    request.on('end', function() {
      var post = qs.parse(body);
      db.query('DELETE FROM topic WHERE id = ?', [post.id], function(error, result) {
        if(error) throw error;
        response.writeHead(302, {Location: `/`});
        response.end();
      })
    })
  } else {
    response.writeHead(404);
    response.end('Not found');
  }
});

app.listen(3000);
{% endhighlight %}
