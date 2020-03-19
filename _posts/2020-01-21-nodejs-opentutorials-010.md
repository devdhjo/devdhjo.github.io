---
layout: post
title: "[nodejs] 017# App ⑽ 글수정 - 파일 변경"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩 ([https://opentutorials.org/course/1](https://opentutorials.org/course/1)) 에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js ([https://opentutorials.org/course/3332](https://opentutorials.org/course/3332))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ FORM 을 통해 전송된 데이터로 기존 파일을 변경하는 방법을 학습합니다.  

### 1. 글을 수정할 수 있도록 UI 를 생성합니다.  

#### 1) 화면에 따라 글을 관리하는 버튼을 다르게 보이도록 하기 위해 control 파라미터를 추가합니다.  

```diff
- function templateHTML(title, list, body){
+ function templateHTML(title, list, body, control){
    return `
      <!doctype html>
      <html>
      <head>
        <title>WEB1 - ${title}</title>
        <meta charset="utf-8">
      </head>
      <body>
        <h1><a href="/">WEB</a></h1>
-       <a href="/create">Create</a>
        ${list}
+       ${control}
        ${body}
      </body>
      </html>
      `;
  }
```

#### 2) templateHTML 함수에 추가한 control 파라미터에 대응하는 코드를 추가합니다.  

- 홈 화면에서는 글쓰기 버튼만 보이고, 글 조회 화면에서만 수정 버튼이 보이도록 추가합니다.  
-- 수정하려는 파일을 구분하기 위해 기존 파일의 title 을 쿼리스트링의 id 값으로 전송합니다.  

- 글작성 화면에서는 control 버튼이 보이지 않도록 하기 위해 빈 문자열을 전송합니다.  

```diff
var app = http.createServer(function(request,response){
  var _url = request.url;
  var queryData = url.parse(_url, true).query;
  var pathname = url.parse(_url, true).pathname;
  if(pathname == '/') {
    if(queryData.id == undefined) {
      ...
-       var template = templateHTML(title, list, `<h2>${title}</h2>${description}`);
+       var template = templateHTML(title, list, `<h2>${title}</h2>${description}`,
+         `<a href="/create">Create</a>`);
      ...
    } else {
      ...
-         var template = templateHTML(title, list, `<h2>${title}</h2>${description}`);
+         var template = templateHTML(title, list, `<h2>${title}</h2>${description}`,
+           `<a href="/create">Create</a> <a href="/update?id=${title}">Update</a>`);
      ...
    }
  } else if (pathname == '/create') {
    ...
      var template = templateHTML(title, list, `
          <form action="/create_process" method="post">
            ...
          </form>
-       `);
+       `, ``);
     ...
  } else if (pathname == '/create_process') {
    ...
```

![nodejs_updatefile_001](https://drive.google.com/uc?id=1XBsw1K374jmyaEIxpxW388jF_lNqA95P)  

#### 3) '/update' 요청을 처리할 수 있도록 코드를 추가합니다.  

- 쿼리스트링으로 넘어온 id 값을 FORM 의 hidden 속성으로 저장합니다.  
- FORM 태그에 ${title} 과 ${description} 을 이용하여 기존의 파일 정보를 표시합니다.  
- 수정한 데이터는 '/update_process' 주소를 통해 POST 방식으로 전송합니다.  

```diff
  var app = http.createServer(function(request,response){
    ...
    if(pathname == '/') {
      ...
    } else if (pathname == '/create') {
      ...
    } else if (pathname == '/create_process') {
      ...
+   } else if (pathname == '/update') {
+     fs.readdir('./data', 'utf8', function(error, filelist){
+       fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
+         var title = queryData.id;
+         var list = templateList(filelist);
+         var template = templateHTML(title, list, `
+             <form action="/update_process" method="post">
+               <input type="hidden" name="id" value="${title}">
+               <p><input type="text" name="title" placeholder="title" value="${title}"></p>
+               <p><textarea name="description" placeholder="description">${description}</textarea></p>
+               <p><input type="submit"></p>
+             </form>
+           `, `<a href="/create">Create</a> <a href="/update?id=${title}">Update</a>`);
+         response.writeHead(200);
+         response.end(template);
+       })
+     })
    } else {
      response.writeHead(404);
      response.end('Not found');
    }
  });
```

![nodejs_updatefile_002](https://drive.google.com/uc?id=18QcsRDUKJbSZHXaY8YSNBZpo0rv70XhM)  

### 2. '/update_process' 주소로 전송된 FORM 데이터를 처리하기 위한 코드를 추가합니다.  

#### 1) POST 방식으로 전송된 데이터를 이용하여 파일을 변경합니다.  

- 기존 파일의 title 을 fs.rename 함수를 이용하여 변경합니다.  
-- 참고자료 ([Node.js - fs.rename(oldPath, newPath, callback)](https://nodejs.org/dist/latest-v12.x/docs/api/fs.html#fs_fs_rename_oldpath_newpath_callback))  

- 기존 파일의 description 을 fs.writeFile 함수를 이용하여 변경합니다.  
-- 참고자료 ([Node.js - fs.writeFile(file, data[, options], callback)](https://nodejs.org/dist/latest-v12.x/docs/api/fs.html#fs_fs_writefile_file_data_options_callback))  

```diff
  var app = http.createServer(function(request,response){
    ...
    if(pathname == '/') {
      ...
    } else if (pathname == '/update') {
      ...
+   } else if (pathname == '/update_process') {
+     var body = '';
+     request.on('data', function(data){
+       body = body + data;
+     })
+     request.on('end', function(){
+       var post = qs.parse(body);
+       var id = post.id;
+       var title = post.title;
+       var description = post.description;
+       fs.rename(`data/${id}`, `data/${title}`, function(error){
+         fs.writeFile(`data/${title}`, description, 'utf8', function(err){
+           response.writeHead(302, {Location: `/?id=${title}`});
+           response.end();
+         })
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

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/9d363d4fbf4c6b061a9df5040ba71f5f85a34326))  

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
            `<a href="/create">Create</a> <a href="/update?id=${title}">Update</a>`);
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
  } else {
    response.writeHead(404);
    response.end('Not found');
  }
});

app.listen(3000);
{% endhighlight %}

### 4. 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  

#### 1) 'Update' 버튼을 눌러 글을 수정합니다.  

![nodejs_updatefile_003](https://drive.google.com/uc?id=1-0-Rhuo4M5t-ASYu3Fgfsvrc3XnM-Ta7)  

#### 2) '제출' 버튼을 클릭하면 수정한 내용을 조회하는 화면으로 리다이렉션 됩니다.  

![nodejs_updatefile_004](https://drive.google.com/uc?id=1nTKHsp_r5RBlLRnJ41OXJ7yTO13ofKUx)  

- 쿼리스트링의 id 값을 이용하여 기존 파일명을 찾아 수정된 내용으로 파일을 저장하는 것을 확인할 수 있습니다.  
