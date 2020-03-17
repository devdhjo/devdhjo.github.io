---
layout: post
title: "[nodejs] 015# App ⑻ 함수를 이용한 코드 정리정돈"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩 ([https://opentutorials.org/course/1](https://opentutorials.org/course/1)) 에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js ([https://opentutorials.org/course/3332](https://opentutorials.org/course/3332))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ 함수를 이용해서 코드를 정리정돈 하는 방법을 학습합니다.  

### 1. main.js 파일에서 중복되는 코드를 찾아 함수로 분리합니다.  

#### 1) 동일한 값으로 두 번 선언되어 있는 template 변수를 함수로 분리합니다.  

- 본문의 형태가 수정될 수 있기 때문에 body 라는 하나의 변수로 치환하였습니다.  

```diff
  ...
  var url = require('url');
  
+ function templateHTML(title, list, body){
+   return `
+     <!doctype html>
+     <html>
+     <head>
+       <title>WEB1 - ${title}</title>
+       <meta charset="utf-8">
+     </head>
+     <body>
+       <h1><a href="/">WEB</a></h1>
+       ${list}
+       ${body}
+     </body>
+     </html>
+     `;
+ }

var app = http.createServer(function(request,response){
  ...
  if(pathname == '/') {
    if(queryData.id == undefined) {
      fs.readdir('./data', function(error, filelist){
        ...
+       var template = templateHTML(title, list, `<h2>${title}</h2>${description}`);
-       var template = `
-         <!doctype html>
-         <html>
-         <head>
-           <title>WEB1 - ${title}</title>
-           <meta charset="utf-8">
-         </head>
-         <body>
-           <h1><a href="/">WEB</a></h1>
-           ${list}
-           <h2>${title}</h2>
-           <p>${description}</p>
-         </body>
-         </html>
-         `;
        ...
      })
    } else {
      fs.readdir('./data', function(error, filelist){
        ...
        fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
          ...
+         var template = templateHTML(title, list, `<h2>${title}</h2>${description}`);
-         var template = `
-           <!doctype html>
-           <html>
-           <head>
-             <title>WEB1 - ${title}</title>
-             <meta charset="utf-8">
-           </head>
-           <body>
-             <h1><a href="/">WEB</a></h1>
-             ${list}
-             <h2>${title}</h2>
-             <p>${description}</p>
-           </body>
-           </html>
-           `;
          response.writeHead(200);
          response.end(template);
        });
      })
    }
  } else {
  ...
```

#### 2) 동일한 값으로 두 번 선언되어 있는 list 변수를 함수로 분리합니다.  

```diff
  ...
  var url = require('url');

  function templateHTML(title, list, body){
    ...
  }

+ function templateList(filelist){
+   var list = '<ul>';
+   var i = 0;
+   while(i < filelist.length){
+     list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
+      i = i + 1;
+   }
+   list = list + '</ul>';
+   return list;
+ }

var app = http.createServer(function(request,response){
  ...
  if(pathname == '/') {
    if(queryData.id == undefined) {
      fs.readdir('./data', function(error, filelist){
        var title = 'Welcome';
        var description = 'Hello, Node.js';
+       var list = templateList(filelist);
-       var list = '<ul>';
-       var i = 0;
-       while(i < filelist.length){
-         list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
-         i = i + 1;
-       }
-       list = list + '</ul>';
        ...
      })
    } else {
      fs.readdir('./data', function(error, filelist){
-       var list = '<ul>';
-       var i = 0;
-       while(i < filelist.length){
-         list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
-         i = i + 1;
-       }
-       list = list + '</ul>';
        fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
          var title = queryData.id;
+         var list = templateList(filelist);
          ...
        });
      })
    }
  } else {
  ...
```

### 3. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\git\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/a3dca06a095dfe8e9fbaad845ec61f9f0d98df1b))  

{% highlight javascript linenos %}
var http = require('http');
var fs = require('fs');
var url = require('url');

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
  } else {
    response.writeHead(404);
    response.end('Not found');
  }
});

app.listen(3000);
{% endhighlight %}

### 4. 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  
-- main.js 의 64번 라인에서 설정해준 포트번호 3000을 반드시 함께 입력해야 합니다.  

![nodejs_readfile_001](https://drive.google.com/uc?id=1Om1g3wRlh8AW0IFafD66tlZYjHaY7k1G)  

- 이전과 동일하게 작동되지만, 변경된 코드는 가독성이 좋아지고, 유지보수가 용이해졌습니다.  
