---
title: R과 MySQL 데이터베이스 연결 방법
author: 양우성
layout: post
permalink: /2013/06/how-to-connect-r-with-mysql/
dsq_thread_id:
  - 1407315170
categories:
  - R-Tips
tags:
  - Big data
  - MySQL
  - R
  - RMySQL
  - 빅데이터
---
<img class="aligncenter" alt="R + MySQL" src="http://i0.wp.com/farm6.staticflickr.com/5493/9065201916_d7002b922c_o.png?w=550" data-recalc-dims="1" />

데이터베이스에 연결된 R 환경은 데이터베이스의 저장 용량과 R의 계산능력 사용할 수 있습니다. 이번 포스팅에서는 MySQL과 R을 서로 연결하는 방법을 정리해 보겠습니다.

MySQL과 R을 연결하기 위해서는 `RMySQL` 패키지를 이용합니다. 이 패키지는 R 환경을 위한 데이터베이스 인터페이스와 MySQL 드라이버를 포함한 패키지로 주로 다음과 같은 상황에서 사용할 수 있습니다.

*   데이터가 MySQL 데이터베이스에 저장되어 있고 R 환경에서 이 데이터를 추출해 분석하고 싶을 때
*   R에서 작성한 데이터를 MySQL의 데이터베이스에 저장하고 싶을 때

MySQL에 ODBC드라이버를 인스톨하면 `RODBC`을 이용해도 같은 기능을 실현할 수 있습니다. 최근 버전의 `RMySQL`는 `DBI` 패키지에 구현된 데이터베이스 인터페이스 정의를 따르고 있습니다.  
<!--more-->

## MS Windows

R 2.12 이후로는 RMySQL의 바이너리 파일이 배포되지 않기 때문에 사용자가 컴파일해서 설치해야 합니다.

### 필수 소프트웨어

*   [Rtools][1] (R의 버전에 대응하는 것)
*   MySQL Client C API library (MySQL Community Server 에도 포함되어있음) 
    *   MySQL Community Server를 PC에 디폴트로 설치한 경우에는 MySQL Client C API library가 이미 들어가 있기 때문에 별도로 설치할 필요는 없습니다. 단, 별도의 호스트에 설치된 MySQL Server를 이용하는 경우에는 MySQL Client C API library를 별도로 설치해야 합니다.

#### RMySQL 컴파일 순서

1. R의 환경변수 MYSQL_HOME에 MySQL 설치 디렉터리를 8.3형식으로 지정합니다.  
MySQL Community Server 5.6의 MSI Installer판을 디폴트로 설치한 경우  
`C:\Program Files\R\설치한 R 버전\etc` 디렉터리에 `Renviron.site`라는 이름으로 텍스트 파일을 작성하여 이하의 내용을 입력합니다.

    MYSQL_HOME=C:/PROGRA~1/MySQL/MYSQLS~1.6
    

2. MySQL이 설치된 디렉터리에 있는 `lib\libmysql.lib`을 `lib\opt\libmysql.lib`로 복사합니다.

3. R에서 다음 명령을 실행합니다. 이때 R 콘솔에서 `\Rtools\bin`, `\Rtools\gcc-4.6.3\bin` 이하의 실행 파일들을 사용하게 되므로 두 디렉터리의 Path가 지정되어 있어야 합니다(윈도즈에서 Path를 추가하는 방법은 [이곳][2]을 참조하세요). 만약 RStudio에서 컴파일을 한다면 Path 지정 필요 없이 그냥 알아서 해 줍니다.

<pre><code class="r">&gt; install.packages("DBI")
&gt; install.packages("RMySQL", type = "source")
</code></pre>

## 리눅스 (Ubuntu)

다음 명령을 실행하여 R과 MySQL을 설치합니다.

    sudo apt-get update
    sudo apt-get install r-base
    sudo apt-get install r-dev
    sudo apt-get install r-cran-rmysql
    sudo apt-get install r-cran-dbi
    sudo apt-get install mysql-server my-client
    sudo apt-get install libmysqlclient-dev
    

