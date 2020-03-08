---
layout: post
title: "[nodejs] 010# App ⑶ 동적인 웹페이지 만들기"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---


> ##### ▶ nodejs 의 App 포스팅은 생활코딩 ([https://opentutorials.org/course/1](https://opentutorials.org/course/1)) 에서 제공하는 강의를 기반으로 한 학습내용입니다.  
> ##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js ([https://opentutorials.org/course/3332](https://opentutorials.org/course/3332))  
> ##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



### ◇ 쿼리스트링에 따라 동작하는 웹페이지 만들기  
-- 009# 에서 실습한 샘플 페이지에 쿼리스트링을 이용하여 동적인 페이지를 생성하는 방법을 학습합니다.  


### 1. 샘플 프로젝트의 1.html 파일 소스를 복사합니다.  

- 1.html

```html
<!doctype html>
<html>
<head>
  <title>WEB1 - HTML</title>
  <meta charset="utf-8">
</head>
<body>
  <h1><a href="index.html">WEB</a></h1>
  <ol>
    <li><a href="1.html">HTML</a></li>
    <li><a href="2.html">CSS</a></li>
    <li><a href="3.html">JavaScript</a></li>
  </ol>
  <h2>HTML</h2>
  <p><a href="https://www.w3.org/TR/html5/" target="_blank" title="html5 speicification">Hypertext Markup Language (HTML)</a> is the standard markup language for <strong>creating <u>web</u> pages</strong> and web applications.Web browsers receive HTML documents from a web server or from local storage and render them into multimedia web pages. HTML describes the structure of a web page semantically and originally included cues for the appearance of the document.
  <img src="coding.jpg" width="100%">
  </p><p style="margin-top:45px;">HTML elements are the building blocks of HTML pages. With HTML constructs, images and other objects, such as interactive forms, may be embedded into the rendered page. It provides a means to create structured documents by denoting structural semantics for text such as headings, paragraphs, lists, links, quotes and other items. HTML elements are delineated by tags, written using angle brackets.
  </p>
</body>
</html>
```

### 2. main.js 파일을 수정합니다.  

#### 1) 009# 에서 생성한 main.js 파일에 template 이라는 변수를 만들고, 위에서 복사한 내용을 그 값으로 선언합니다.  

```diff
    ...
    }
    response.writeHead(200);
-   response.end(fs.readFileSync(__dirname + url));

+   var template = `
+     <!doctype html>
+     <html>
+     <head>
+       <title>WEB1 - HTML</title>
+       <meta charset="utf-8">
+     </head>
+     <body>
+       <h1><a href="index.html">WEB</a></h1>
+       <ol>
+         <li><a href="1.html">HTML</a></li>
+         <li><a href="2.html">CSS</a></li>
+         <li><a href="3.html">JavaScript</a></li>
+       </ol>
+       <h2>HTML</h2>
+       <p><a href="https://www.w3.org/TR/html5/" target="_blank" title="html5 speicification">Hypertext Markup Language (HTML)</a> is the standard markup language for <strong>creating <u>web</u> pages</strong> and web applications.Web browsers receive HTML documents from a web server or from local storage and render them into multimedia web pages. HTML describes the structure of a web page semantically and originally included cues for the appearance of the document.
+       <img src="coding.jpg" width="100%">
+       </p><p style="margin-top:45px;">HTML elements are the building blocks of HTML pages. With HTML constructs, images and other objects, such as interactive forms, may be embedded into the rendered page. It provides a means to create structured documents by denoting structural semantics for text such as headings, paragraphs, lists, links, quotes and other items. HTML elements are delineated by tags, written using angle brackets.
+       </p>
+     </body>
+     </html>`;

+   response.end(template);
  });
  ...
```

#### 2) 쿼리스트링을 사용하기 위한 모듈 url 을 임포트하고, 쿼리스트링 값을 저장할 변수를 선언합니다.  

-- url 이라는 변수의 값이 중복되기 때문에 function 내부의 url 이라는 변수명을 _url 로 수정합니다.  

