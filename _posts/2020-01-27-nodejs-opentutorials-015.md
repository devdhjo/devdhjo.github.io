---
layout: post
title: "[nodejs] 022# App ⒂ 출력 정보에 대한 보안"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩 ([https://opentutorials.org/course/1](https://opentutorials.org/course/1)) 에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js ([https://opentutorials.org/course/3332](https://opentutorials.org/course/3332))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ 출력정보에서 발생할 수 있는 보안적인 이슈를 학습합니다.  


### 1. 사용자가 입력한 내용을 출력시키는 화면을 확인해봅니다.  

- 입력하는 내용에 스크립트 태그를 사용해봅니다.  

![nodejs_sanitizeHtml_001](https://drive.google.com/uc?id=1CC_SlFOnIa3SWAKIRRKqwJadcwC3aI2C)  

-- 스크립트 태그를 이용해서 alert 뿐만 아니라 href 를 이용하여 사용자의 브라우저 또한 제어할 수 있게 됩니다.  

#### ◇ XSS 란?  

Cross-site Scripting 의 약어로, 웹사이트 관리자가 아닌 사용자가 웹페이지에 악성 스크립트를 삽입할 수 있는 취약점  

### 2. npm 에서 제공하는 sanitize-html 모듈을 사용하여 입력 정보에 html 태그를 사용할 수 없도록 처리합니다.  

- sanitize-html 는 사용자 입력 정보에 html 태그들을 변환시켜 악성 스크립트를 막아주는 보안 라이브러리입니다.  
  -- 참고자료 ([npm > sanitize-html](https://www.npmjs.com/package/sanitize-html))  

#### 1) 프로젝트 디렉토리에서 npm 을 사용할 수 있도록 명령어를 실행합니다.  

```
npm init
```

- init 을 실행하면 기본 정보를 입력하라는 메세지를 확인할 수 있습니다.  
-- 정보를 입력하지 않고 Enter 키를 누르면 자동으로 기본 정보가 입력됩니다.  

![nodejs_sanitizeHtml_002](https://drive.google.com/uc?id=1iIKsWPyqJSci6Fmmi7b-EXuwOaRjMw6k)  

- init 명령이 완료되면 디렉토리에 package.json 파일이 생성되는 것을 확인할 수 있습니다.  
-- [수정사항 보기 > npm init](https://github.com/devdhjo/nodejs_opentutorials/commit/5b4ac15047d0a49de4b1ae10baadaedd4bb485bd)  

- package.json  

{% highlight json linenos %}
{
  "name": "nodejs_opentutorials",
  "version": "1.0.0",
  "description": "",
  "main": "main.js",
  "directories": {
    "lib": "lib"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/devdhjo/nodejs_opentutorials.git"
  },
  "author": "",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/devdhjo/nodejs_opentutorials/issues"
  },
  "homepage": "https://github.com/devdhjo/nodejs_opentutorials#readme"
}
{% endhighlight %}

#### 2) npm 을 이용하여 sanitize-html 모듈을 설치합니다.  

```
npm install -S sanitize-html
```

-- 해당 프로젝트에서만 모듈을 사용할 경우 '-S' 를 입력하고, 모듈을 전역에서 사용할 경우 '-g' 를 입력합니다.  

![nodejs_sanitizeHtml_003](https://drive.google.com/uc?id=1a6F69N746uHY00-OtCce2cev79OLpDF6)  

- install 명령이 완료되면 디렉토리에 node_modules 폴더가 생성되고, 내부에 sanitize-html 모듈과 그 모듈이 의존하고 있는 다른 모듈까지 자동으로 설치됩니다.  
-- [수정사항 보기 > npm install -S sanitize-html](https://github.com/devdhjo/nodejs_opentutorials/commit/9cf8a6d5f954c2461ad79fd9c3b65b227d42775d)  

![nodejs_sanitizeHtml_004](https://drive.google.com/uc?id=1nJiv0s3CqE8o3VrpQGNq852NMMqPZsrY)  

- package.json 파일에도 dependencies 정보가 자동으로 업데이트 된 것을 확인할 수 있습니다.  

### 3. main.js 파일에 sanitize-html 모듈을 사용하여 출력 정보를 소독합니다.  

#### 1) sanitize-html 모듈을 임포트하고, 제목과 내용을 읽어오는 곳에 적용합니다.  

```diff
  ...
  var template = require('./lib/template.js');
  var path = require('path');
+ var sanitizeHtml = require('sanitize-html');

  var app = http.createServer(function(request,response){
    ...
    if(pathname == '/') {
      if(queryData.id == undefined) {
        ...
      } else {
        fs.readdir('./data', 'utf8', function(error, filelist){
          var filteredId = path.parse(queryData.id).base;
          fs.readFile(`data/${filteredId}`, 'utf8', function(err, description) {
            var title = queryData.id;
+           var sanitizeTitle = sanitizeHtml(title);
+           var sanitizeDesc = sanitizeHtml(description);
            var list = template.list(filelist);
-           var html = template.HTML(title, list, `<h2>${title}</h2>${description}`,
+           var html = template.HTML(title, list, `<h2>${sanitizeTitle}</h2>${sanitizeDesc}`,
-             `<a href="/create">Create</a> <a href="/update?id=${title}">Update</a>
+             `<a href="/create">Create</a> <a href="/update?id=${sanitizeTitle}">Update</a>
               <form action="delete_process" method="post">
-                <input type="hidden" name="id" value="${title}">
+                <input type="hidden" name="id" value="${sanitizeTitle}">
                 <input type="submit" value="Delete">
               </form>`);
            response.writeHead(200);
            response.end(html);
          });
        ...
```

#### 2) 수정된 main.js 를 실행하고, 스크립트가 입력된 파일을 다시 조회하면 alert 기능이 작동하지 않는 것을 볼 수 있습니다.  

- sanitize-html 모듈을 사용하면 script 와 같이 치명적인 태그는 출력에서 삭제됩니다.  

![nodejs_sanitizeHtml_005](https://drive.google.com/uc?id=1CCMbY-VfZM4XnctiOWVt_2wYrh7DVsO6)  

- 치명적이지 않은 'h1', 'h2' 와 같은 제목 태그의 경우, 태그는 삭제되고 내용만 출력됩니다.  

![nodejs_sanitizeHtml_006](https://drive.google.com/uc?id=13t2jHCqmnBx82vq_R3fcRf20q-A3oVe0)  

#### 3) 필요한 태그를 허용하고 싶은 경우 sanitize-html 모듈의 두번째 인자로 입력해줍니다.  

-- 참고자료 ([npm > sanitize-html](https://www.npmjs.com/package/sanitize-html#how-to-use))  

```diff
    fs.readFile(`data/${filteredId}`, 'utf8', function(err, description) {
      var title = queryData.id;
      var sanitizeTitle = sanitizeHtml(title);
-     var sanitizeDesc = sanitizeHtml(description);
+     var sanitizeDesc = sanitizeHtml(description, {
+       allowedTags:['h1']
+     });
      var list = template.list(filelist);
      ...
    });
```

### 4. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\git\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/a28d3dd6c2f005801f4a08be4db98a721d841ff6))  

{% highlight javascript linenos %}
var http = require('http');
var fs = require('fs');
var url = require('url');
var qs = require('querystring');
var template = require('./lib/template.js');
var path = require('path');
var sanitizeHtml = require('sanitize-html');

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
          var sanitizeTitle = sanitizeHtml(title);
          var sanitizeDesc = sanitizeHtml(description, {
            allowedTags:['h1']
          });
          var list = template.list(filelist);
          var html = template.HTML(title, list,
            `<h2>${sanitizeTitle}</h2><p>${sanitizeDesc}</p>`,
            `<a href="/create">Create</a> <a href="/update?id=${sanitizeTitle}">Update</a>
             <form action="delete_process" method="post">
               <input type="hidden" name="id" value="${sanitizeTitle}">
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

### 5. 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  

![nodejs_sanitizeHtml_007](https://drive.google.com/uc?id=1cvvUsTPIjsnvbU2To7TcEz9k_JhXYZ0f)  

-- 허용된 내용 이외의 html 태그는 모두 제거 후 출력되는 것을 확인할 수 있습니다.  
