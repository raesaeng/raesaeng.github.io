---
layout: post
title: "Docker Ubuntu 설치하기 및 기본 문법"
author: "한결"
categories: blog
---

## 1. Ubuntu에 Docker 설치하기



Docker 홈페이지에서 긁어온 설치 명령어. 이대로만 설치하면 크게 문제가 없이 잘 실행되는 것 같다.

* 패키지 설치

```
$ sudo apt-get update
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

* gpg키 등록

```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

* key 확인

```
$ sudo apt-key fingerprint 0EBFCD88

pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
```

* 저장소 다운

```
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

* 업데이트 후 도커 실행파일 다운

```
$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

* 도커 시스템 시작

```
$ sudo systemctl start docker
$ sudo systemctl enable docker
$ sudo systemctl status docker
```

* 권한 부여

```
$ sudo usermod -aG docker $USER
```

* hello-world 컨테이너 생성하며 docker 시작하기

```
$ sudo docker run hello-world
```



## 2. Docker hub 도커 허브
도커허브는 도커의 가장 기본적인 이미지 저장소이다. 도커허브에서 이미지를 공유할 수 있다. 오피셜 이미지 외에도 사용자들이 개발한 이미지도 공유되어 다운받을 수 있다. 도커허브에 저장된 이미지의 이름은 기본적으로 "저장소이름:태그"로 존재하며 오피셜 이미지는 "저장소 이름"으로 존재한다. 그외의 이미지들은 "허브ID/저장소이름"으로 되어있다.  
  
  
* 도커 이미지 검색
  이미지를 검색한 후 찾아서 다운로드가 가능하다. 서치 명령어로는 이미지 저장소에 저장된 태그까지는 알 수 없다.  
  NAME : 이미지 저장소 이름  
  DESCRIPTION : 이미지에 대한 설명  
  STARS : 이미지에서 대한 평가점수  
  OFFICIAL : 공식 이미지 여부  
  AUTOMATED : 자동화 빌드 여부  

  ```
  $ docker search centos
  ```

* 도커 이미지 다운로드
  태그를 생략하면 자동으로 latest가 부여되어 다운로드가 진행된다. 이미지마다 레이어 수가 다르며 중복된 레이어는 다운받지 않는다.  
  TAG : 이미지의 태그로 버전을 나타내거나 특성을 나타냄  
  DIGEST : 해시값  

  ```
  $ docker pull centos
  $ docker pull httpd:latest
  $ docker pull mysql:5.7
  ```

* 도커 이미지 리스트 
  현재 호스트에 저장되어 있는 이미지 목록을 나타낸다.  
  저장소를 지정하여 해당 저장소의 이미지만 나열할 수 있다.

  ```
  $ docker images 
  $ docker image ls
  
  $ docker images centos
  ```

* 도커 이미지 삭제

  컨테이너에서 사용중인 이미지는 삭제되지 않는다.  

  ```
  $ docker image rm hello-world:latest
  $ docker rmi hello-world:latest
  ```

  -f 옵션 force 으로 강제 삭제가 가능하다. 권장하지는 않는 삭제법.  

  ```
  $ docker rmi -f hello-world:latest
  ```
  
* 도커 정보확인
  docker inspect 명령어로 도커 이미지나 오브젝트의 정보를 자세히 확인할 수 있다. json 포맷으로 보여준다.  
  Config : cmd는 이미지를 컨테이너로 생성할 때 컨테이너가 실행하는 애플리케이션을 말한다.  
  Volumes : 도커 볼륨.  
  WorkingDir : 컨테이너에 접근했을 때의 디렉토리  
  Entrypoint : Cmd와 마찬가지로 컨테이너가 실행할 애플리케이션을 말한다. 
  * Entrypoint와 Cmd 관계는 명령과 인자의 관계와 비슷하다. Entrypoint가 지정되어 있지 않으면 Cmd가 명령으로 동작하며, Entrypoint와 Cmd가 함께 있으면 Entrypoint가 명령이고 Cmd가 인자처럼 동작한다.  
  RootFS : 이미지의 레이어를 나타냄. 계층화.  
  
  ```
  $ docker inspect centos:latest
  ```

