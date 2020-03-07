---
layout: post
title: "[nodejs] 008# App ⑴ nodejs 설치"
tags: [nodejs, opentutorials, webapp]
categories: nodejs
---



### 1. nodejs 설치  

- Windows 기준으로 작성됩니다.  

![nodejs-download-001](https://drive.google.com/uc?id=1Eusl9MW8rSwYGuB81sHFaTkKsrUG9lG_)  

1) [https://nodejs.org](https://nodejs.org) 에 접속하여 설치 버튼을 클릭합니다. 저는 LTS 버전을 설치하였습니다.  

- LTS : 안정화 된 버전, 신뢰도가 높고 유지/보수와 보안에 초점을 맞춘 버전입니다.  
- Current : 최신 버전, 기존 API의 기능 개선에 초점을 맞춘 버전으로 업데이트가 잦고 기능이 변경될 수 있기 때문에 간단한 개발 및 테스트에 적합한 버전입니다.  

![nodejs-download-002](https://drive.google.com/uc?id=1Y90xSrkt6G7kFxpM6URjCsmyLZVDxbTN)  

2) 원하는 경로를 선택하여 설치를 진행합니다.  

![nodejs-download-003](https://drive.google.com/uc?id=1vBtq1CUzbOrlxu2SFaxnw0IQcffKlQNz)  

3) 설치가 완료되면 win + R 키를 눌러 cmd 를 실행합니다.  

```
node -v

node

console.log(1+1);
```
4) cmd 창에서 node -v 명령어를 실행하여 설치된 nodejs 의 버전을 확인할 수 있습니다.  

![nodejs-download-004](https://drive.google.com/uc?id=17u6CmJXK4G3N0k23wJTm6z2LfiiecMFn)  

5) node 명령어를 실행하면 nodejs 를 사용할 수 있게 됩니다. Ctrl + C 를 입력하면 종료됩니다.  
