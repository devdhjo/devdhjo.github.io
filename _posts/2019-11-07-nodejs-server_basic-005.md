---
layout: post
title: "[nodejs] 005# EVENT 처리"
tags: [nodejs, server_basic]
categories: nodejs
---


### ◇ Node.js 의 이벤트 처리  

- nodejs 는 이벤트 기반 비동기 방식의 서버 프레임워크 입니다.  
-- nodejs 에서는 기본적으로 아래의 세 가지 함수와 객체를 이용하여 이벤트를 처리합니다.  

#### 1) EventEmmiter  

- nodejs 의 모든 이벤트 처리가 정의된 기본 객체입니다.  
-- 이벤트를 사용하기 위해서는 이 객체를 재정의하여 사용해야 할 수 있습니다.  

#### 2) on()  

- 이벤트를 연결하는 함수입니다.  
-- 이전 포스팅에서 request 객체에 on() 함수를 이용하여 'data' 라는 이벤트를 캐치했던 것 처럼 사용됩니다.  

#### 3) emit()  

- 이벤트를 발생시키는 함수입니다.  
-- on() 함수에서 'data' 라는 이벤트가 캐치되기 위해서는 emit('data')의 형태로 이벤트를 발생시켜야 합니다.  


### 1. 이벤트를 가진 객체 만들기  

custom_event.js 파일을 생성하여 events 모듈을 재정의하여 사용하도록 작성합니다.  

  - custom_event.js  

{% highlight javascript linenos %}
// 1. 이벤트가 정의되어 있는 events 모듈 생성 (process.EventEmitter() 는 deprecated)
var EventEmitter = require('events');

// 2. 생성된 이벤트 모듈을 사용하기 위해 custom_object 로 초기화
var custom_object = new EventEmitter();

// 3. events 모듈에 선언되어 있는 on() 함수를 재정의하여 'call' 이벤트를 처리
custom_object.on('call', ()=> {
  console.log('~~~called events~~~');
})

// 4. call 이벤트를 발생
custom_object.emit('call');
{% endhighlight %}

- 실행결과  

CMD 창에서 node custom_event 를 실행하면 다음과 같은 결과를 확인할 수 있습니다.  

![custom-event-cmd](https://drive.google.com/uc?id=1pjZTgtckAG_BOOjd0irjkpin4G3VoUat)  


### 2. 매 초 이벤트를 이용한 타이머 모듈 만들기  

#### 1) 매 초 이벤트를 이용하여 콘솔에 주기적으로 현재 시간을 출력하는 타이머 모듈 생성  

- custom_module_timer.js  

{% highlight javascript linenos %}
var EventEmitter = require('events');

// 1. setInterval 함수가 동작하는 interval 값을 설정합니다.
var sec = 1;

// 2. timer 변수를 EventEmitter 로 초기화
exports.timer = new EventEmitter();

// 3. javascript 내장 함수인 setInterval 을 사용하여
//    1초에 한 번씩 timer 객체에 tick 이벤트 발생
setInterval(function() {
  exports.timer.emit('tick');
}, sec*1000);
{% endhighlight %}

#### 2) 타이머 모듈에서 발생하는 이벤트를 캐치하여 작동하는 파일 생성  

- call_timer.js  

{% highlight javascript linenos %}
var module = require('./custom_module_timer');

// 1. module 내부에 선언된 timer 객체를 통해 tick 이벤트를 캐치
module.timer.on('tick', function(time) {
  // 2. 현재 시간을 가져오기 위한 Date 객체 생성
  var time = new Date();

  // 3. 이벤트 발생 시 마다 현재 시간을 출력
  console.log('time : ' + time);
});
{% endhighlight %}


#### 3) 실행  

CMD 창을 열어 server_basic 폴더로 이동한 후 call_timer.js 를 실행합니다.  
(window키 + R → 'cmd' 입력하여 실행)  

![call-timer-cmd](https://drive.google.com/uc?id=1D1w_og2NA17ip3CfaMI5ohpGv6VYgAtf)  

-- setInterval() 함수를 사용하여 매 초마다 현재 시간을 콘솔에 출력하는 것을 확인할 수 있습니다.  
