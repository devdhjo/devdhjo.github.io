---
layout: post
title: "[nodejs] 002# 클라이언트 요청 처리 - GET"
tags: [nodejs, server_basic]
categories: nodejs
---

#### ◇ 클라이언트 GET 요청  
GET 방식은 주소를 이용하여 서버로 값을 전달하는 방식입니다.  
주소의 끝에 ?(물음표)를 붙이고, key1=value1&key2=value2&... 의 형태로 요청합니다.  

```
https://search.naver.com/search.naver?where=post&query=github
```

이렇게 실제 주소값 뒤에 ?(물음표) 다음의 값을 Query String 이라고 합니다.  
Query String 은 주소 형태로 요청할 수도 있고, HTML 의 form 태그를 사용할 수도 있습니다.  


### 1. server_req_get.js 파일 생성  

클라이언트의 GET 요청을 처리할 파일을 작성합니다.  

{% highlight javascript linenos %}
var http = require('http');

// 1. 요청한 url 을 객체로 만들기 위해 url 모듈 사용
var url = require('url');

// 2. 요청한 url 중 Query String 을 객체로 만들기 위헤 querystring 모듈 사용
var querystring = require('querystring');

var server = http.createServer(function(request, response) {
  // 3. 콘솔 화면에 로그 시작 부분을 출력
  console.log('--- log start ---');

  // 4. 브라우저에 요청한 주소를 parsing 하여 객체화 후 출력
  var parsedUrl = url.parse(request.url);
  console.log(parsedUrl);

  // 5. 객체화 된 url 중 Query String 부분만 따로 객체화하여 출력
  var parsedQuery = querystring.parse(parsedUrl.query, '&', '=');
  console.log(parsedQuery);

  // 6. 콘솔 화면에 로그 종료 부분을 출력
  console.log('--- log end ---');

  response.writeHead(200, {'Content-Type':'text/html'});
  response.end(parsedUrl.query);
});

server.listen(9074, function() {
  console.log('Server is running...');
});
{% endhighlight %}

### 2. 실행 및 접속  
CMD 창을 열어 server_basic 폴더로 이동한 후 server_req_get.js 를 실행합니다.  
(window키 + R → 'cmd' 입력하여 실행)  

![server-req-get-cmd](https://drive.google.com/uc?id=1DZK88w0qtEYKLbc2250MmL9_PwY1x4pP)  

정상적으로 실행된 로그를 확인했다면 웹브라우저를 열어 주소창에 다음과 같이 입력한 후 접속합니다.  
[http://localhost:9074/?key1=value1&key2=value2](http://localhost:9074/?key1=value1&key2=value2)  

아래와 같이 브라우저에 출력된다면 정상적으로 실행된 것입니다.  

![server-req-get-result](https://drive.google.com/uc?id=1CkvEa8Ifba6ygGQJcOI6LfCFq7P2WZBM)  


### 3. 로그 확인  
GET 요청 후 CMD 창을 보면 아래와 같이 로그가 출력된 것을 확인할 수 있습니다.  

![server-req-get-log](https://drive.google.com/uc?id=1DeGqgRSIelnxPzdGYudeS_mso_4apwP2)  

- 로그를 보면 2번 출력된 것으로 보입니다. 이것은 서버로 요청 시 브라우저 자체에서 기본적으로 표시되는 favicon 이라는 아이콘을 요청하였기 때문입니다.  
네이버 홈페이지를 예로 들면, 아래와 같이 페이지 타이틀 앞에 녹색 아이콘이 favicon 입니다.  

![favicon-naver](https://drive.google.com/uc?id=1cA7XoZa7GoQ4VFW6P5Xq1hmLxBBiHcyj)  

#### ◇ 로그 분석  

```json
--- log start ---
Url {
  protocol: null,
  slashes: null,
  auth: null,
  host: null,
  port: null,
  hostname: null,
  hash: null,
  search: '?key1=value1&key2=value2',
  query: 'key1=value1&key2=value2',
  pathname: '/',
  path: '/?key1=value1&key2=value2',
  href: '/?key1=value1&key2=value2'
}
```

- var parsedUrl = url.parse(주소); 함수로 주소값을 객체화 한 부분입니다.  
parsedUrl.search 형태로 객체 내부의 변수값을 사용할 수 있습니다.  

```json
[Object: null prototype] { key1: 'value1', key2: 'value2' }
--- log end ---
```

- var parsedQuery = querystring.parse(parsedUrl.query, '&', '='); 함수로  
Url 객체 내부의 query 라는 변수값을 querystring 으로 parsing 하여 객체화 하였습니다.  
parsedQuery.key1 형태로 객체 내부의 값을 사용할 수 있습니다.  


### 4. 소스코드 분석  

```javascript
var http = require('http');

// 1. 요청한 url 을 객체로 만들기 위해 url 모듈 사용
var url = require('url');

// 2. 요청한 url 중 Query String 을 객체로 만들기 위헤 querystring 모듈 사용
var querystring = require('querystring');
```

- url 모듈은 클라이언트가 요청한 주소값을 javascript 객체로 변환하여 사용할 수 있게 하는 모듈입니다.  
- querystring 모듈은 주소로 전달된 Query String 을 변환하여 javascript 객체로 사용할 수 있게 하는 모듈입니다.  

```javascript
  console.log('--- log start ---');
  /*
  	...
  */
  console.log('--- log end ---');
```

- 서버에서 처리되는 내용을 콘솔에 출력하는데 로그의 시작과 끝을 알기 위해 출력합니다.  


```javascript
  // 4. 브라우저에 요청한 주소를 parsing 하여 객체화 후 출력
  var parsedUrl = url.parse(request.url);
  console.log(parsedUrl);
```

- url 모듈의 parse() 함수를 이용하여 request 객체에 있는 url 값을 parsedUrl 변수에 담아 출력합니다.  
- request 객체 내부에 url 이라는 변수가 존재하고, 이 url 변수는 주소를 문자열 값으로 가지고 있습니다.  


```javascript
  // 5. 객체화 된 url 중 Query String 부분만 따로 객체화하여 출력
  var parsedQuery = querystring.parse(parsedUrl.query, '&', '=');
  console.log(parsedQuery);
```

- parsedUrl 에는 객체화 된 url 값이 들어있습니다. 이 중 Query String 이 담겨있는 query 변수를 가져온 후 querystring 모듈의 parse() 함수를 이용하여 객체화 합니다.  
- querystring.parse() 함수의 파라미터로 지정된 문자열에 따라 Query String의 구분자로 parsing 할 수 있습니다.  


```json
{ key1: 'value1', key2: 'value2' }
```

- parsedQuery 에 담긴 객체를 parsedQuery.key1 형태로 사용할 수 있습니다.  
