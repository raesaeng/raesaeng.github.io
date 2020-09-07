---
layout: post
title: "웹 어플리케이션 보안 -1"
author: "한결"
categories: blog
---


### 어플리케이션 보안

어플리케이션 보안은 보통 웹 보안을 말한다. 

컴퓨터 -> 웹 

스마트폰 -> 앱 = 웹

웹보안에서 신경쓰는 것은 접근제어나 계정보안이다.

1. 접근제어
2. 계정보안(인증)   -> id password 탈취당하는 걸 막도록 노력하는 것
                           -> 키 
3. 암호화 통신
4. 버전관리



### WWW World Wide Web

* 네트워크에 연결된 컴퓨터를 통해 정보를 공유할 수 있는 전 세계적 정보 공간
* 하이퍼텍스트로 텍스트와 각종 데이터를 교환하는 전송방식을 의미한다.

### 하이퍼텍스트 Hypertext

* HTML 등의 언어로 만들어진 하이퍼 텍스트 웹문서를 HTTP로 전송받아 웹브라우저로 실행해서 이용하는 것이 웹서비스이다.
* 하이퍼 텍스트를 표시하는 언어가 HTML이다. (ML : markup language)



### URI Uniform Resource Identifier = URL + URN

* 프로토콜://컴퓨터주소/경로명/파일명
* IT 업계에서는 이 용어를 많이 쓴다.

### URL Uniform Resource Locator

* 네트워크 상에서 서비스되는 자원의 정확한 위치를 나타내는 표준 규약
* 자원의 위치는 바뀔 수 있음
  * 프로토콜://컴퓨터주소
  * 프로토콜: 서버와 어플리케이션이 통신하기 위한 규약
  * 호스트네임 : 서버의 호스트네임 또는 IP 주소
  * 포트 : 브라우저와 통신하기 위하여 열려 있는 서버의 포트
  * Path to File : 서버 내의 자원의 위치. 디렉터리 / 파일명
  * 파라미터 : 동적으로 결과를 생성하는 어플리케이션에 전달하는 값
    * ? : URL 파일 경로 뒤에 붙어 파라미터를 전달할 때 사용한다.
    * = : 파라미터 항목과 값을 연결한다
    * & : 복수의 파라미터를 연결한다.
    * % : URL 인코딩
    * \+ : 공백

### URN Uniform Resource Name

* 자원의 위치와 관련 없는 자원 자체의 정보
* 식별번호 같은 것이기 때문에 바뀌지 않는다.
* URN:ISBN:1234567890



### 오늘날의 웹은 웹어플리케이션화 되어 해킹 요소가 많아졌다.

### 웹 어플리케이션 아키텍처

* 외부에서는 방화벽 때문에 웹에만 접근 할 수 있다. 이외는 접근할 수 있는 방법이 없다.
* 웹 어플리케이션은 3계층으로 구성된다.
  * 프론트 서버 + 미들웨어 + 백서버
  * Web Server + Web Application Server (WAS) + DB Server

### 서버사이드 Server Side

* 서버에서 ASP, JSP, PHP등과 같은 서버사이드 언어가 해석되고 실행되는 것
* 서버의 소스코드는 클라이언트가 볼 수 없다. 실행 결과만 볼 수 있음

### 클라이언트 사이드

* 클라이언트에서 HTML, JavaScript 등과 같은 언어가 해석되고 실행되는 것
* 클라이언트도 원본 소스를 볼 수 있다.



### 모든 보안 조치는 클라이언트 사이드가 아닌 서버사이드에서 이루어 져야 한다.

* 클라이언트 사이드는 우리가 코드를 쉽게 변경할 수 있기 때문. 단 보안 프로그램은 클라이언트 사이드 조치가 인정된다. 보안프로그램을 해킹하기 위해서 10년이 걸리기 때문이다.



### HTTP 프로토콜

* Hypertext Transfer Protocol
* 인터넷 핵심프로토콜
* 요청, 응답형식으로 동작한다.
* HTTP 1.1 버전을 거의 쓴다.
* Connectionless Protocol
* 1.0 부터 keep - alive 기능이 사용된다.
* 불필요한 메소드는 종료하기



### 웹 프록시

* 프록시를 통해 클라이언트가 다른 네트워크 서비스에 간접적으로 접속할 수 있게 하는 컴퓨터 시스템이나 응용 프로그램을 말한다. 서버와 클라이언트 사이에 중계기로 통신을 수행하는 것은 프록시, 중계기능을 하는 것은 프록시 서버이다.
* 모든 웹 통신을 중간에서 가로챔
  * http request
  * http response
