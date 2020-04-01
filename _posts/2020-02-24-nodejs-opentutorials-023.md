---
layout: post
title: "[nodejs] 030# MySQL ⑻ DB 설정정보 분리"
tags: [nodejs, opentutorials, webapp, MySQL]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js > Node.js - MySQL ([https://opentutorials.org/course/3347](https://opentutorials.org/course/3347))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ Node.js 웹앱에서 데이터베이스의 설정 정보를 별도의 파일로 분리하는 방법을 학습합니다.  

### 1. lib 폴더 내부에 db.js 파일을 생성합니다.  

#### 1) main.js 파일에 작성된 db 객체에 대한 코드를 lib/db.js 파일로 옮겨옵니다.  

- main.js  

```diff
  ...
  var sanitizeHtml = require('sanitize-html');
- var mysql = require('mysql');
- var db = mysql.createConnection({
-   host:'localhost',
-   user:'TESTUSER',
-   password:'1234',
-   database:'TESTDB'
- });
- db.connect();

  var app = http.createServer(function(request,response) {
    ...
```

- lib/db.js  

```javascript
var mysql = require('mysql');
var db = mysql.createConnection({
  host     : 'localhost',
  user     : 'TESTUSER',
  password : '1234',
  database : 'TESTDB'
});
db.connect();
```

#### 2) lib/db.js 파일의 db 객체를 main.js 에서 사용할 수 있도록 모듈로 export 합니다.  

- lib/db.js  

```diff
  var mysql = require('mysql');
  var db = mysql.createConnection({
    host     : 'localhost',
    user     : 'TESTUSER',
    password : '1234',
    database : 'TESTDB'
  });
  db.connect();
+ module.exports = db;
```

### 2. main.js 파일에 db 모듈을 import 합니다.  

#### 1) lib/db.js 모듈을 추가합니다.  

- main.js  

```diff
  ...
  var sanitizeHtml = require('sanitize-html');
+ var db = require('./lib/db.js');

  var app = http.createServer(function(request,response) {
    ...
```

#### 2) 사용하지 않는 모듈은 삭제합니다.  

- main.js  

```diff
  var http = require('http');
- var fs = require('fs');
  var url = require('url');
  var qs = require('querystring');
  var template = require('./lib/template.js');
- var path = require('path');
- var sanitizeHtml = require('sanitize-html');
  var db = require('./lib/db.js');

  var app = http.createServer(function(request,response) {
    ...
```

### 3. DB 설정정보가 들어있는 lib/db.js 파일은 버전관리에서 제외합니다.  

#### 1) Github 버전관리에서 제외할 파일을 설정하기 위해 .gitignore 파일을 생성합니다.  

-- .gitignore 파일에 버전관리를 하지 않을 파일 lib/db.js 을 추가합니다.  

- .gitignore  

```bash
# Database config files
lib/db.js
```

#### 2) 추후 DB 설정이 필요한 경우 정보만 입력하여 사용할 수 있도록 템플릿 파일을 생성합니다.  

- lib/db.template.js  

```javascript
var mysql = require('mysql');
var db = mysql.createConnection({
  host     : '',
  user     : '',
  password : '',
  database : ''
});
db.connect();
module.exports = db;
```

### 4. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/334053dc4dfff2edf315b60929e78ab351c54b64))  

{% highlight javascript linenos %}
var http = require('http');
var url = require('url');
var qs = require('querystring');
var template = require('./lib/template.js');
var db = require('./lib/db.js');

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
