---
layout: post
title: "[nodejs] 006# URL 다루기"
tags: [nodejs, server_basic]
categories: nodejs
---


#### ◇ 서버 URI  

URL 모듈은 클라이언트가 요청한 주소를 parsing 하여 서버 내의 실제 로컬 자원만 따로 처리할 수 있도록 해줍니다.  
기존 URL 주소체계에서 서버 리소스는 '디렉토리+파일' 형태였으나, RESTful 이 표준화 된 지금은 특정 도메인 서버가 가지는 리소스를 식별하는 서버식별자로 표현하는 것이 더 적절할 것입니다.  

```http
* 전체 URL : http://www.naver.com/myPage/firstPage?section=15
- [ http://www.naver.com ]  : 도메인
- [ /myPage/firstPage ]     : 서버 URI
- [ ?section=15 ]           : 쿼리스트링
```


### 1. URI를 활용한 요청 처리  

server_req_uri.js 파일을 생성합니다.  

{% highlight javascript linenos %}
var http = require('http');
var url = require('url');

var server = http.createServer(function(request, response) {
  // 1. 실제 요청한 주소 전체를 콘솔에 출력
  console.log(request.url);

  var parsedUrl = url.parse(request.url);

  // 2. parsing 된 url 중 서버 URI 에 해당하는 pathname 만 따로 저장
  var resource = parsedUrl.pathname;
  console.log('resource path = %s', resource);

  // 3. 리소스에 해당하는 문자열이 아래와 같으면 해당 메세지를 클라이언트에 전달
  if(resource == '/address') {
    response.writeHead(200, {'Content-Type':'text/html'});
    response.end('서울특별시 강남구');
  } else if(resource == '/phone') {
    response.writeHead(200, {'Content-Type':'text/html'});
    response.end('02-1234-5678');
  } else if(resource == '/name') {
    response.writeHead(200, {'Content-Type':'text/html'});
    response.end('Hong Gil Dong');
  } else {
    response.writeHead(404, {'Content-Type':'text/html'});
    response.end('404 Page Not Found');
  }
});

server.listen(9074, function() {
  console.log('Server is running...');
});
{% endhighlight %}


### 2. 서버 URI 요청 및 확인  

CMD 창에서 node server_req_uri 를 실행 후 브라우저에 접속합니다.  

#### 1) 기본 URL인 [http://localhost:9074](http://localhost:9074) 를 요청해봅니다.  

![server-req-uri-default](https://drive.google.com/uc?id=1Xd_iwALSmpHSsFpJBh5lJ_RR-DorVmvi)  

  -- 요청한 자원이 정의한 문자열에 해당되지 않기 때문에 페이지가 없다는 메세지가 출력됩니다.  
  -- 지정되지 않은 자원을 요청할 경우에도 동일한 결과를 확인할 수 있습니다.  

-  [http://localhost:9074/email](http://localhost:9074/email)  

![server-req-uri-email](https://drive.google.com/uc?id=1DcVUVpNTjT_UAXSvp7EsXyfYOzr_y_EH)  


#### 2) 미리 지정한 리소스를 요청합니다.  

-  [http://localhost:9074/address](http://localhost:9074/address)  

![server-req-uri-address](https://drive.google.com/uc?id=1_lcZbjFELldZRvNIngWsp1c-ZOL5RWB1)  

-  [http://localhost:9074/phone](http://localhost:9074/phone)  

![server-req-uri-phone](https://drive.google.com/uc?id=1wjxYQ32B9jWNRHbp9zdPXcDaqE9NX2Da)  

-  [http://localhost:9074/name](http://localhost:9074/name)  

![server-req-uri-name](https://drive.google.com/uc?id=1M6ejYy5zi7mLxZphSXdH0poLSE9DHni1)    

  -- 지정한 리소스에 해당하는 응답이 출력됩니다.  


  - 위와 같이 클라이언트에서 요청한 url 을 parsing 하여 요청한 자원에 따른 응답을 처리할 수 있습니다.  