윈도즈의 삽질에 비하면 정말 간단하죠? R의 태생이 그런지라 불편 없이 R을 사용하고자 한다면 리눅스 환경을 강력 추천합니다.

## 사용법

R과 MySQL이 같은 호스트에서 실행되고 있고, test\_db라는 데이터베이스에 test\_user라는 사용자가 접근권한을 가지고 있다고 가정할 때, MySQL의 test\_db 안의 test\_table이라는 테이블의 내용을 쿼리를 이용하여 R의 데이터프레임 test.table로 저장하기 위해서는

<pre><code class="r">&gt; library(RMySQL)
&gt; con &lt;- dbConnect(m, dbname = "test_db", user = "test_user")
&gt; query.result &lt;- dbSendQuery(con, "SELECT * FROM test_table")
&gt; test.table &lt;- fetch(query.result)
&gt; dbDisconnect(con)
</code></pre>

여기서 `dbConnect()`는 데이터베이스와 접속을 개시하는 함수, `dbDisconnect()`는 접속을 종료하는 함수입니다. `dbSendQuery()`은 SQL문을 MySQL서버에 보내는 함수이며 그 응답을 받아 R의 데이터프레임으로 저장해 주는 함수가 `fetch()`입니다.

`dbSendQuery()`와 `fetch()`를 동시에 수행하는 `dbGetQuery()` 함수를 사용하여도 무방합니다.

<pre><code class="r">&gt; library(RMySQL)
&gt; con &lt;- dbConnect(dbDriver("MySQL"), dbname = "test_db", user = "test_user", 
+     password = "password")
&gt; dbListTables(con)  #DB abc에 있는 테이블목록 확인
&gt; test.table &lt;- dbGetQuery(con, "SELECT * FROM test_table")
&gt; dbDisconnect(con)
</code></pre>

R에서 작성된 result라는 데이터프레임을 MySQL 데이터베이스에 `test_table2`라는 테이블로 저장하기 위해서는 `dbWriteTable()` 함수를 이용합니다.

<pre><code class="r">&gt; dbWriteTable(con, "test_table2", result, overwrite = TRUE)
</code></pre>

만약, MySQL 서버가 R과 같은 호스트에 있지 않고 dbserver.domain라는 호스트에서 운영되고 있다면、

<pre><code class="r">&gt; library(RMySQL)
&gt; con &lt;- dbConnect(dbDriver("MySQL"), host = "dbserver.domain", dbname = "test_db", 
+     user = "test_user", password = "password")
&gt; 
</code></pre>

R 언어에서 명시적으로 데이터베이스 사용자 및 암호를 표시할 때 보안상의 문제점을 일으킬 수 있으므로 로컬 디렉터리 구성 파일 (~/.my/cnf)에서 MySQL을 사용하는 사용자 그룹을 정의 할 수 있습니다.

    [local]
    user = root
    password = ultra_secret
    host = localhost
    
    [nerds]
    user = supernerd
    password = galaxy
    host = dbserver.domain
    

그런 다음 R 사용자 그룹을 사용하여

<pre><code class="r">&gt; library(RMySQL)
&gt; con &lt;- dbConnect(MySQL(), group = "nerds", dbname = "test_db")
&gt; test.table &lt;- dbGetQuery(con, "SELECT * FROM test_table")
&gt; dbDisconnect(con)
</code></pre>

지금까지 R과 MySQL을 연결하는 방법을 방법에 대해 정리해 보았습니다. 적절한 SQL 쿼리를 이용해 필요한 데이터를 만들 수 있다면 데이터 전체를 R로 불러와 데이터 조작을 하는 것보다 메모리의 효율적인 사용에 매우 큰 도움이 될 수 있습니다.

&nbsp;

 [1]: http://cran.rstudio.com/bin/windows/Rtools/
 [2]: http://mwultong.blogspot.com/2006/08/xp-path.html