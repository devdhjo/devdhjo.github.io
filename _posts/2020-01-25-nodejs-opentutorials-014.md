---
layout: post
title: "[nodejs] 021# App ⒁ 입력 정보에 대한 보안"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩 ([https://opentutorials.org/course/1](https://opentutorials.org/course/1)) 에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js ([https://opentutorials.org/course/3332](https://opentutorials.org/course/3332))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ 입력정보와 관련해서 보안적으로 처리해야 할 이슈를 학습합니다.  


### 1. 입력정보를 처리하는 부분을 확인해봅니다.  

- 현재 main.js 파일에서는 ${queryData.id} 를 사용하여 파일 내용을 읽어오고 있습니다.  
-- 그렇다면, id 값을 이용하여 data 폴더가 아닌 다른 파일도 확인할 수 있을까요?  

![nodejs_secureInput_001](https://drive.google.com/uc?id=1QUh7F3JQFOFtgbMFZv3m33cGH5KvmC6G)  

- data 폴더가 아닌 다른 디렉토리에 있는 파일의 내용이 노출되는 것을 확인할 수 있습니다.  

- id 값을 통해 경로를 변경하여 접근한다면, 모든 파일을 읽을 수 있게 되는 것입니다.  

### 2. path 모듈을 사용하여 id 를 이용하여 경로를 변경할 수 없도록 처리합니다.  

- path 의 base 는 dir 을 제외한, 파일명과 확장자 정보만을 가지고 있습니다.  
  -- 참고자료 ([Node.js - path.parse(path)](https://nodejs.org/dist/latest-v12.x/docs/api/path.html#path_path_parse_path))  

1) main.js 파일에 path 모듈을 임포트합니다.  

```diff
  ...
  var template = require('./lib/template.js');
+ var path = require('path');

  var app = http.createServer(function(request,response){
    ..
```

2) 사용자가 입력한 id 값을 사용하는 코드를 제어할 수 있도록 수정합니다.  

```diff
  var app = http.createServer(function(request,response){
    ...
    if(pathname == '/') {
      if(queryData.id == undefined) {
        ...
      } else {
        fs.readdir('./data', 'utf8', function(error, filelist){
+         var filteredId = path.parse(queryData.id).base;
+         fs.readFile(`data/${filteredId}`, 'utf8', function(err, description) {
-         fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
            ...
          });
        })
      }
    } else if (pathname == '/create') {
      ...
    } else if (pathname == '/create_process') {
      ...
    } else if (pathname == '/update') {
      fs.readdir('./data', 'utf8', function(error, filelist){
+       var filteredId = path.parse(queryData.id).base;
+       fs.readFile(`data/${filteredId}`, 'utf8', function(err, description) {
-       fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
          ...
        });
      })
    } else if (pathname == '/update_process') {
      ...
    } else if (pathname == '/delete_process') {
      ...
      request.on('end', function(){
          var post = qs.parse(body);
          var id = post.id;
+         var filteredId = path.parse(id).base;
+         fs.unlink(`data/${filteredId}`, function(error){
-         fs.unlink(`data/${id}`, function(error){
            ...
          })
      });
    } else {
      ...
    }
  });
```

### 3. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\git\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/d5cb0ee857ae56e6438832c18c2fbff20f899558))  

{% highlight javascript linenos %}
var http = require('http');
var fs = require('fs');
var url = require('url');
var qs = require('querystring');
var template = require('./lib/template.js');
var path = require('path');

var app = http.createServer(function(request,response) {
  var _url = request.url;
  var queryData = url.parse(_url, true).query;
  var pathname = url.parse(_url, true).pathname;
  if(pathname == '/') {
    if(queryData.id == undefined) {
      fs.readdir('./data', 'utf8', function(error, filelist) {
        var title = 'Welcome';
        var description = 'Hello, Node.js';
        var list = template.list(filelist);
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
          var list = template.list(filelist);
          var html = template.HTML(title, list,
            `<h2>${title}</h2><p>${description}</p>`,
            `<a href="/create">Create</a> <a href="/update?id=${title}">Update</a>
             <form action="delete_process" method="post">
               <input type="hidden" name="id" value="${title}">
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

### 4. 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  

![nodejs_secureInput_002](https://drive.google.com/uc?id=1MVDVFcf2VcvNLzKgisXrw3JwFIiApQhP)  

- path 에 입력한 경로 정보를 차단함으로써 다른 경로의 파일에는 접근되지 않는 것을 확인할 수 있습니다.  
