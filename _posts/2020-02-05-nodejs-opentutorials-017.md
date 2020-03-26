---
layout: post
title: "[nodejs] 024# MySQL ⑵ Node.js 프로젝트에 연동하기"
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

#### 1) 'mysql' 모듈을 임포트 합니다.  

```diff
  ...
  var path = require('path');
  var sanitizeHtml = require('sanitize-html');
+ var mysql = require('mysql');

  var app = http.createServer(function(request,response){
    var _url = request.url;
    ...
```

#### 2) 사용할 데이터베이스와 계정 정보를 입력하고 연결합니다.

```diff
  ...
  var sanitizeHtml = require('sanitize-html');
  var mysql = require('mysql');
+ var db = mysql.createConnection({
+   host:'localhost',
+   user:'TESTUSER',
+   password:'1234',
+   database:'TESTDB'
+ });
+ db.connect();

  var app = http.createServer(function(request,response) {
    var _url = request.url;
    ...
```

#### 3) 파일 목록을 가져오는 함수 readdir 는 삭제하고, DB 쿼리를 실행하는 함수를 추가합니다.  

```diff
  var app = http.createServer(function(request,response) {
    ...
    if(pathname == '/') {
      if(queryData.id == undefined) {
-       fs.readdir('./data', 'utf8', function(error, filelist) {
+       db.query('SELECT * FROM topic', function(error, topics) {
          var title = 'Welcome';
          var description = 'Hello, Node.js';
          ...
```

#### 4) 변수 filelist 는 삭제하고, 쿼리를 실행한 결과를 담은 객체 topics 를 사용합니다.  

```diff
  ...
  if(pathname == '/') {
    if(queryData.id == undefined) {
      db.query('SELECT * FROM topic', function(error, topics) {
        var title = 'Welcome';
        var description = 'Hello, Node.js';
-       var list = template.list(filelist);
+       var list = template.list(topics);
        var html = template.HTML(title, list,
          `<h2>${title}</h2>${description}`,
          `<a href="/create">Create</a>`);
        response.writeHead(200);
        response.end(html);
      });
    } else {
      ...
```

### 2. lib/template.js 파일을 수정합니다.  

#### 1) 변수 filelist 는 삭제하고, 쿼리를 실행한 결과를 담은 객체 topics 를 사용합니다.  

```diff
  module.exports = {
    HTML:function(title, list, body, control) {
      ...
-   }, list : function(filelist) {
+   }, list : function(topics) {
      var list = '<ul>';
      var i = 0;
-     while(i < filelist.length){
+     while(i < topics.length){
        list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
        i = i + 1;
      }
      list = list + '</ul>';
      return list;
    }
  }

```

#### 2) topics 의 id 값을 쿼리스트링에, title 값을 메인화면 목록으로 사용합니다.  

```diff
  module.exports = {
    HTML:function(title, list, body, control) {
      ...
    }, list : function(topics) {
      var list = '<ul>';
      var i = 0;
      while(i < topics.length){
-       list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
+       list = list + `<li><a href="/?id=${topics[i].id}">${topics[i].title}</a></li>`;
        i = i + 1;
      }
      list = list + '</ul>';
      return list;
    }
  }
```

- topics 는 객체이기 때문에, 그 자체를 사용하면 화면에는 'Object' 로 출력됩니다. 따라서 객체 안에 있는 값을 지정하여 사용하여야 원하는 값을 출력할 수 있습니다.  

- topics 를 콘솔에 출력해보면, 아래와 같이 id, title, description 등의 값을 가진 것을 확인할 수 있습니다.  
-- 이 중 필요한 값의 key 를 이용하여 접근하면 value 값을 출력할 수 있습니다.  

![nodejs_mysql_005](https://drive.google.com/uc?id=1CwWBAKSCWCqL4oIGqqMCGN73FpwZZidS)  

### 3. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/299fd16c70bd920d865b4aa0d0d27cf9efb5292f))  

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
      fs.readdir('./data', 'utf8', function(error, filelist) {
        var filteredId = path.parse(queryData.id).base;
        fs.readFile(`data/${filteredId}`, 'utf8', function(err, description) {
          var title = queryData.id;
          var sanitizeTitle = sanitizeHtml(title);
          var sanitizeDesc = sanitizeHtml(description, {
            allowedTags:['h1']
          });
          var list = template.list(filelist);
          var html = template.HTML(title, list,
            `<h2>${sanitizeTitle}</h2><p>${sanitizeDesc}</p>`,
            `<a href="/create">Create</a> <a href="/update?id=${sanitizeTitle}">Update</a>
             <form action="delete_process" method="post">
               <input type="hidden" name="id" value="${sanitizeTitle}">
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

![nodejs_mysql_006](https://drive.google.com/uc?id=1sNvkmJ-Utzu0t0attWh4E3OrEBEhQm61)  

-- 기존의 data 폴더에 있던 파일 목록이 아닌, 데이터베이스의 topic 테이블에서 목록을 조회하는 것을 확인할 수 있습니다.  
