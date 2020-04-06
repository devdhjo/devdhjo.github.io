---
layout: post
title: "[nodejs] 032# MySQL ⑽ 저자 관리 - 목록 조회"
tags: [nodejs, opentutorials, webapp, MySQL]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js > Node.js - MySQL ([https://opentutorials.org/course/3347](https://opentutorials.org/course/3347))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ Node.js 웹앱에서 저자 목록을 조회하는 방법을 학습합니다.  

### 1. template.js 파일을 수정합니다.  

#### 1) HTML 모듈에 author 목록 조회 화면으로 가는 버튼을 추가합니다.  

```diff
  HTML : function(title, list, body, control) {
    return `
      <!doctype html>
      <html>
      <head>
        <title>WEB1 - ${title}</title>
        <meta charset="utf-8">
      </head>
      <body>
        <h1><a href="/">WEB</a></h1>
+       <a href="/author">author</a>
        ${list}
        ${control}
        ${body}
      </body>
      </html>
      `;
  }, list : function(topics) {
    ...
```

#### 2) 조회 된 author 목록을 화면에 보여주기 위한 모듈을 추가합니다.  

```diff
  module.exports = {
    HTML : function(title, list, body, control) {
      ...
    }, list : function(topics) {
      ...
    }, authorSelect : function(authors, author_id) {
      ...
+   }, authorTable : function(authors) {
+     var tag = '<table>';
+     var i = 0;
+     while(i < authors.length) {
+       tag += `
+         <tr>
+           <td>${authors[i].name}</td>
+           <td>${authors[i].profile}</td>
+           <td>Update</td>
+           <td>Delete</td>
+         </tr>`;
+       i++;
+     }
+     tag += '</table>';
+     return tag;
    }
  }
```

### 2. lib 폴더 내부에 author.js 파일을 생성합니다.  

#### 1) lib/topic.js 파일에 작성된 'home' 모듈의 코드를 lib/author.js 파일로 옮겨옵니다.  

-- author 모듈에서 필요한 template 모듈과 db 모듈도 함께 추가합니다.  

- lib/author.js  

```javascript
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
```

#### 2) author 파일의 home 모듈 내부에서 author 테이블을 조회하는 쿼리를 추가합니다.  

- lib/author.js  

```diff
  exports.home = function(request, response) {
    db.query('SELECT * FROM topic', function(error, topics) {
+     db.query('SELECT * FROM author', function(error, authors) {
        var title = 'Welcome';
        var description = 'Hello, Node.js';
        var list = template.list(topics);
        var html = template.HTML(title, list,
          `<h2>${title}</h2><p>${description}</p>`,
          `<a href="/create">Create</a>`);
        response.writeHead(200);
        response.end(html);
+     })
    })
  }
```

#### 3) 기존 화면 body는 삭제하고, 1번에서 template 파일에 추가했던 authorSelect 모듈을 불러옵니다.  

-- title 변수는 적절히 수정하고, 사용하지 않는 description 변수는 삭제합니다.  

- lib/author.js  

```diff
  exports.home = function(request, response) {
    db.query('SELECT * FROM topic', function(error, topics) {
      db.query('SELECT * FROM author', function(error, authors) {
+       var title = 'Author';
-       var title = 'Welcome';
-       var description = 'Hello, Node.js';
        var list = template.list(topics);
        var html = template.HTML(title, list,
-         `<h2>${title}</h2><p>${description}</p>`,
+         `${template.authorTable(authors)}`,
          `<a href="/create">Create</a>`);
        response.writeHead(200);
        response.end(html);
      })
    })
  }
```

### 3. main.js 파일에 '/author' 요청을 처리하는 함수를 추가합니다.  

- main.js  

```diff
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
+   } else if (pathname == '/author') {
+     author.home(request, response);
    } else {
      response.writeHead(404);
      response.end('Not found');
    }
  });
```

### 4. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\nodejs\webapp>node main.js
```

-- [수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/b41ebe1ba2c1ff87ae37ed66a2d56331759a469e)  

- main.js  

{% highlight javascript linenos %}
var http = require('http');
var url = require('url');
var topic = require('./lib/topic');
var author = require('./lib/author');

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
  } else if (pathname == '/author') {
    author.home(request, response);
  } else {
    response.writeHead(404);
    response.end('Not found');
  }
});

app.listen(3000);
{% endhighlight %}

- lib/author.js  

{% highlight javascript linenos %}
var template = require('./template');
var db = require('./db');

exports.home = function(request, response) {
  db.query('SELECT * FROM topic', function(error, topics) {
    db.query('SELECT * FROM author', function(error, authors) {
      var title = 'Author';
      var list = template.list(topics);
      var html = template.HTML(title, list,
        `${template.authorTable(authors)}`,
        `<a href="/create">Create</a>`);
      response.writeHead(200);
      response.end(html);
    })
  })
}
{% endhighlight %}

- lib/template.js

{% highlight javascript linenos %}
module.exports = {
  HTML : function(title, list, body, control) {
    return `
      <!doctype html>
      <html>
      <head>
        <title>WEB1 - ${title}</title>
        <meta charset="utf-8">
      </head>
      <body>
        <h1><a href="/">WEB</a></h1>
        <a href="/author">author</a>
        ${list}
        ${control}
        ${body}
      </body>
      </html>
      `;
  }, list : function(topics) {
    var list = '<ul>';
    var i = 0;
    while(i < topics.length) {
      list = list + `<li><a href="/?id=${topics[i].id}">${topics[i].title}</a></li>`;
      i = i + 1;
    }
    list = list + '</ul>';
    return list;
  }, authorSelect : function(authors, author_id) {
    var tag = '';
    var i = 0;
    while(i < authors.length) {
      var selected = '';
      if(authors[i].id === author_id) {
        selected = ' selected';
      }
      tag += `<option value="${authors[i].id}"${selected}>${authors[i].name}</option>`;
      i++;
    }
    return `<select name="author">${tag}</select>`;
  }, authorTable : function(authors) {
    var tag = '<table>';
    var i = 0;
    while(i < authors.length) {
      tag += `
        <tr>
          <td>${authors[i].name}</td>
          <td>${authors[i].profile}</td>
          <td>Update</td>
          <td>Delete</td>
        </tr>`;
      i++;
    }
    tag += '</table>';
    return tag;
  }
}
{% endhighlight %}

### 5. 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  

![nodejs_mysql_032](https://drive.google.com/uc?id=1sqmHFAi3xLULJK0TcHZ4scY1uQrxVIFx)  

-- author 버튼을 누르면 author 테이블 목록이 조회되는 것을 확인할 수 있습니다.  
