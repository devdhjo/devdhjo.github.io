---
layout: post
title: "[nodejs] 018# App ⑾ 글삭제 - 파일 삭제"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩 ([https://opentutorials.org/course/1](https://opentutorials.org/course/1)) 에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js ([https://opentutorials.org/course/3332](https://opentutorials.org/course/3332))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ FORM 을 이용하여 title 이 일치하는 파일을 삭제하는 방법을 학습합니다.  

### 1. 글을 삭제할 수 있도록 버튼을 생성합니다.  

- 다른 버튼처럼 a 태그를 이용하여 버튼을 생성한다면 링크 이동이 발생하기 때문에 FORM 태그를 이용합니다.  

- 삭제할 파일을 구분하기 위해 선택한 파일의 title 을 FORM 의 hidden 속성으로 저장합니다.

```diff
  var app = http.createServer(function(request,response){
    var _url = request.url;
    var queryData = url.parse(_url, true).query;
    var pathname = url.parse(_url, true).pathname;
    if(pathname == '/') {
      if(queryData.id == undefined) {
        ...
      } else {
        fs.readdir('./data', 'utf8', function(error, filelist){
          fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
            var title = queryData.id;
            var list = templateList(filelist);
            var template = templateHTML(title, list, `<h2>${title}</h2>${description}`,
-             `<a href="/create">Create</a> <a href="/update?id=${title}">Update</a>`);
+             `<a href="/create">Create</a> <a href="/update?id=${title}">Update</a>
+              <form action="delete_process" method="post">
+                <input type="hidden" name="id" value="${title}">
+                <input type="submit" value="Delete">
+              </form>
+             `);
            response.writeHead(200);
            response.end(template);
          });
        })
      }
    } else if (pathname == '/create') {
      ...
    }
  });
```


### 2. '/delete_process' 주소로 전송된 FORM 데이터를 처리하기 위한 코드를 추가합니다.  

- fs.unlink 함수를 이용하여 title 이 일치하는 파일을 삭제합니다.  
-- 참고자료 ([Node.js - fs.unlink(path, callback)](https://nodejs.org/dist/latest-v12.x/docs/api/fs.html#fs_fs_unlink_path_callback))  

```diff
  var app = http.createServer(function(request,response){
    ...
    if(pathname == '/') {
      ...
+   } else if (pathname == '/delete_process') {
+     var body = '';
+     request.on('data', function(data){
+       body = body + data;
+     });
+     request.on('end', function(){
+       var post = qs.parse(body);
+       var id = post.id;
+       fs.unlink(`data/${id}`, function(error){
+         response.writeHead(302, {Location: `/`});
+         response.end();
+       })
+     })
    } else {
      response.writeHead(404);
      response.end('Not found');
    }
  });
```

### 3. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\git\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/2bdbadf5641e69067108d16aba31d821c9fa7687))  

{% highlight javascript linenos %}
var http = require('http');
var fs = require('fs');
var url = require('url');
var qs = require('querystring');

function templateHTML(title, list, body, control) {
  return `
    <!doctype html>
    <html>
    <head>
      <title>WEB1 - ${title}</title>
      <meta charset="utf-8">
    </head>
    <body>
      <h1><a href="/">WEB</a></h1>
      ${list}
      ${control}
      ${body}
    </body>
    </html>
    `;
}

function templateList(filelist) {
  var list = '<ul>';
  var i = 0;
  while(i < filelist.length) {
    list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
    i = i + 1;
  }
  list = list + '</ul>';
  return list;
}

var app = http.createServer(function(request,response) {
  var _url = request.url;
  var queryData = url.parse(_url, true).query;
  var pathname = url.parse(_url, true).pathname;
  if(pathname == '/') {
    if(queryData.id == undefined) {
      fs.readdir('./data', 'utf8', function(error, filelist) {
        var title = 'Welcome';
        var description = 'Hello, Node.js';
        var list = templateList(filelist);
        var template = templateHTML(title, list,
          `<h2>${title}</h2><p>${description}</p>`,
          `<a href="/create">Create</a>`);
        response.writeHead(200);
        response.end(template);
      })
    } else {
      fs.readdir('./data', 'utf8', function(error, filelist) {
        fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
          var title = queryData.id;
          var list = templateList(filelist);
          var template = templateHTML(title, list,
            `<h2>${title}</h2><p>${description}</p>`,
            `<a href="/create">Create</a> <a href="/update?id=${title}">Update</a>
             <form action="delete_process" method="post">
               <input type="hidden" name="id" value="${title}">
               <input type="submit" value="Delete">
             </form>
            `);
          response.writeHead(200);
          response.end(template);
        })
      })
    }
  } else if (pathname == '/create') {
    fs.readdir('./data', 'utf8', function(err, filelist) {
      var title = 'Welcome - Create';
      var list = templateList(filelist);
      var template = templateHTML(title, list, `
        <form action="/create_process" method="post">
          <p><input type="text" name="title" placeholder="title"></p>
          <p><textarea name="description" placeholder="description"></textarea></p>
          <p><input type="submit"></p>
        </form>
        `, ``);
      response.writeHead(200);
      response.end(template);
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
      fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
        var title = queryData.id;
        var list = templateList(filelist);
        var template = templateHTML(title, list, `
          <form action="/update_process" method="post">
            <input type="hidden" name="id" value="${title}">
            <p><input type="text" name="title" placeholder="title" value="${title}"></p>
            <p><textarea name="description" placeholder="description">${description}</textarea></p>
            <p><input type="submit"></p>
          </form>
          `, `<a href="/create">Create</a> <a href="/update?id=${title}">Update</a>`);
        response.writeHead(200);
        response.end(template);
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
      fs.unlink(`data/${id}`, function(error){
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

#### 1) 삭제할 글을 조회하고, 'Delete' 버튼을 눌러 글을 삭제합니다.  

![nodejs_deletefile_001](https://drive.google.com/uc?id=1bavu7iFJHzyk5z4JchCeB8rmi1brkrio)  

#### 2) 해당 파일이 삭제되고, 메인 화면으로 리다이렉션 하는 것을 확인할 수 있습니다.  

![nodejs_deletefile_002](https://drive.google.com/uc?id=1PvfM0zvmVL3QljfYUWIHkIasQhrlEHmO)  
