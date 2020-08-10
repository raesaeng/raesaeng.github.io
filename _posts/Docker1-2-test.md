## Docker 기본 문법 문제

﻿

1. docker hub 가입

https://www.throwawaymail.com/사이트에서 임시 이메일을 발급받아, http://hub.docker.com/ 사이트 가입(향후 실습시 사용)



2. docker 사용환경 구축

- 연습을 위해 현재 사용하고 있는 노트북에 직접 docker 설치해 볼 것

- https://docs.docker.com 및 교재를 참고하여 docker 서비스 설치

  

  => 설치 과정에서 발생하는 문제 등 직접 해결해 볼 것

﻿﻿

3. docker 동작 확인

docker 명령을 사용하여 최신 버전의 centos 이미지를 다운로드 하고 컨테이너 실행시 표준입/출력으로 연결하여 컨테이너의 OS 정보 확인 (/etc/os-release)

```
ubuntu@ubuntu1804:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              a6ea92c35c43        4 days ago          166MB
nginx               latest              08393e824c32        5 days ago          132MB
centos              latest              831691599b88        7 weeks ago         215MB
hello-world         latest              bf756fb1ae65        7 months ago        13.3kB
ubuntu@ubuntu1804:~$ sudo docker run -it --name os1 centos:latest
[root@638b42f4ac77 /]# cat /etc/os-release  
NAME="CentOS Linux"
VERSION="8 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="8"
PLATFORM_ID="platform:el8"
PRETTY_NAME="CentOS Linux 8 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:8"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-8"
CENTOS_MANTISBT_PROJECT_VERSION="8"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="8"

[root@638b42f4ac77 /]# exit
exit

```



4. docker 컨테이너 작성

- ubuntu 이미지를 docker hub 에서 다운로드하여 사용

- 우분투에서 패키지 설치를 위해 패키지 repository를 업데이트 하고 nginx 웹서버를 설치하시오.

- index.html 파일을 작성하고, 작성한 파일을 /usr/share/nginx/html 경로에 복사하시오

- index.html 파일의 내용을 임의로 작성하시오

- nginx를 구동하시오

- 구동중인 상태에서 nginx가 정상적으로 컨테이너에서 웹 서비스를 제공하고 있는지 확인하시오.

  => 현재 사용중인 노트북 os ubuntu에 docker을 설치한 뒤 docker hub에서 ubuntu 이미지를 다운받는다. 다운받은 ubuntu 이미지로 컨테이너를 생성 후 접속하여 우분투 저장소(apt-get) 업데이트 한 뒤 nginx 웹서버를 설치한다. 편집기를 이용해 index.html 파일을 생성한 뒤 /usr/share/nginx/html/nginx 로 복사한다. 이때 생성하는 index.html 파일의 내용은 임의로 작성해도 상관없다. 시스템 명령어를 사용해 nginx을 시작시킨다. ip 주소로 접속해 웹서버가 구동되는지 확인한다. 

  ```
  ubuntu@ubuntu1804:~$ sudo docker pull ubuntu
  Using default tag: latest
  latest: Pulling from library/ubuntu
  3ff22d22a855: Pull complete 
  e7cb79d19722: Pull complete 
  323d0d660b6a: Pull complete 
  b7f616834fd0: Pull complete 
  Digest: sha256:5d1d5407f353843ecf8b16524bc5565aa332e9e6a1297c73a92d3e754b8a636d
  Status: Downloaded newer image for ubuntu:latest
  docker.io/library/ubuntu:latest
  
  ubuntu@ubuntu1804:~$ sudo docker run -it --name server -p 80:80 ubuntu:latest
  root@2893200acc16:/# apt-get update
  root@2893200acc16:/# vim index.html
  root@2893200acc16:/# cp index.html /var/www/html/index.html              
  
  root@2893200acc16:/# service nginx start
   * Starting nginx nginx                                                                                                                 [ OK ] 
  root@2893200acc16:/# service nginx status
   * nginx is running
   
  root@2893200acc16:/# ip a
  29: eth0@if30: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
      link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
      inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
         valid_lft forever preferred_lft forever
         
  root@2893200acc16:/# curl 172.17.0.2
  hello world
  ubuntu
  computing
  
  ```

  ```
  root@2893200acc16:/# curl 172.17.0.2
  hello world
  ubuntu
  computing
  ```

![스크린샷 2020-08-11 오전 1.21.49](/Users/gyeol/Desktop/스크린샷 2020-08-11 오전 1.21.49.png)





5. 웹 컨텐츠 공유

- vol2 볼륨을 새롭게 생성하시오

- httpd:latest 컨테이너를 사용하여 vol2 볼륨이 웹 서비스의 컨텐츠 위치로 마운트되도록 생성하시오.

- vol2 볼륨을 사용하는 centos:latest 이미지를 사용하는 컨테이너를 생성하고, 이 컨테이너 내에서 웹 컨텐츠를 수정하시오 (index.html)

- 수정한 웹 컨텐츠가 정상적으로 제공되는지 확인하시오.

  => 볼륨 생성 후 볼륨을 httpd 컨테이너에 마운트 시킨다. vol2+httpd

  os 이미지로 컨테이너를 생성하며 볼륨을 지정해주면 만들어진 os 컨테이너에 연결된다. centos+(vol2+httpd)

  centos 컨테이너 내에서 컨텐츠를 수정한다.

  

```
ubuntu@ubuntu1804:~$ sudo docker volume create vol2
vol2

ubuntu@ubuntu1804:~$ sudo docker inspect vol2
[
    {
        "CreatedAt": "2020-08-10T10:28:01-04:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/vol2/_data",
        "Name": "vol2",
        "Options": null,
        "Scope": "local"
    }
]

ubuntu@ubuntu1804:~$ sudo docker run -d --name web -v vol2:/usr/local/apache2/htdocs:ro httpd:latest
3c8819c1cecab01910e90f0c6137b61f03916b173e2a33e8b8731c853c5744bd

ubuntu@ubuntu1804:~$ sudo docker run -itd --name centos -v vol2:/tmp/volume centos:latest
1f03f6a5f401df5d3f5448229af3dd3f33970d35099602dd50919af18a21c8fe

ubuntu@ubuntu1804:~$ sudo docker exec centos cat /tmp/volume/index.html
<html><body><h1>It works!</h1></body></html>

ubuntu@ubuntu1804:~$ sudo docker attach centos
[root@1f03f6a5f401 /]# vi /tmp/volume/index.html
[root@1f03f6a5f401 /]# read escape sequence

ubuntu@ubuntu1804:~$ docker inspect web
[
    {
        "Id": .................
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
    }
]

ubuntu@ubuntu1804:~$ curl 172.17.0.2
<html><body><h1>AAAAAAAAAAAA</h1></body></html>

```

﻿![스크린샷 2020-08-11 오전 1.31.11](/Users/gyeol/Desktop/스크린샷 2020-08-11 오전 1.31.11.png)