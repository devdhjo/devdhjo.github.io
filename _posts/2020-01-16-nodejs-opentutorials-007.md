---
layout: post
title: "[nodejs] 014# App ⑺ 글 목록 출력하기"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩 ([https://opentutorials.org/course/1](https://opentutorials.org/course/1)) 에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js ([https://opentutorials.org/course/3332](https://opentutorials.org/course/3332))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ data 디렉토리에 있는 파일 목록을 출력하는 기능을 학습합니다.  

### 1. main.js 파일을 수정합니다.  

#### 1) 파일 목록을 읽어오는 기능을 하는 함수를 추가합니다.

-- 참고자료 ([Node.js - https://nodejs.org](https://nodejs.org/ko/knowledge/file-system/how-to-search-files-and-directories-in-nodejs/))

- 메인페이지의 본문은 파일에서 읽어오는 것이 아니기 때문에 readFile 함수는 불필요하므로 삭제하고, 파일 목록을 불러오는 함수 readdir 로 변경합니다.

```diff
   ...
   if(pathname == '/') {
     if(queryData.id == undefined) {
-      fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
+      fs.readdir('./data', function(error, filelist){
         var title = 'Welcome';
         var description = 'Hello, Node.js';
         var template = `
           ...
           `;
         response.writeHead(200);
         response.end(template);
       });
     } else {
+      fs.readdir('./data', function(error, filelist){
         fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
         ...
         });
+      })
     }
   } else {
     ...
   }
 });
 app.listen(3000);
```

#### 2) readdir 로 읽어온 파일 목록을 출력하는 반복문을 작성합니다.  

```diff
    ...
    if(queryData.id == undefined) {
      fs.readdir('./data', function(error, filelist){
        var title = 'Welcome';
        var description = 'Hello, Node.js';
+       var list = '<ul>';
+       var i = 0;
+       while(i < filelist.length){
+         list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
+         i = i + 1;
+       }
+       list = list + '</ul>';
        var template = `
          ...
      })
    } else {
      fs.readdir('./data', function(error, filelist){
+       var list = '<ul>';
+       var i = 0;
+       while(i < filelist.length){
+         list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
+         i = i + 1;
+       }
+       list = list + '</ul>';
        fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description) {
          var title = queryData.id;
          var template = `
          ...
```

#### 3) 반복문을 통해 생성한 파일목록을 template 변수에 적용합니다.  

```diff
  var template = `
    <!doctype html>
    <html>
    <head>
      <title>WEB1 - ${title}</title>
      <meta charset="utf-8">
    </head>
    <body>
      <h1><a href="/">WEB</a></h1>
-     <ul>
-       <li><a href="/?id=HTML">HTML</a></li>
-       <li><a href="/?id=CSS">CSS</a></li>
-       <li><a href="/?id=JavaScript">JavaScript</a></li>
-     </ul>
+     ${list}
      <h2>${title}</h2>
      <p>${description}</p>
    </body>
    </html>
    `;
```

### 3. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\git\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/31bf278b32849ccc1820bb135456f6ae13bdc3a6))  

{% highlight javascript linenos %}
var http = require('http');
var fs = require('fs');
var url = require('url');

var app = http.createServer(function(request,response) {
  var _url = request.url;
  var queryData = url.parse(_url, true).query;
  var pathname = url.parse(_url, true).pathname;
  if(pathname == '/') {
    if(queryData.id == undefined) {
      fs.readdir('./data', function(error, filelist) {
        var title = 'Welcome';
        var description = 'Hello, Node.js';
        var list = '<ul>';
        var i = 0;
        while(i < filelist.length) {
          list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
          i = i + 1;
        }
        list = list + '</ul>';
        var template = `
          <!doctype html>
          <html>
          <head>
            <title>WEB1 - ${title}</title>
            <meta charset="utf-8">
          </head>
          <body>
            <h1><a href="/">WEB</a></h1>
            ${list}
            <h2>${title}</h2>
            <p>${description}</p>
          </body>
          </html>
          `;
        response.writeHead(200);
        response.end(template);
      })
    } else {
      fs.readdir('./data', function(error, filelist) {
        var list = '<ul>';
        var i = 0;
        while(i < filelist.length) {
          list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
          i = i + 1;
        }
        list = list + '</ul>';
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
              ${list}
              <h2>${title}</h2>
              <p>${description}</p>
            </body>
            </html>
            `;
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
-- main.js 의 75번 라인에서 설정해준 포트번호 3000을 반드시 함께 입력해야 합니다.  

![nodejs_readfile_001](https://drive.google.com/uc?id=1Om1g3wRlh8AW0IFafD66tlZYjHaY7k1G)  

- data 폴더에 새 파일이 추가되면 별도의 코드 수정 없이 파일 목록을 조회할 수 있습니다.  

![nodejs_readdir_002](https://drive.google.com/uc?id=1cY1rTgs75YN0r6go5Y4NKghu2tV8HjAO)  
