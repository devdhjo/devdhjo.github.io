---
layout: post
title: "[MySQL] 001# MySQL 설치하기 - Windows"
tags: [database, MySQL]
categories: mysql
---

### 1. MySQL 설치 프로그램 다운로드  

#### 1) [https://www.mysql.com/downloads/](https://www.mysql.com/downloads/) 에 접속합니다.  

- 페이지 하단에 [MySQL Community (GPL) Downloads](https://dev.mysql.com/downloads/) 버튼을 클릭합니다.

![mysql_download_001](https://cphinf.pstatic.net/mooc/20180130_220/1517301639758tipul_PNG/2_7_2_01.png?type=w760)  

#### 2) [MySQL Community Server](https://dev.mysql.com/downloads/mysql/) 를 선택합니다.  

![mysql_download_002](https://cphinf.pstatic.net/mooc/20180130_50/15173016771322yd4l_PNG/2_7_2_02.png?type=w760)  

#### 3) 사용하는 운영체제를 선택하고, MySQL Installer MSI 다운로드를 선택합니다.  

![mysql_download_003](https://cphinf.pstatic.net/mooc/20180130_141/1517301716304Elg4m_PNG/2_7_2_03.png?type=w760)  

#### 4) '-web-' 이 붙어있지 않은 아래쪽 MSI Installer 를 다운로드 합니다.  

![mysql_download_004](https://cphinf.pstatic.net/mooc/20180130_178/1517301741527LIfS9_PNG/2_7_2_04.png?type=w760)  

#### 5) 로그인 하지 않고 다운로드 하려면 'No thanks, just start my download.' 를 클릭합니다.  

![mysql_download_005](https://cphinf.pstatic.net/mooc/20180130_26/1517301764096QbT2o_PNG/2_7_2_05.png?type=w760)  

-- MySQL Community Edition 다운로드가 완료되면, mysql-installer-community-5.7.21.0.msi 파일을 실행합니다.  

### 2. MySQL Installer 실행  

#### 1) 설치 프로그램을 실행하여 라이센스에 동의한 후 Next > 버튼을 클릭합니다.  

![mysql_download_006](https://cphinf.pstatic.net/mooc/20180130_66/1517301795664jFdjh_PNG/2_7_2_06.png?type=w760)  

#### 2) Developer Default 를 선택합니다. 개발자를 위한 다양한 도구들이 함께 설치됩니다.  

![mysql_download_007](https://cphinf.pstatic.net/mooc/20180130_230/1517301822203xcY00_PNG/2_7_2_07.png?type=w760)  

#### 3) Execute 버튼을 클릭하여 설치를 진행합니다.  

![mysql_download_008](https://cphinf.pstatic.net/mooc/20180131_207/1517373007940UfqlB_PNG/2_7_2_07-1.png?type=w760)  

#### 4) 설치가 모두 Complete 되었다면 Next > 버튼을 클릭합니다.  
-- 이후 계정 설정하는 부분까지 기본값으로 설치를 진행합니다.  

![mysql_download_009](https://cphinf.pstatic.net/mooc/20180131_97/1517373135155jRYVo_PNG/2_7_2_10.png?type=w760)  

#### 5) MySQL 의 관리자 (root) 계정의 암호를 설정합니다.  
-- 이후 커넥션 테스트를 진행할 때 까지 기본값으로 설치를 진행합니다.  

![mysql_download_010](https://cphinf.pstatic.net/mooc/20180131_300/1517373194725igKRc_PNG/2_7_2_12.png?type=w760)  

#### 6) root 에 설정했던 암호를 입력하고 Check 버튼을 클릭합니다.  

![mysql_download_011](https://cphinf.pstatic.net/mooc/20180131_60/15173732195796I7yM_PNG/2_7_2_13.png?type=w760)  

-- 위와 같이 연결 성공이라는 녹색 체크표시가 보이면 Next > 버튼을 클릭합니다.  

#### 7) 모든 설치 과정이 끝났다면 Finish 버튼을 클릭합니다.  

![mysql_download_012](https://cphinf.pstatic.net/mooc/20180131_18/1517373256380a1AEr_PNG/2_7_2_14.png?type=w760)  

#### 8) Finish 버튼을 클릭하면 Workbench 와 Shell 이 실행됩니다.  

![mysql_download_013](https://cphinf.pstatic.net/mooc/20180131_221/1517373280390svBSo_PNG/2_7_2_15.png?type=w760)  

### 3. 환경변수 설정  

#### 1) 시스템 속성 창을 열어 '환경 변수' 버튼을 클릭합니다.  

![mysql_download_014](https://cphinf.pstatic.net/mooc/20180131_177/15173734556398WYJk_PNG/2_7_2_20.png?type=w760)  

#### 2) 시스템 변수의 Path 를 선택하고 '편집' 버튼을 클릭합니다.  

![mysql_download_015](https://cphinf.pstatic.net/mooc/20180131_38/15173734789828xbN0_PNG/2_7_2_21.png?type=w760)  

#### 3) '새로 만들기' 버튼을 클릭하고 아래의 path 를 입력한 후 '확인' 버튼을 클릭합니다.  

```
C:\Program Files\MySQL\MySQL Server 5.7\bin
```

![mysql_download_016](https://cphinf.pstatic.net/mooc/20180131_276/1517373550179G8GOy_PNG/2_7_2_22.png?type=w760)  

- 이제 콘솔창에서 'mysql' 명령을 실행할 수 있습니다.  
