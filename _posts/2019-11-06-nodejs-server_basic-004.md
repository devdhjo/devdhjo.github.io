---
layout: post
title: "[nodejs] 004# MODULE 사용하기"
tags: [nodejs, server_basic]
categories: nodejs
---

#### ◇ require('모듈명');  

require() 함수는 js 파일을 모듈화 하기 위해 사용하는 내장객체입니다.  
이전 포스팅에서 http, url 과 같은 모듈을 객체화 할 때 사용했던 함수입니다.  


### 1. 모듈 생성  

custom_module.js 파일을 생성하여 호출할 모듈을 작성합니다.  

{% highlight javascript linenos %}
// 1. exports 객체를 사용해서 sum 이라는 변수를 만들고, sum 변수에 function 을 사용해서 하나의 파라미터를 가진 함수식 대입
exports.sum = function(max) {
  // 2. 1부터 입력된 값 까지 더하여 반환하는 로직
  return (max+1)*max/2;
}

// 3. num 변수에 'NEW VALUE 100' 입력
exports.num = 'NEW VALUE 100';
{% endhighlight %}

- node.js 에서 모듈 형태로 사용하기 위해서는 내장객체인 exports 를 사용합니다.  
exports 객체로 사용할 변수명을 선언하고, 해당 변수에 함수, 값 또는 객체를 대입합니다.  

- 함수형 언어에서 함수는 객체지향의 객체와 같이 각각의 함수가 객체처럼 포인터를 가집니다.  
따라서 예제에서 처럼 함수식 자체를 변수에 대입하는 것이 가능합니다.  

```javascript
var 변수명 = function(파라미터) { 함수식 };
```


### 2. 모듈을 호출 할 파일 생성  

module_example.js 파일에서 이전 단계에 생성한 module을 호출하도록 작성합니다.  

{% highlight javascript linenos %}
var module = require('./custom_module');

// 1. formatted 특수문자 %d 를 사용해서 module.sum() 에서 리턴된 숫자값을 출력
console.log('sum = %d' , module.sum(100));

// 2. formatted 특수문자 %s 를 사용해서 module.num 의 문자값을 출력
console.log('num = %s' , module.num);
{% endhighlight %}


### 3. 실행 및 접속  

CMD 창을 열어 server_basic 폴더로 이동한 후 module_example.js 를 실행합니다.  
(window키 + R → 'cmd' 입력하여 실행)  

![module-example-cmd](https://drive.google.com/uc?id=1pPn0JlHD8YG7Fh5YW5eYMPkYd-_uBI1x)  


### 4. 소스코드 분석  

- 함수를 객체처럼 사용하기  

```javascript
var 변수명 = function(파라미터) { 함수식 };
```

-- 함수형 언어에서 함수는 객체지향의 객체와 같이 각각의 함수가 포인터를 가집니다.  
따라서 변수에 함수식 자체를 대입하는 것이 가능합니다.  

#### 1) custom_module.js  

```javascript
exports.sum = function(max) {
  return (max+1)*max/2;
}

exports.num = 'NEW VALUE 100';
```

-- nodejs 에서 모듈 형태로 사용하기 위해서는 내장 객체인 exports 를 사용합니다.  
   exports 객체로 먼저 사용할 변수명을 선언하고, 해당 변수에 함수나 값 또는 객체를 대입하여 사용합니다.  

- 여러 종류 문자열 출력하기  

```javascript
console.log('var1 = %d, var2 = %s, var3 = %d', 100, 'Hello', 56789);
```

-- nodejs 의 console.log() 함수는 formatted 출력을 지원합니다.  
   따라서 %d, %s 등을 사용하여 입력되는 값을 문자열 내부에 출력할 수 있습니다.  

#### 2) module_example.js  

```javascript
console.log('sum = %d' , module.sum(100));
console.log('num = %s' , module.num);
```

-- module 의 sum() 함수 호출 결과를 %d 를 사용하여 숫자값으로 출력합니다.  
   module 의 num 변수값을 %s 를 사용하여 문자값으로 출력합니다.  
