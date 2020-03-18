---
layout: post
title: "[nodejs] 016# App ⑼ 글작성 - 파일생성과 리다이렉션"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩 ([https://opentutorials.org/course/1](https://opentutorials.org/course/1)) 에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js ([https://opentutorials.org/course/3332](https://opentutorials.org/course/3332))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ FORM 을 생성하여 POST 데이터를 받아 파일에 저장하고, 결과 페이지로 리다이렉션 하는 방법을 학습합니다.  

### 1. 글을 작성할 수 있도록 UI 를 생성합니다.  

#### 1) 글쓰기 버튼을 추가하고, 버튼을 클릭했을 때 '/create' 요청을 전송하도록 설정합니다.  

```diff
  function templateHTML(title, list, body){
    return `
      <!doctype html>
      <html>
      <head>
        <title>WEB1 - ${title}</title>
        <meta charset="utf-8">
      </head>
      <body>
        <h1><a href="/">WEB</a></h1>
+       <a href="/create">Create</a>
        ${list}
        ${body}
      </body>
      </html>
      `;
  }
```

![nodejs_createfile_001](https://drive.google.com/uc?id=1CNAVtQ8dRfm6NUnKYaFMrp5yLtItpCZ0)  

#### 2) '/create' 요청을 처리할 수 있도록 코드를 추가합니다.  

- template 변수에 FORM 태그를 이용하여 글을 작성할 수 있는 UI 를 구성합니다.  
- 작성한 데이터는 '/create_process' 주소를 통해 POST 방식으로 전송합니다.  

```diff
  var app = http.createServer(function(request,response){
    ...
    if(pathname == '/') {
      if(queryData.id == undefined) {
        ...
      } else {
        ...
      }
+   } else if (pathname == '/create') {
+      fs.readdir('./data', 'utf8', function(error, filelist){
+       var title = 'WEB - Create';
+       var list = templateList(filelist);
+       var template = templateHTML(title, list, `
+           <form action="/create_process" method="post">
+             <p><input type="text" name="title" placeholder="title"></p>
+             <p><textarea name="description" placeholder="description"></textarea></p>
+             <p><input type="submit"></p>
+           </form>
+         `);
+       response.writeHead(200);
+       response.end(template);
+     })
    } else {
      response.writeHead(404);
      response.end('Not found');
    }
  });
```

![nodejs_createfile_002](https://drive.google.com/uc?id=1tvUiY7Bz_DoDT8QXtZI_w6CgNFALDlEh)  

### 2. '/create_process' 주소로 전송된 FORM 데이터를 처리할 코드를 추가합니다.  

#### 1) POST 방식으로 전송된 데이터를 사용하기 위해 querystring 모듈을 임포트합니다.  

```diff
  var http = require('http');
  var fs = require('fs');
  var url = require('url');
+ var qs = require('querystring');

  function templateHTML(title, list, body){
    ...
  });

  app.listen(3000);
```

#### 2) POST 방식으로 전송된 데이터를 request 에서 가져옵니다.  

-- 참고자료 ([Node.js - HTTP 트랜잭션 해부](https://nodejs.org/ko/docs/guides/anatomy-of-an-http-transaction/))  

```diff
  var app = http.createServer(function(request,response){
    ...
    if(pathname == '/') {
      ...
    } else if (pathname == '/create') {
      ...
+   } else if (pathname == '/create_process') {
+     var body = '';
+     request.on('data', function(data){
+       body = body + data;
+     })
    } else {
      response.writeHead(404);
      response.end('Not found');
    }
  });
```

#### 3) request 에서 꺼내온 데이터를 파일로 저장합니다.  

- writeFile 함수를 이용하여 파일을 생성합니다.  
-- 참고자료 ([Node.js - fs.writeFile()](https://nodejs.org/dist/latest-v12.x/docs/api/fs.html#fs_fs_writefile_file_data_options_callback))  

```diff
  } else if (pathname == '/create_process') {
    var body = '';
    request.on('data', function(data){
        body = body + data;
    });
+   request.on('end', function(){
+     var post = qs.parse(body);
+     var title = post.title;
+     var description = post.description;
+     fs.writeFile(`data/${title}`, description, 'utf8', function(err){
+       response.writeHead(302, {Location: `/?id=${title}`});
+       response.end();
+     })
+   })
  } else {
    ...
```

### 3. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\git\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/581ea9f76674d2231cb693afeee8b9c33b0d1722))  

{% highlight javascript linenos %}
var http = require('http');
var fs = require('fs');
var url = require('url');
var qs = require('querystring');

function templateHTML(title, list, body){
  return `
    <!doctype html>
    <html>
    <head>
      <title>WEB1 - ${title}</title>
      <meta charset="utf-8">
    </head>
    <body>
      <h1><a href="/">WEB</a></h1>
      <a href="/create">Create</a>
      ${list}
      ${body}
    </body>
    </html>
    `;
}

function templateList(filelist){
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
      fs.readdir('./data', function(error, filelist) {
        var title = 'Welcome';
        var description = 'Hello, Node.js';
        var list = templateList(filelist);
        var template = templateHTML(title, list, `<h2>${title}</h2><p>${description}</p>`);
        response.writeHead(200);
        response.end(template);
      })
    } else {
      fs.readdir('./data', function(error, filelist) {
        fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
          var title = queryData.id;
          var list = templateList(filelist);
          var template = templateHTML(title, list, `<h2>${title}</h2><p>${description}</p>`);
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
        `);
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
  } else {
    response.writeHead(404);
    response.end('Not found');
  }
});

app.listen(3000);
{% endhighlight %}

### 4. 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  

#### 1) 'Create' 버튼을 눌러 글을 작성합니다.  

![nodejs_createfile_003](https://drive.google.com/uc?id=1kgk9KOp-HDL2mtYUv1HJTCHwhO3rTeBr)  

#### 2) '제출' 버튼을 클릭하면 작성한 내용을 조회하는 화면으로 리다이렉션 됩니다.  

![nodejs_createfile_004](https://drive.google.com/uc?id=1UoXK9yUxkiHWQT03JGv6mxTl2oKG5oId)  

- 작성한 내용은 '/data' 폴더 안에 파일로 저장되어 파일목록에도 조회되는 것을 확인할 수 있습니다.  
