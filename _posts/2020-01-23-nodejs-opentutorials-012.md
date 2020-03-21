---
layout: post
title: "[nodejs] 019# App ⑿ 객체를 이용한 템플릿 기능 정리정돈"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩 ([https://opentutorials.org/course/1](https://opentutorials.org/course/1)) 에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js ([https://opentutorials.org/course/3332](https://opentutorials.org/course/3332))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ 객체를 이용하여 템플릿 기능을 정리정돈하는 방법을 학습합니다.  

### 1. templateHTML, templateList 두 함수를 하나로 객체화 합니다.  

```diff
+ var template = {
- function templateHTML(title, list, body, control){
+   HTML : function(title, list, body, control) {
      return `
        <!doctype html>
        <html>
        ...
        </html>
        `;
- }
+   }, list : function(filelist) {
- function templateList(filelist){
      var list = '<ul>';
      ...
      return list;
    }
+ }
```

### 2. templateHTML, templateList 를 호출하는 코드를 수정합니다.  

```diff
  var app = http.createServer(function(request,response){
    ...
    if(pathname == '/') {
      if(queryData.id == undefined) {
        fs.readdir('./data', 'utf8', function(error, filelist){
          ...
-         var list = templateList(filelist);
+         var list = template.list(filelist);
-         var template = templateHTML(title, list,
+         var html = template.HTML(title, list,
            `<h2>${title}</h2>${description}`,
            `<a href="/create">Create</a>`);
          response.writeHead(200);
-         response.end(template);
+         response.end(html);
        })
      } else {
        fs.readdir('./data', 'utf8', function(error, filelist){
          fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
            var title = queryData.id;
-           var list = templateList(filelist);
+           var list = template.list(filelist);
-           var template = templateHTML(title, list,
+           var html = template.HTML(title, list,
              `...`);
            response.writeHead(200);
-           response.end(template);
+           response.end(html);
          });
        })
      }
    } else if (pathname == '/create') {
      fs.readdir('./data', 'utf8', function(error, filelist){
        var title = 'WEB - Create';
-       var list = templateList(filelist);
+       var list = template.list(filelist);
-       var template = templateHTML(title, list,
+       var html = template.HTML(title, list,
          ...`, '');
        response.writeHead(200);
-       response.end(template);
+       response.end(html);
      })
    } else if (pathname == '/create_process') {
      ...
    } else if (pathname == '/update') {
      fs.readdir('./data', 'utf8', function(error, filelist){
        fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
          var title = queryData.id;
-         var list = templateList(filelist);
+         var list = template.list(filelist);
-         var template = templateHTML(title, list,
+         var html = template.HTML(title, list,
            ...`);
          response.writeHead(200);
-         response.end(template);
+         response.end(html);
        });
      })
    } else if (pathname == '/update_process') {
      ...
  });
```

### 3. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\git\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/1877bee67cddabf4048c1a4297bf0e7e569da5dd))  

{% highlight javascript linenos %}
var http = require('http');
var fs = require('fs');
var url = require('url');
var qs = require('querystring');

var template = {
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
        ${list}
        ${control}
        ${body}
      </body>
      </html>
      `;
  }, list : function(filelist) {
    var list = '<ul>';
    var i = 0;
    while(i < filelist.length) {
      list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
      i = i + 1;
    }
    list = list + '</ul>';
    return list;
  }
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
        var list = template.list(filelist);
        var html = template.HTML(title, list,
          `<h2>${title}</h2><p>${description}</p>`,
          `<a href="/create">Create</a>`);
        response.writeHead(200);
        response.end(html);
      })
    } else {
      fs.readdir('./data', 'utf8', function(error, filelist) {
        fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
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
      fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
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

![nodejs_deletefile_002](https://drive.google.com/uc?id=1PvfM0zvmVL3QljfYUWIHkIasQhrlEHmO)  

- 페이지는 동일하게 작동하지만, 내부의 코드는 효율적으로 바뀌었습니다.  

#### ◇ 코드의 동작은 유지하면서, 내부의 코드를 더 효율적으로 바꾸는 것을 __리팩토링__ 이라고 합니다.  

-- 현재 프로젝트의 코드는 많지 않기 때문에 객체화를 하지 않아도 되지만, 코드의 양이 늘어날수록 관리하기 어려워짐에 따라 리팩토링 스킬은 매우 중요합니다.  

-- 처음부터 이상적인 코드를 작성하려고 하는 것 보다, 완성된 코드의 반복되는 패턴을 찾아 함수화, 객체화를 통해 자주 리팩토링하며 개선하는 것이 좋습니다.  
