---
layout: post
title: "[nodejs] 013# App ⑹ 메인페이지 구현"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩 ([https://opentutorials.org/course/1](https://opentutorials.org/course/1)) 에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js ([https://opentutorials.org/course/3332](https://opentutorials.org/course/3332))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ 조건문을 활용해서 쿼리스트링이 없는 메인페이지를 표현하는 방법을 학습합니다.  

### 1. main.js 파일을 수정합니다.  

#### 1) 쿼리스트링이 없는 경우 분기처리를 위해 if문을 추가하고, 기존 코드를 복사하여 if 문과 else 문에 동일하게 추가합니다.  

```diff
  ...
  var app = http.createServer(function(request,response){
      ...
      if(pathname == '/') {
+         if(queryData.id == undefined) {
+             fs.readFile(`data/${title}`, 'utf8', function(err, description) {
+             ...
+             });
+         } else {
              fs.readFile(`data/${title}`, 'utf8', function(err, description) {
              ...
              });
+         }
      } else {
          response.writeHead(404);
          response.end('Not found');
      }
  });
  app.listen(3000);
```

#### 2) 페이지의 제목과 본문을 지정합니다.  

- 쿼리스트링이 없는 메인페이지에도 title 을 표기해야 하므로 해당 변수를 readFile 함수 안쪽으로 이동하고, 각각의 경우에 따른 값을 지정해줍니다.  

- 메인페이지는 data 디렉토리에 별도의 본문 파일이 없기 때문에 변수 description 에 본문에 들어갈 내용을 지정합니다.

```diff
  ...
  var app = http.createServer(function(request,response){
      var _url = request.url;
      var queryData = url.parse(_url, true).query;
      var pathname = url.parse(_url, true).pathname;
-     var title = queryData.id;
      if(pathname == '/') {
          if(queryData.id == undefined) {
-             fs.readFile(`data/${title}`,        'utf8', function(err, description) {
+             fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
+                 var title = 'Welcome';
+                 var description = 'Hello, Node.js';
                  var template = `
                  ...
              });
          } else {
-             fs.readFile(`data/${title}`,        'utf8', function(err, description) {
+             fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
+                 var title = queryData.id;
                  var template = `
                  ...
              });
          }
      } else {
      ...
```

### 2. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\git\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/fa25389fc80598598e2e49b7d0cd245c4b67995e))  

{% highlight javascript linenos %}
var http = require('http');
var fs = require('fs');
var url = require('url');

var app = http.createServer(function(request,response){
  var _url = request.url;
  var queryData = url.parse(_url, true).query;
  var pathname = url.parse(_url, true).pathname;
  if(pathname == '/') {
    if(queryData.id == undefined) {
      fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
        var title = 'Welcome';
        var description = 'Hello, Node.js';
        var template = `
          <!doctype html>
          <html>
          <head>
            <title>WEB1 - ${title}</title>
            <meta charset="utf-8">
          </head>
          <body>
            <h1><a href="/">WEB</a></h1>
            <ul>
              <li><a href="/?id=HTML">HTML</a></li>
              <li><a href="/?id=CSS">CSS</a></li>
              <li><a href="/?id=JavaScript">JavaScript</a></li>
            </ul>
            <h2>${title}</h2>
            <p>${description}</p>
          </body>
          </html>
          `;
        response.writeHead(200);
        response.end(template);
      })
    } else {
      fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
        var title = queryData.id;
        var template = `
          <!doctype html>
          <html>
          <head>
            <title>WEB1 - ${title}</title>
            <meta charset="utf-8">
          </head>
          <body>
            <h1><a href="/">WEB</a></h1>
            <ul>
              <li><a href="/?id=HTML">HTML</a></li>
              <li><a href="/?id=CSS">CSS</a></li>
              <li><a href="/?id=JavaScript">JavaScript</a></li>
            </ul>
            <h2>${title}</h2>
            <p>${description}</p>
          </body>
          </html>
          `;
        response.writeHead(200);
        response.end(template);
      })
    }
  } else {
    response.writeHead(404);
    response.end('Not found');
  }
});

app.listen(3000);
{% endhighlight %}

### 3. 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  
-- main.js 의 68번 라인에서 설정해준 포트번호 3000을 반드시 함께 입력해야 합니다.  

![nodejs_readfile_003](https://drive.google.com/uc?id=1hEO9zKnqWXvTjalPACb8-d44sp8egoqB)  

- 012# 에서 undefined 로 출력되던 제목과 본문을 코드에서 지정한 값으로 확인할 수 있습니다.  