* burp suite 웹 프록시 도구. 웹 해킹의 중심. 무기 같은 것. fiddler, paros 등이 있다.



### Burp Suite 실습

* burp suite 다운로드 https://portswigger.net/burp/communitydownload

* 프록시를 사용하면 클라이언트의 요청 정보, 서버의 응답정보를 상세히 확인이 가능하다. 

  





### HTTP REQUEST

* 클라이언트는 서버에게 메소드라는 수단으로 요청을 한다. 메소드 중 가장 중요한 건 GET 과 POST 이다.

* http://sam60.xo.st/
* https 사이트 접속을 위한 설정
* http://127.0.0.1:8080

Request Line
Headers
공백
Body

Request Line
 -> 메소드 공백 URL 공백 HTTP버전



### GET

* 지정된 URL 정보를 요청한다.
* 전달해야하는 파라미터를 URL에 넣어 전달한다.

```
GET /sam/entry.cgi HTTP/1.1
Host: sam60.ivyro.net
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.135 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://sam60.ivyro.net/sam/index.cgi
Accept-Encoding: gzip, deflate
Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
Connection: close
```



### POST

* 지정된 URL 정보를 요청한다.
* 메세지 본문 (http body)을 통해 파라미터를 전달한다.

```
POST /sam/status.cgi HTTP/1.1
Host: sam60.ivyro.net
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 29
Origin: http://sam60.ivyro.net
Connection: close
Referer: http://sam60.ivyro.net/sam/index.cgi
Upgrade-Insecure-Requests: 1

mode=STATUS&id=1111&pass=1111
```





### 불필요한 메소드를 활용한 보안 위협 실습

options 대상 서버에 활성화 되어있는 메소드. 메소드를 활용한 정보수집.

netcat을 설치한다. 설치 후 

nc 124.53.136.207 777
OPTIONS / HTTP/1.1

host:124.53.136.207
--------------------------------------
### PUT 메소드를 이용한 업로드

nc 124.53.136.207 777
PUT /chapter5/test.html HTTP/1.1
host:124.53.136.207
Content-Length: 20
theflower go go go!

124.53.136.207:777/chapter5/test.html 접속하기





### HTTP Response

Status line
headers
공백
Body

Status line

HTTP버전 공백 상태코드 공백 상태문구정의

상태코드(웹 서버의 응답코드)

중요상태코드
200 ok 클라이언트의요청이 성공함
400 : Bad Request : 클라이언트가 서버에 잘못된 요청을 했을때
401 : Unauthorized : 클라이언트가 HTTP 인증을 받아야 함
403 : Forbidden : 서버가 클라이언트에 권한을 주지 않은 상태
404 : Not Found : 지정한 웹문서가 존재하지 않음
500 : Internal Server Error : 
       서버의 일부가 멈췄거나 설정에서 오류 발생
503 : Service Unavailable 
        최대 세션수를 초과해서 웹 서비스가 불가능한 경우





### 정보노출이 되면 보안 취약점
-> 따라서 상태코드 , 에러 정보등 어떤 정보도 노출되게 하면 안된다
-> 똑같은 페이지가 뜨게 만들어야한다.

Username: natas0
Password: natas0

URL: http://natas0.natas.labs.overthewire.org
-----------------------------------------------------------


https://www.google.com/search?q=%EA%B9%80%ED%98%B8%EC%A7%84&oq=%EA%B9%80%ED%98%B8%EC%A7%84&aqs=chrome..69i57j0l7.3422j0j4&sourceid=chrome&ie=UTF-8
-------------------------------------------------------
http://124.53.136.207:777





### 세션 하이재킹 공격 또는 쿠키 재생공격

다른 사람이 로그인하고 있는 동안 세션 id를 훔치는데 성공하면 해당 세션 id를 사용해서 로그인 세션을 가로챌 수 있다.

http://124.53.136.207:777 으로 실습한다. 

cookie toolbar를 사용할 것이다. 설치해준다. 쿠키값을 조작하도록 돕는 툴바.

희생자 : 크롬
공격자 : 인터넷익스플로러

크롬에서 로그인 후 버프를 잡은 뒤 쿠키 값을 탈취한다.

익스플로러로 접속 시도 후 쿠키값을 탈취한 쿠키값으로 변경하고 forward 한다.

그러면 로그인 세션을 가져올 수 있게 된다. 

쿠키툴바를 사용해 쿠키값을 계속 유지시킨다. Edit Cookie

세션 ID만 훔치면 공격이 가능하다.
