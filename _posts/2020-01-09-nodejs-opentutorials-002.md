---
layout: post
title: "[nodejs] 009# App ⑵ 웹서버 만들기"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---


##### ▶ nodejs 의 App 포스팅은 생활코딩 ([https://opentutorials.org/course/1](https://opentutorials.org/course/1)) 에서 제공하는 강의를 기반으로 한 학습내용입니다.  
##### ▶ 출처 : 생활코딩 > WEB > WEB2 - Node.js ([https://opentutorials.org/course/3332](https://opentutorials.org/course/3332))  
##### ▷ 해당 학습 과정의 프로젝트를 저의 Github Repository 에도 게시하였습니다. ([devdhjo/nodejs_opentutorials](https://github.com/devdhjo/nodejs_opentutorials))  



### 1. 학습 프로젝트 다운로드  

#### 1) 생활코딩에서 제공하는 샘플 프로젝트를 다운로드 받습니다.  

- [https://github.com/web-n/web1_html_internet](https://github.com/web-n/web1_html_internet)  

![nodejs-webserver-001](https://drive.google.com/uc?id=1jCe0jgJvYgxWxfDuPOmacIxBoZIHUIAB)  


#### 2) 프로젝트 디렉토리로 사용할 위치에 압축을 풀어줍니다.  

- 저는 D:\workspace\git\nodejs\webapp 를 프로젝트 디렉토리로 사용하였습니다.  

![nodejs-webserver-002](https://drive.google.com/uc?id=1Dm4F583K_eZEmT5_GixLCoUjlBa19EGb)  




### 2. main 파일 생성  

#### 1) 프로젝트 디렉토리에 main.js 파일을 생성합니다.  

- main.js  

{% highlight javascript linenos %}
var http = require('http');
var fs = require('fs');

var app = http.createServer(function(request,response){
    var url = request.url;
    if(request.url == '/'){
    	url = '/index.html';
    }
    if(request.url == '/favicon.ico'){
        response.writeHead(404);
        response.end();
        return;
    }
    response.writeHead(200);
    response.end(fs.readFileSync(__dirname + url));
});

app.listen(3000);
{% endhighlight %}




### 3. main 파일 실행  

#### 1) window + R 키를 눌러 cmd 를 입력하여 명령 프롬프트를 실행합니다.  

#### 2) 프로젝트 디렉토리에 액세스합니다.  

- 저는 D:\workspace\git\nodejs\webapp 를 프로젝트 디렉토리로 사용하였습니다.  

```
D:\>cd workspace/git/nodejs/webapp
```


#### 3) main.js 파일을 실행하는 명령어를 입력합니다.  

```
D:\workspace\git\nodejs\webapp>node main.js
```


#### 4) 브라우저를 열어 main.js 서버로 접속합니다.  

- [localhost:3000](localhost:3000)  
-- main.js 의 18번 라인에서 설정해준 포트번호 3000을 반드시 함께 입력해야 합니다.  

![nodejs-webserver-003](https://drive.google.com/uc?id=1ZSjPbrtIKp6AzMN21DSqfB4EMgoq2PMX)  

- 프로젝트 파일이 실행되는 것을 확인할 수 있습니다.  

- main.js 의 15번 라인의 코드 response.end() 에 입력한 내용에 따라 사용자에게 전송하는 데이터가 달라지는 것이 nodejs 의 특징입니다.  
