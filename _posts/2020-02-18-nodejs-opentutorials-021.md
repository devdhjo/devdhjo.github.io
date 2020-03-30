---
layout: post
title: "[nodejs] 028# MySQL ⑹ App - 글 삭제"
tags: [nodejs, opentutorials, webapp, MySQL]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js > Node.js - MySQL ([https://opentutorials.org/course/3347](https://opentutorials.org/course/3347))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ MySQL 모듈을 이용해서 Node.js 홈페이지에 글을 삭제하는 방법을 학습합니다.  

### 1. main.js 파일의 '/delete_process' 처리 코드를 수정합니다.  

#### 1) 파일을 지우는 함수 unlink 를 삭제하고, 데이터베이스를 삭제하는 쿼리를 추가합니다.  

- 사용하지 않을 변수 id 와 filteredId 는 삭제합니다.  

- 쿼리 실행에서 에러가 발생한 경우 처리를 위한 코드도 함께 추가합니다.  

```diff
var app = http.createServer(function(request,response) {
  ...
  if(pathname == '/') {
    ...
  } else if (pathname == '/delete_process') {
    var body = '';
    request.on('data', function(data) {
      body = body + data;
    })
    request.on('end', function() {
      var post = qs.parse(body);
-     var id = post.id;
-     var filteredId = path.parse(id).base;
-     fs.unlink(`data/${filteredId}`, function(error) {
+     db.query('DELETE FROM topic WHERE id = ?', [post.id], function(error, result) {
+       if(error) throw error;
        response.writeHead(302, {Location: `/`});
        response.end();
      })
    })
  } else {
    ...
```

### 2. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/d9405080da6b1d9acb471038d8df89ded4403292))  

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
        db.query(`SELECT * FROM topic WHERE id=?`, [queryData.id], function(error2, topic) {
          var title = topic[0].title;
          var description = topic[0].description;
          var list = template.list(topics);
          var html = template.HTML(title, list,
            `<h2>${title}</h2><p>${description}</p>`,
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
      var title = 'Welcome - Create';
      var list = template.list(topics);
      var html = template.HTML(title, list,
        `<form action="/create_process" method="post">
           <p><input type="text" name="title" placeholder="title"></p>
           <p><textarea name="description" placeholder="description"></textarea></p>
           <p><input type="submit"></p>
         </form>
        `, ``);
      response.writeHead(200);
      response.end(html);
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
        [post.title, post.description, 1], function(error, result) {
        if(error) throw error;
        response.writeHead(302, {Location: `/?id=${result.insertId}`});
        response.end();
      })
    })
  } else if (pathname == '/update') {
    db.query('SELECT * FROM topic', function (error, topics) {
      db.query(`SELECT * FROM topic WHERE id=?`, [queryData.id], function (error, topic) {
        if(error) throw error;
        var list = template.list(topics);
        var html = template.HTML(topic[0].title, list,
          `<form action="/update_process" method="post">
             <input type="hidden" name="id" value="${topic[0].id}">
             <p><input type="text" name="title" placeholder="title" value="${topic[0].title}"></p>
             <p><textarea name="description" placeholder="description">${topic[0].description}</textarea></p>
             <p><input type="submit"></p>
           </form>
          `, `<a href="/create">Create</a> <a href="/update?id=${topic[0].id}">Update</a>`);
        response.writeHead(200);
        response.end(html);
      })
    })
  } else if (pathname == '/update_process') {
    var body = '';
    request.on('data', function(data) {
      body = body + data;
    })
    request.on('end', function() {
      var post = qs.parse(body);
      db.query(`UPDATE topic SET title=?, description=? WHERE id=?`, [post.title, post.description, post.id], function(error, result) {
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

### 3. 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  

#### 1) 삭제할 글을 조회합니다.  

![nodejs_mysql_028_001](https://drive.google.com/uc?id=1sMepUnPRMLMx8WHEZvvfKGX9EWJCbhYu)  

#### 2) 'Delete 버튼을 클릭하면 해당 글이 삭제되고, 메인 화면으로 돌아가는 것을 확인할 수 있습니다.  

![nodejs_mysql_028_002](https://drive.google.com/uc?id=1cddMvffp4mKy8sJ0ruI2I_g2RiIdIte0)  
