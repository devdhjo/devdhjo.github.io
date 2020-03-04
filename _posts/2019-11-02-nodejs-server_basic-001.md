---
layout: post
title: "[nodejs] 001# 서버 구축하기"
tags: [nodejs, server_basic]
categories: nodejs
---

### 1. 서버구축 프로젝트 폴더 생성  

서버를 구축하기 위한 프로젝트 폴더를 생성합니다. 저는 server_basic 이라는 이름으로 생성하였습니다.  

![create-workspace-folder](https://drive.google.com/uc?id=1D8GRN94R8gKIiy83u7NceqEXoHGkIbhs)

### 2. server.js 파일 생성  

server_basic 폴더 아래에 server.js 파일을 생성합니다.  
포트번호는 사용자 환경에 따라 설정합니다. 저는 9074번으로 설정하였습니다.  

{% highlight javascript linenos %}
// 1. 서버 사용을 위해 http 모듈을 변수에 담는다.
var http = require('http');

// 2. http 모듈로 서버를 생성한다.
var server = http.createServer(function(request, response) {
  response.writeHead(200, {'Content-Type':'text/html'});
  response.end('Hello node.js!!');
})

// 3. listen 함수로 9074번 포트를 가진 서버를 실행한다.
server.listen(9074, function(){
  console.log('Server is running...');
})
{% endhighlight %}

### 3. server.js 실행 및 접속  
CMD 창을 열어 server_basic 폴더로 이동한 후 server.js 를 실행합니다.  
(window키 + R → 'cmd' 입력하여 실행)  

![cmd-serverjs](https://drive.google.com/uc?id=1LPr1O0IaC8m1Z7iLE7PWOI-t-llgoxhK)  

정상적으로 실행된 로그를 확인했다면 웹브라우저를 열어 주소창에 다음과 같이 입력한 후 접속합니다.  
[http://localhost:9074](http://localhost:9074)  

아래와 같이 브라우저에 출력된다면 정상적으로 실행된 것입니다.  
![serverjs-result](https://drive.google.com/uc?id=10Bo2C2KGehkv_i2IfNGA0m9U1ZjqoUpC)  


### ◆ 소스코드 분석  

```javascript
// 1. 서버 사용을 위해 http 모듈을 변수에 담는다.
var http = require('http');
```

- 웹서버를 실행하기 위해 http 모듈을 require 함수로 불러옵니다.  
require는 다른 언어의 import 와 유사한 기능입니다.  

```javascript
// 2. http 모듈로 서버를 생성한다.
var server = http.createServer(function(request, response) {

})
```

-  http 모듈에 정의되어 있는 createServer 함수로 서버를 생성합니다.  
createServer 에 파라미터로 입력되는 function 은 함수명이 없습니다.  
함수명 없이 작성 된 function 의 파라미터는 이벤트 발생 시 callback 됩니다.  
즉, 생성된 서버로 어떤 요청이 들어오면 function 내부 로직이 실행되면서  
function 내부에 선언한 request 와 response 라는 이름으로 사용할 수 있는 값을 넘겨줍니다.  
- request 객체는 사용자가 요청한 내용이 담겨있는 객체입니다.  
- response 객체는 웹브라우저 또는 앱으로부터 서버로 어떤 요청이 있을 때  
요청한 사용자 쪽으로 값을 반환해줄 때 사용하는 객체입니다.  

```javascript
response.writeHead(200, {'Content-Type':'text/html'});
```

- writeHead(코드, {키 : 값}) - 응답 헤더 (서버로부터 반환되는 값에 대한 기본 정보) 를 설정하는 함수  
- 브라우저는 응답헤더 값을 보고 서버에서 넘어온 값의 형태를 파악하고, 실제 값을 header 에 세팅된 설정에 맞게 화면에 보여주게 됩니다.  
- 200 : 웹서버에 들어온 요청에 대해 서버에서 정상적으로 처리되었다는 의미의 코드 200을 전송  
- {'Content-Type':'text/html'} : 서버에서 보내는 컨텐츠 타입이 텍스트이고, html 형태라는 것을 정의  
-- { } 블럭 형태로 값이 전달되는 경우, 해당 블럭에 복수 개의 값이 담길 수 있다는 의미입니다.  
-- Content-Type 외에도 Authrization, Cookie 등 다양한 값을 지정할 수 있습니다.  

```javascript
response.end('Hello node.js!!');
```

- end() 함수를 이용하여 실제 컨텐츠를 담아 브라우저에 전달합니다.  
브라우저는 해당 컨텐츠를 html 형태로 화면에 출력합니다.
