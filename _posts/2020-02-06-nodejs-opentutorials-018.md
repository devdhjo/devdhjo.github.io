---
layout: post
title: "[nodejs] 025# MySQL ⑶ App - 글 상세조회"
tags: [nodejs, opentutorials, webapp, MySQL]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js > Node.js - MySQL ([https://opentutorials.org/course/3347](https://opentutorials.org/course/3347))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ MySQL 모듈을 이용해서 Node.js 홈페이지를 구현하는 방법을 학습합니다.  

### ☞ 시작하기 전에  

---

- Nodejs 에서 사용할 수 있는 mysql 모듈이 설치되어 있어야 합니다.  
-- [Dev.log > [nodejs] 023# MySQL ⑴ 모듈의 기본 사용방법](https://devdhjo.github.io/nodejs/2020/02/04/nodejs-opentutorials-016.html)  

---

### 1. main.js 파일을 수정합니다.  

#### 1) 파일 목록을 가져오는 함수 readdir 를 삭제하고, 데이터베이스 목록을 조회하는 함수를 추가합니다.  

```diff
var app = http.createServer(function(request,response){
  ...
  if(pathname == '/') {
    if(queryData.id == undefined) {
      ...
    } else {
-     fs.readdir('./data', 'utf8', function(error, filelist) {
+     db.query('SELECT * FROM topic', function(error, topics) {
        var filteredId = path.parse(queryData.id).base;
        ...
      })
    }
  } else if (pathname == '/create') {
    ...
```

#### 2) 파일 내용을 조회하는 함수 readFile 를 삭제하고, 데이터베이스 한 건을 조회하는 함수를 추가합니다.  

```diff
var app = http.createServer(function(request,response){
  ...
  if(pathname == '/') {
    if(queryData.id == undefined) {
      ...
    } else {
      db.query('SELECT * FROM topic', function(error, topics) {
        var filteredId = path.parse(queryData.id).base;
-       fs.readFile(`data/${filteredId}`, 'utf8', function(err, description) {
+       db.query(`SELECT * FROM topic WHERE id=?`, [queryData.id], function(error2, topic) {
          ...
        })
      })
    }
  } else if (pathname == '/create') {
    ...
```

#### 3) template 모듈에서 사용했던 기존 변수값을 데이터베이스에서 가져온 값으로 변경합니다.  

- sanitize 모듈을 위한 변수도 사용하지 않기 때문에 삭제합니다.  

```diff
var app = http.createServer(function(request,response){
  ...
  if(pathname == '/') {
    if(queryData.id == undefined) {
      ...
    } else {
      db.query('SELECT * FROM topic', function(error, topics) {
-       var filteredId = path.parse(queryData.id).base;
        db.query(`SELECT * FROM topic WHERE id=?`, [queryData.id], function(error2, topic) {
-         var title = queryData.id;
+         var title = topic[0].title;
-         var sanitizeTitle = sanitizeHtml(title);
-         var sanitizeDesc = sanitizeHtml(description, {
-           allowedTags:['h1']
-         });
+         var description = topic[0].description;
-         var list = template.list(filelist);
+         var list = template.list(topics);
          var html = template.HTML(title, list,
            ...
        })
      })
    }
  } else if (pathname == '/create') {
```

#### 4) template 모듈의 파라미터에 사용되는 변수를 3)과 같이 수정합니다.  

```diff
var app = http.createServer(function(request,response){
  ...
  if(pathname == '/') {
    if(queryData.id == undefined) {
      ...
    } else {
      db.query('SELECT * FROM topic', function(error, topics) {
        db.query(`SELECT * FROM topic WHERE id=?`, [queryData.id], function(error2, topic) {
          var title = topic[0].title;
          var description = topic[0].description;
          var list = template.list(topics);
-         var html = template.HTML(title, list, `<h2>${sanitizeTitle}</h2>${sanitizeDesc}`,
+         var html = template.HTML(title, list, `<h2>${title}</h2>${description}`,
-           `<a href="/create">Create</a> <a href="/update?id=${sanitizeTitle}">Update</a>
+           `<a href="/create">Create</a> <a href="/update?id=${queryData.id}">Update</a>
             <form action="delete_process" method="post">
-              <input type="hidden" name="id" value="${sanitizeTitle}">
+              <input type="hidden" name="id" value="${queryData.id}">
               <input type="submit" value="Delete">
             </form>`);
          response.writeHead(200);
          response.end(html);
        })
      })
    }
  } else if (pathname == '/create') {
```

### 2. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/ff425f9fe120cbe9c54488bbf6ec045eac9dc991))  

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
    fs.readdir('./data', 'utf8', function(err, filelist) {
      var title = 'Welcome - Create';
      var list = template.list(filelist);
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
      var title = post.title;
      var description = post.description;
      fs.writeFile(`data/${title}`, description, 'utf8', function(err) {
        response.writeHead(302, {Location: `/?id=${title}`});
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

### 5. 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  

![nodejs_mysql_025_001](https://drive.google.com/uc?id=1gnYHlKDxT7xfPsv7kjD_11GQ3vTjWWKU)  

-- 글을 조회하면 데이터베이스에서 가져온 내용이 보이는 것을 확인할 수 있습니다.  