* 도커 저장, 불러오기
  docker save 명령은 호스트에 저장된 이미지를 아카이브 파일로 복사한다. -o (output) 옵션을 사용하여 생성되는 파일의 경로를 지정해준다.  
  docker load 명령은 아카이브 파일을 불러오는 명령이다. -i (input) 옵션을 사용하여 지정된 파일을 불러온다.   

  ```
  $ docker save -o img.tar centos:latest httpd:latest
  $ docker load -i img.tar
  ```
  img.tar으로 저장한 centos와 httpd 이미지를 불러왔다.  


## 3. 컨테이너

* 컨테이너 목록 확인하기

  ps 는 실행중인 컨테이너 목록을 출력하고

  ps -a 옵션을 사용하면 존재하는 모든 컨테이너 목록을 출력한다.  
  
  CONTAINER ID : 컨테이너 고유 ID
  IMAGE : 컨테이너가 사용하는 이미지
  COMMAND : 컨테이너에서 실행중인 애플리케이션
  CREATED : 컨테어너가 생성된 날짜
  STATUS : 컨테이너의 상태
  PORTS : 컨테이너에서 사용 중인 포트
  NAMES : 컨테이너의 이름

  ```
  $ docker ps
  $ docker container ls
  $ docker ps -a
  ```

* 컨테이너 생성하기

  create 명령어를 사용해 생성한다. 

  --name 으로 이름을 지정. 마지막엔 이미지명:버전.

  -i 연결되어 있지 않아도 표준 입력을 유지함

  -t 가상 터미널 지정

  ```
  $ docker create --name os1 centos:latest
  $ docker create -it --name os2 centos:lstest
  ```

* 컨테이너 시작하기

  -i 컨테이너의 표준입력에 연결

  -a 컨테이너의 표준출력/에러에 연결

  inspect 명령어는 컨테이너나 이미지의 세부정보를 json 형태로 출력한다. inspect 명령어로 ip 주소를 확인한 후 curl 명령어로 웹이 잘 동작되고 있는지 확인한다.

  ```
  $ docker start web1
  $ docker inspect web1
  $ curl 172.17.0.2
  ```

  os로 접속할때는 쉘에 접속해야 하므로 -ai 옵션을 같이 써줘야 접근이 가능하다.

  옵션을 사용하지 않으면 그냥 시작만 됨.

  쉘을 종료하지 않고 나가려면 exit가 아닌 ctrl+q+p 를 눌러야 계속 컨테이너가 up 상태로 유지된다.

  ```
  $ docker start -ai os1
  [root@123456789 /]# exit or ctrl+q+p
  ```

* 컨테이너 삭제하기

  container prune은 종료된 모든 컨테이너를 삭제한다.

  ```
  $ docker rm os1
  $ docker rm -f os1
  $ docker container prune
  ```

* 컨테이너 상태 확인

  ```
  $ docker stats
  ```

* 컨테이너 중지

  ```
  $ docker stop os1
  ```

* 컨테이너 접근

  컨테이너 표준 입력, 표준 출력/에러를 포함하는 컨테이너에 연결함

  ```
  $ docker attach
  ```

* 컨테이너 명령 실행

  실행중인 컨테이너에 간단하게 접근하여 명령을 실행시킬 수 있다.

  ```
  $ docker exec
  $ docker exec os4 cat /tmp/volume/index.html
  ```

* 컨테이너 파일 복사 

  호스트의 passwd를 os5 /tmp로 복사

  ```
  $ docker cp /etc/passwd os5:/tmp
  $ docker exec os5 ls -l /tmp/passwd
  ```

  컨테이너 os5의 /tmp/passwd 파일을 /home/user/ 으로 복사

  ```
  $ docker cp os5:/tmp/passwd /home/user
  ```

  

