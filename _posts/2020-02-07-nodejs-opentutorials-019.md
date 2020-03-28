---
layout: post
title: "[nodejs] 026# MySQL ⑷ App - 글 생성"
tags: [nodejs, opentutorials, webapp, MySQL]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js > Node.js - MySQL ([https://opentutorials.org/course/3347](https://opentutorials.org/course/3347))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ MySQL 모듈을 이용해서 Node.js 홈페이지에 글을 작성하는 방법을 학습합니다.  

### 1. main.js 파일의 '/create' 처리 코드를 수정합니다.  

#### 1) 파일 목록을 가져오는 함수 readdir 를 삭제하고, 데이터베이스 목록을 조회하는 쿼리를 추가합니다.  

- 변수 filelist 를 삭제하고, 쿼리 결과가 담긴 변수 topics 를 사용합니다.  

```diff
var app = http.createServer(function(request,response) {
  ...
  if(pathname == '/') {
    ...
  } else if (pathname == '/create') {
-   fs.readdir('./data', 'utf8', function(error, filelist) {
+   db.query('SELECT * FROM topic', function(error, topics) {
      var title = 'WEB - Create';
-     var list = template.list(filelist);
+     var list = template.list(topics);
      var html = template.HTML(title, list, `
          ...
        `, '');
      response.writeHead(200);
      response.end(html);
    })
  } else if (pathname == '/create_process') {
    ...
```

### 2. main.js 파일의 '/create_process' 처리 코드를 수정합니다.  

#### 1) 파일을 생성하는 함수 writeFile 를 삭제하고, 데이터베이스를 입력하는 쿼리를 추가합니다.  

- 쿼리 실행에서 에러가 발생한 경우 처리를 위한 코드도 함께 추가합니다.  

- 쿼리 실행 콜백함수의 writeHead 에서 지정하는 Location 을 현재 입력된 데이터의 id 로 변경합니다.  
-- 콜백 함수의 결과 객체에서 'insertId' 로 접근하면 현재 입력된 데이터의 id 를 가져올 수 있습니다.  
-- 참고자료 ([npm > mysql > Getting the id of an inserted row](https://www.npmjs.com/package/mysql#getting-the-id-of-an-inserted-row))  

```diff
var app = http.createServer(function(request,response){
  ...
  if(pathname == '/') {
    ...
  } else if (pathname == '/create') {
    ...
  } else if (pathname == '/create_process') {
    var body = '';
    request.on('data', function(data) {
      body = body + data;
    })
    request.on('end', function() {
      var post = qs.parse(body);
-     var title = post.title;
-     var description = post.description;
-     fs.writeFile(`data/${title}`, description, 'utf8', function(err) {
+     db.query(
+       `INSERT INTO topic (title, description, created, author_id) VALUES (?, ?, NOW(), ?);`,
+       [post.title, post.description, 1], function(error, result) {
+       if(error) throw error;
-       response.writeHead(302, {Location: `/?id=${title}`});
+       response.writeHead(302, {Location: `/?id=${result.insertId}`});
        response.end();
      })
    })
  } else if (pathname == '/update') {
    ...
```

### 3. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/132a319ff41c7bb7004fccdb78eedc40edd0ce4d))  

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
    fs.readdir('./data', function(error, filelist) {
      var filteredId = path.parse(queryData.id).base;
      fs.readFile(`data/${filteredId}`, 'utf8', function(err, description) {
        var title = queryData.id;
        var list = template.list(filelist);
        var html = template.HTML(title, list,
          `<form action="/update_process" method="post">
             <input type="hidden" name="id" value="${title}">
             <p><input type="text" name="title" placeholder="title" value="${title}"></p>
             <p><textarea name="description" placeholder="description">${description}</textarea></p>
             <p><input type="submit"></p>
           </form>
          `, `<a href="/create">Create</a> <a href="/update?id=${title}">Update</a>`);
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
      var id = post.id;
      var title = post.title;
      var description = post.description;
      fs.rename(`data/${id}`, `data/${title}`, function(error){
        fs.writeFile(`data/${title}`, description, 'utf8', function(err) {
          response.writeHead(302, {Location: `/?id=${title}`});
          response.end();
        })
      })
    })
  } else if (pathname == '/delete_process') {
    var body = '';
    request.on('data', function(data) {
      body = body + data;
    })
    request.on('end', function() {
      var post = qs.parse(body);
      var id = post.id;
      var filteredId = path.parse(id).base;
      fs.unlink(`data/${filteredId}`, function(error){
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

### 4. 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  

#### 1) 'Create' 버튼을 클릭하여 데이터를 입력합니다.  

![nodejs_mysql_026_001](https://drive.google.com/uc?id=1QLF3L19p6irdk2uXoDEri9PORB85ugsF)  

#### 2) 입력한 데이터가 정상적으로 생성된 것을 확인할 수 있습니다.  

![nodejs_mysql_026_002](https://drive.google.com/uc?id=17o-0KmZqECxnVmP73EVfwmGTxFZ-ti6l)  
