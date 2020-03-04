---
layout: post
title: "[nodejs] 003# 클라이언트 요청 처리 - POST"
tags: [nodejs, server_basic]
categories: nodejs
---

#### ◇ 클라이언트 POST 요청  

POST 방식은 데이터를 주소가 아닌 요청 BODY에 담아 서버측에 전달합니다.  

#### ◆ HTTP 프로토콜의 구조  

HTTP 프로토콜은 브라우저에서 서버로 요청(REQUEST) 하거나 서버에서 브라우저로 응답(RESPONSE) 할 때 서로 데이터를 주고받게 되는데, 이 데이터의 구조는 요청에 대한 설정 정보가 담기는 HEADER와 실제 데이터가 담기는 BODY로 구성됩니다.  

-- 아래는 특정 주소를 서버로 요청 시 서버에 전달되는 데이터 형태입니다.  

```c
// Request : 실제 요청에 대한 정보가 저장되는 부분
POST /request/specific_datas.call HTTP/1.1

// Request Header : 요청에 대한 설정 정보, 요청 데이터 타입, 요청자의 브라우저 정보 등이 담긴다.
Accept: image/gif, image/xxbitmap, image/jpeg, image/pjpeg,
application/xshockwaveflash, application/vnd.msexcel,
application/vnd.mspowerpoint, application/msword, */*
Referer: http://wahh-app.com/books/default.asp
Accept-Language: en-gb,en-us;q=0.5
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1)

// Request Body : 실제 전달하고자 하는 데이터가 담긴다.
specific_datas
```


### 1. server_req_post.js 파일 생성  

클라이언트의 POST 요청을 처리할 파일을 작성합니다.  

{% highlight javascript linenos %}
var http = require('http');

var querystring = require('querystring');

var server = http.createServer(function(request, response) {
  console.log('--- log start ---');

  // 1. post로 전달된 데이터를 담을 변수 선언
  var postData = '';

  // 2. request 객체에 on() 함수로 'data' 이벤트를 연결
  request.on('data', function(data) {
    // 3. data 이벤트가 발생할 때 마다 callback 을 통해 pastData 변수에 저장
    postData += data;
  });

  // 4. request 객체에 on() 함수로 'end' 이벤트를 연결
  request.on('end', function() {

    // 5. end 이벤트가 발생하면 (1회만 발생) 3번에서 저장해 둔 postData 를 querystring 으로 객체화
    var parsedQuery = querystring.parse(postData);

    // 6. 객체화 된 데이터를 로그로 출력
    console.log(parsedQuery);

    console.log('--- log end ---');

    // 7. 성공 HEADER 와 데이터를 담아 클라이언트에 응답 처리
    response.writeHead(200, {'Content-Type':'text/html'});
    response.end('<p>'+postData+'</p>');
  });

});

server.listen(9074, function(){
  console.log('Server is running...');
});
{% endhighlight %}

### 2. 실행 및 접속  

CMD 창을 열어 server_basic 폴더로 이동한 후 server_req_post.js 를 실행합니다.  
(window키 + R → 'cmd' 입력하여 실행)  

![server-req-post-cmd](https://drive.google.com/uc?id=1Szlxs03ZrfG8uLppHV5uSPy3s_5FXZTR)  

정상적으로 실행된 로그를 확인했다면 웹브라우저를 열어 주소창에 다음과 같이 입력한 후 접속합니다.  
[http://localhost:9074/?key1=value1&key2=value2](http://localhost:9074/?key1=value1&key2=value2)  

GET 요청 방식과 동일한 주소로 브라우저를 통해 호출하면 아래와 같은 결과가 나타납니다.  
POST 방식은 BODY 부분의 데이터를 처리하기 때문에 주소로 넘어온 데이터를 찾을 수 없기 때문입니다.  

![server-req-post-result](https://drive.google.com/uc?id=1UbhgUSPvQWMACBnoy1xoqQj5xXXkYk-S)  


### 3. 'POSTMAN' 프로그램을 이용한 POST 요청 전달  

#### 1) POST 요청을 전달하기 위한 프로그램 'POSTMAN' 을 설치합니다.  
[https://www.getpostman.com/downloads/](https://www.getpostman.com/downloads/)  

#### 2) POST을 실행한 후 아래와 같이 설정하여 요청을 전송합니다.  

![server-req-post-body-ex](https://drive.google.com/uc?id=1KNY--_MlodiNmZuGcEkWQixaUG64_ZGo)  

-- 요청한 데이터가 Response Body 에서 확인되는 것을 볼 수 있습니다.  


### 4. 소스코드 분석  

nodejs 는 일반적인 웹서버의 thread 동기 방식이 아닌 비동기 event 방식으로 데이터를 처리합니다.  
따라서 on() 함수를 사용하여 event 를 등록하고, 해당 event 가 발생하는 시점에 로직을 실행하는 구조가 가장 대표적인 event 처리를 위한 코드입니다.  
이벤트를 등록하게 되면 로직의 순서와는 상관 없이 이벤트가 발생했을 때만 동작하게 됩니다.  

```javascript
  request.on('data', function(data) {

  });
```

- request 객체에 on() 함수를 사용하여 'data' 라는 이벤트를 catch 합니다.  
-- 서버를 구동시키는 server.listen() 함수와 비슷하지만 차이점은 on() 은 이벤트 명칭을 정의합니다.  
이로 인해 특정 이벤트가 발생하는 경우에만 로직을 실행하게 됩니다.  

```javascript
  request.on('end', function(data) {

  });
```

- on() 함수와 같은 형태로 end 이벤트도 처리할 수 있는데, data 이벤트는 전송하는 데이터의 크기가 길 경우 여러번에 나누어서 발생하지만 end 이벤트는 data 전송이 완료되었을 때 1회만 발생합니다.  


#### cf. POST 요청 및 응답 Header  

- Request Header  

![server-req-post-header-request](https://drive.google.com/uc?id=1iJwOOy4BIzF_mIpbiohHAl0ELHpeY0ui)  

- Response Header  

![server-req-post-header-response](https://drive.google.com/uc?id=1P5xDoj-HXioqOI5otcWg33YGVpL8tEIJ)  
