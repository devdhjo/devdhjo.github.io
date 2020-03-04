---
layout: post
title: "[nodejs] 007# 파일 입출력"
tags: [nodejs, server_basic]
categories: nodejs
---


#### ◇ 파일 I/O  

nodejs 는 파일을 읽고 쓰기 위해 동기와 비동기 두 가지 형태의 함수를 제공합니다. 이벤트 방식의 비동기를 지향하기 때문에 비동기 방식의 파일 읽고 쓰기가 default 입니다.  

이 포스팅에서는 url 모듈을 사용하여 요청된 자원이 파일인 경우 해당 파일을 읽어 클라이언트에 전달하는 예제를 실행해보겠습니다.  


### 1. 파일 읽기  

- file_read.js 파일을 생성합니다.  

{% highlight javascript linenos %}
// 1. fs (파일시스템) 모듈 사용
var fs = require('fs');

// 2. 비동기 방식의 파일 읽기
//    - 파일을 읽은 후 마지막 파라미터에 넘긴 callback 함수가 호출
fs.readFile('module_example.js', 'utf-8', function(error, data) {
  console.log('01 readAsync : %s', data);
});

// 3. 동기 방식의 파일 읽기
//    - 파일을 읽은 후 data 변수에 저장
var data = fs.readFileSync('module_example.js', 'utf-8');
console.log('02 readSync : %s', data);
{% endhighlight %}

- 1번의 readFile() 함수는 비동기방식으로 파일을 읽는 함수가 다른 thread 에 의해 실행되기 때문에 2번 readFileSync() 함수가 먼저 처리됩니다.  

![file-read-cmd](https://drive.google.com/uc?id=1jv19kfmMQYJ_1d2H3_xT5ZozD4OhO4Zx)  

### 2. 파일 쓰기  

- file_write.js 파일을 생성합니다.  

{% highlight javascript linenos %}
var fs = require('fs');

// 1. 새로 생성할 파일에 입력될 문자열
var data = "File Write Test";

// 2. 비동기 방식으로 파일을 생성
//  - 파라미터 : 파일명, 입력데이터, 인코딩, 콜백함수
fs.writeFile('file01_async.txt', data, 'utf-8', function(e) {
  if(e) {
    // 3. 파일 생성 중 오류가 발생한 경우 콘솔에 오류 출력
    console.log(e);
  } else {
    // 4. 파일 생성 중 오류가 없다면 완료 문자열 출력
    console.log('01 WRITE DONE!');
  }
});

// 5. 동기 방식은 callback 함수를 통한 오류 처리를 할 수 없기 때문에
//    함수 전체를 try 문으로 예외처리
try {
  // 6. 동기 방식으로 파일 생성
  //  - 파라미터 : 파일명, 입력데이터, 인코딩
  fs.writeFileSync('file02_sync.txt', data, 'utf-8');
  console.log('02 WRITE DONE!');
} catch(e) {
  console.log(e);
}
{% endhighlight %}

- 파일 읽기와 마찬가지로 동기방식인 2번 함수의 로그가 먼저 출력됩니다.  
- 텍스트 파일을 열면 data 변수에 지정한 문자열이 입력되어 있는 것을 확인할 수 있습니다.  

![file-write-result](https://drive.google.com/uc?id=1Ee72im-Rd8BpRXJciXMic32jsYmlHnih)  


### 3. 브라우저를 통한 파일 요청 처리  

#### 1) 요청 대상이 되는 html 파일을 작성합니다.  

- file_test.html  

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>file test</title>
  </head>
  <body>
    <h1>Hello</h1>
    클라이언트에서 파일 요청 처리 예제 입니다. </br>
    server_req_file.js 에서 처리합니다.
  </body>
</html>
```

#### 2) 요청을 처리할 server_req_file.js 파일을 작성합니다.  

- server_req_file.js  

{% highlight javascript linenos %}
var http = require('http');
var url = require('url');
var fs = require('fs');

var server = http.createServer(function(request, response) {
  var parsedUrl = url.parse(request.url);
  var resource = parsedUrl.pathname;

  // 1. 요청된 자원이 /test 이면
  if(resource == '/test') {
    // 2. file_test.html 파일을 읽은 후
    fs.readFile('file_test.html', 'utf-8', function(error, data) {
      // 2-1. 읽으면서 오류가 발생한 경우 오류 내용을 출력
      if(error) {
        response.writeHead(500, {'Content-Type':'text/html'});
        response.end('500 Internal Server Error : ' + error);
      } else {
      // 2-2. 오류 없이 정상적으로 읽기가 완료된 경우 파일의 내용을 클라이언트에 전달
        response.writeHead(200, {'Content-Type':'text/html'});
        response.end(data);
      }
    });
  } else {
    response.writeHead(404, {'Content-Type':'text/html'});
    response.end('404 Page Not Found');
  }
});

server.listen(9074, function() {
  console.log('Server is running...');
})
{% endhighlight %}

#### 3) 파일 실행  

- 2에서 생성한 server_req_file 을 실행한 후 [http://localhost:9074/test](http://localhost:9074/test) 를 요청하면 1에서 작성한 화면이 출력되는 것을 확인할 수 있습니다.  

![file-test-result](https://drive.google.com/uc?id=1QL5o1CX37JaEp4dcUvMMMAeuoS9YGwZ-)  