```diff
    var http = require('http');
    var fs = require('fs');
+   var url = require('url');

    var app = http.createServer(function(request,response){
-       var url = request.url;
+       var _url = request.url;
+       var queryData = url.parse(_url, true).query;
+       var title = queryData.id;
-       if(request.url == '/'){
-           url = '/index.html';
-       }
+       if(_url == '/'){
+           title = 'Welcome';
+       }
-       if(request.url == '/favicon.ico'){
-           response.writeHead(404);
-           response.end();
-           return;
-       }
+       if(_url == '/favicon.ico'){
+           return response.writeHead(404);
+       }
        response.writeHead(200);
        ...
```

#### 3) 목록에서 선택하는 title 값을 쿼리스트링 id 값으로 사용합니다.  

-- 쿼리스트링 id 값을 화면에서도 확인할 수 있도록 template 내에 ${ } 로 감싼 title 변수를 사용합니다.  

```diff
   var template = `
     <!doctype html>
     <html>
     <head>
-      <title>WEB1 - HTML</title>
+      <title>WEB1 - ${title}</title>
       <meta charset="utf-8">
     </head>
     <body>
-      <h1><a href="index.html">WEB</a></h1>
+      <h1><a href="/">WEB</a></h1>
-      <ol>
-        <li><a href="1.html">HTML</a></li>
-        <li><a href="2.html">CSS</a></li>
-        <li><a href="3.html">JavaScript</a></li>
-      </ol>
+      <ul>
+        <li><a href="/?id=HTML">HTML</a></li>
+        <li><a href="/?id=CSS">CSS</a></li>
+        <li><a href="/?id=JavaScript">JavaScript</a></li>
+      </ul>
-      <h2>HTML</h2>
+      <h2>${title}</h2>
       <p><a href="https://www.w3.org/TR/html5/" target="_blank" title="html5 speicification">Hypertext Markup Language (HTML)</a> is the standard markup language for <strong>creating <u>web</u> pages</strong> and web applications.Web browsers receive HTML documents from a web server or from local storage and render them into multimedia web pages. HTML describes the structure of a web page semantically and originally included cues for the appearance of the document.
       <img src="coding.jpg" width="100%">
       </p><p style="margin-top:45px;">HTML elements are the building blocks of HTML pages. With HTML constructs, images and other objects, such as interactive forms, may be embedded into the rendered page. It provides a means to create structured documents by denoting structural semantics for text such as headings, paragraphs, lists, links, quotes and other items. HTML elements are delineated by tags, written using angle brackets.
       </p>
     </body>
     </html>`;
```

#### 4) 수정 된 main.js 를 실행합니다.  

- window + R → cmd 실행  

```
D:\workspace\git\nodejs\webapp>node main.js
```

- main.js ([수정사항 보기](https://github.com/devdhjo/nodejs_opentutorials/commit/61f84a83d235e7db273df8723ee31a84e9c54faf))  

{% highlight javascript linenos %}
var http = require('http');
var fs = require('fs');
var url = require('url');

var app = http.createServer(function(request,response){
  var _url = request.url;
  var queryData = url.parse(_url, true).query;
  var title = queryData.id;
  if(_url == '/'){
    title = 'Welcome';
  }
  if(_url == '/favicon.ico'){
    return response.writeHead(404);
  }
  response.writeHead(200);
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
      <p><a href="https://www.w3.org/TR/html5/" target="_blank" title="html5 speicification">Hypertext Markup Language (HTML)</a> is the standard markup language for <strong>creating <u>web</u> pages</strong> and web applications.Web browsers receive HTML documents from a web server or from local storage and render them into multimedia web pages. HTML describes the structure of a web page semantically and originally included cues for the appearance of the document.
      <img src="coding.jpg" width="100%">
      </p><p style="margin-top:45px;">HTML elements are the building blocks of HTML pages. With HTML constructs, images and other objects, such as interactive forms, may be embedded into the rendered page. It provides a means to create structured documents by denoting structural semantics for text such as headings, paragraphs, lists, links, quotes and other items. HTML elements are delineated by tags, written using angle brackets.
      </p>
    </body>
    </html>
    `;
  response.end(template);
});

app.listen(3000);
{% endhighlight %}


#### 5) 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  
-- main.js 의 41번 라인에서 설정해준 포트번호 3000을 반드시 함께 입력해야 합니다.  

![nodejs_querystring_001](https://drive.google.com/uc?id=132AUZ8t5NCwWUinRKYcO26vmg-Q78xe-)  

- 쿼리스트링에 따라 title 이 변경되는 것을 확인할 수 있습니다.  
