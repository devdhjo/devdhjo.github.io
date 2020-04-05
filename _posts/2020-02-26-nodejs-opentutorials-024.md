---
layout: post
title: "[nodejs] 031# MySQL ⑼ 로직의 모듈화"
tags: [nodejs, opentutorials, webapp, MySQL]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js > Node.js - MySQL ([https://opentutorials.org/course/3347](https://opentutorials.org/course/3347))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ Node.js 웹앱의 모듈을 이용하여 로직을 정리하는 방법을 학습합니다.  

#### ☞ [참고] 모듈 정의 방법  

-- 하나의 모듈만 제공하는 경우  

```javascript
module.exports = { }
```

-- 모듈을 통해 여러가지 함수를 제공하는 경우  

```javascript
exports.함수명 = { }
```

### 1. lib 폴더 내부에 topic.js 파일을 생성합니다.  

- main.js 파일에서 처리하고 있는 7가지 로직을 담을 모듈의 틀을 작성합니다.  

#### 1) main.js  

```javascript
var app = http.createServer(function(request,response) {
  ...
  if(pathname == '/') {
    if(queryData.id == undefined) {
      ...
    } else {
      ...
    }
  } else if (pathname == '/create') {
    ...
  } else if (pathname == '/create_process') {
    ...
  } else if (pathname == '/update') {
    ...
  } else if (pathname == '/update_process') {
    ...
  } else if (pathname == '/delete_process') {
    ...
  } else {
    response.writeHead(404);
    response.end('Not found');
  }
});
```

#### 2) lib/topic.js  

```javascript
exports.home = function(request, response) { }

exports.page = function(request, response) { }

exports.create = function(request, response) { }

exports.create_process = function(request, response) { }

exports.update = function(request, response) { }

exports.update_process = function(request, response) { }

exports.delete_process = function(request, response) { }
```

### 2. main.js 로직을 topic.js 모듈로 이동합니다.  

#### 1) main.js 파일의 메인페이지 처리 코드를 topic 모듈의 home 함수 내부로 가져옵니다.  

- main.js  

```diff
var app = http.createServer(function(request,response) {
  ...
  if(pathname == '/') {
    if(queryData.id == undefined) {
-     db.query('SELECT * FROM topic', function (error, topics) {
-       var title = 'Welcome';
-       var description = 'Hello, Node.js';
-       var list = template.list(topics);
-       var html = template.HTML(title, list,
-         `<h2>${title}</h2>${description}`,
-         `<a href="/create">Create</a>`);
-       response.writeHead(200);
-       response.end(html);
-     })
    } else {
      ...
```

- lib/topic.js  

```diff
  exports.home = function(request, response) {
+   db.query('SELECT * FROM topic', function (error, topics) {
+     var title = 'Welcome';
+     var description = 'Hello, Node.js';
+     var list = template.list(topics);
+     var html = template.HTML(title, list,
+       `<h2>${title}</h2>${description}`,
+       `<a href="/create">Create</a>`);
+     response.writeHead(200);
+     response.end(html);
+   })
  }
```

#### 2) main.js 파일의 메인페이지 처리 코드에서 topic 모듈의 home 함수를 호출합니다.  

- main.js  

```diff
var app = http.createServer(function(request,response) {
  ...
  if(pathname == '/') {
    if(queryData.id == undefined) {
+     topic.home(request, response);
    } else {
      ...
```

### 3. 동일한 방식으로 main.js 의 로직을 topic.js 의 각 함수 내부로 이동합니다.  

- 이 때, queryData 변수를 사용하는 로직에는 변수 선언도 함께 추가합니다.  
-- page, update 두 함수에서 필요합니다.  

#### 1) main.js  

```diff
var app = http.createServer(function(request,response) {
  ...
  if(pathname == '/') {
    ...
  } else if (pathname == '/update') {
-   db.query('SELECT * FROM topic', function(error, topics) {
-     ...
-   })
  } else if (pathname == '/update_process') {
    ...
```

#### 2) topic.js  

```diff
  exports.update = function(request, response) {
+   var queryData = url.parse(request.url, true).query;
+   db.query('SELECT * FROM topic', function (error, topics) {
+     ...
+   })
  }
```

### 4. 파일에서 정의한 모듈을 필요에 따라 수정합니다.  

#### 1) topic.js 에서 필요한 모듈을 추가합니다.  

- topic.js  

-- template.js 와 db.js 모듈의 topic.js 에 맞게 변경합니다.

```diff
+ var url = require('url');
+ var qs = require('querystring');
+ var template = require('./template');
+ var db = require('./db');

  exports.home = function(request, response) {
    ...
```

#### 2) main.js 에 topic 모듈을 추가하고, 사용하지 않는 모듈은 삭제합니다.  

- main.js  

```diff
  var http = require('http');
  var url = require('url');
- var qs = require('querystring');
- var template = require('./lib/template.js');
- var db = require('./lib/db.js');
+ var topic = require('./lib/topic');

  var app = http.createServer(function(request,response) {
    ...
```

### 5. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\nodejs\webapp>node main.js
```

-- [수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/8ad2b4f24dffc1722671c43c00b68dc44d3afba2)  

#### 1) main.js  

{% highlight javascript linenos %}
var http = require('http');
var url = require('url');
var topic = require('./lib/topic');

var app = http.createServer(function(request,response) {
  var queryData = url.parse(request.url, true).query;
  var pathname  = url.parse(request.url, true).pathname;
  if(pathname == '/') {
    if(queryData.id == undefined) {
      topic.home(request, response);
    } else {
      topic.page(request, response);
    }
  } else if (pathname == '/create') {
    topic.create(request, response);
  } else if (pathname == '/create_process') {
    topic.create_process(request, response);
  } else if (pathname == '/update') {
    topic.update(request, response);
  } else if (pathname == '/update_process') {
    topic.update_process(request, response);
  } else if (pathname == '/delete_process') {
    topic.delete_process(request, response);
  } else {
    response.writeHead(404);
    response.end('Not found');
  }
});

app.listen(3000);
{% endhighlight %}

#### 2) topic.js  

{% highlight javascript linenos %}
var url = require('url');
var qs = require('querystring');
var template = require('./template');
var db = require('./db');

exports.home = function(request, response) {
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
}

exports.page = function(request, response) {
  var queryData = url.parse(request.url, true).query;
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

exports.create = function(request, response) {
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
}

exports.create_process = function(request, response) {
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
}

exports.update = function(request, response) {
  var queryData = url.parse(request.url, true).query;
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
}

exports.update_process = function(request, response) {
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
}

exports.delete_process = function(request, response) {
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
}
{% endhighlight %}
