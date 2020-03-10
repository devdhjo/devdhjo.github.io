---
layout: post
title: "[nodejs] 012# App ⑸ Not found 오류 구현"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩 ([https://opentutorials.org/course/1](https://opentutorials.org/course/1)) 에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js ([https://opentutorials.org/course/3332](https://opentutorials.org/course/3332))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



#### ◇ 존재하지 않는 정보에 대한 요청의 응답으로 Not found 오류메세지를 전송하는 방법을 학습합니다.  

#### 1. 사용자가 요청한 주소 정보를 가진 값을 저장하기 위한 변수 pathname을 추가합니다.  

-- 참고문서 ([Node.js Documentation](https://nodejs.org/dist/latest-v12.x/docs/api/url.html#url_url_strings_and_url_objects))



```diff
var app = http.createServer(function(request,response){
    var _url = request.url;
    var queryData = url.parse(_url, true).query;
+   var pathname = url.parse(_url, true).pathname;
    var title = queryData.id;
    ...

});
```

#### 2) pathname 에 따라 처리할 내용을 분기처리 합니다.  

```diff
  ...
  var app = http.createServer(function(request,response){
    ...
    var title = queryData.id;
-   if(_url == '/'){
-     title = 'Welcome';
-   }
-   if(_url == '/favicon.ico'){
-     return response.writeHead(404);
-   }
-   response.writeHead(200);
+   if(pathname == '/') {
      fs.readFile(`data/${title}`, 'utf8', function(err, description) {
        var template = `
        ...
        `;
+       response.writeHead(200);
        response.end(template);
      })
+   } else {
+     response.writeHead(404);
+     response.end('Not found');
+   }
  });

  app.listen(3000);
```

### 3. 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\git\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/ae8dfc6e983f4657e6b0cccaffc98b43db4ea9ec))  

{% highlight javascript linenos %}
var http = require('http');
var fs = require('fs');
var url = require('url');

var app = http.createServer(function(request,response){
  var _url = request.url;
  var queryData = url.parse(_url, true).query;
  var pathname = url.parse(_url, true).pathname;
  var title = queryData.id;
  if(pathname == '/') {
    fs.readFile(`data/${title}`, 'utf8', function(err, description) {
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
    response.writeHead(404);
    response.end('Not found');
  }
});

app.listen(3000);
{% endhighlight %}

### 4. 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  
-- main.js 의 40번 라인에서 설정해준 포트번호 3000을 반드시 함께 입력해야 합니다.  

![nodejs_readfile_002](https://drive.google.com/uc?id=1tQ0Pxi9Of2xy35hJodyca1jIfL4VQNKW)  

- 사용자가 찾을 수 없는 요청을 보낸 경우 Not found 오류메세지를 전송합니다.  
