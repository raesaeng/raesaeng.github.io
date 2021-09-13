---
layout: post
title: "Docker Network 설정하기"
author: "한결"
categories: blog
---

## Docker Network



```
[user@control ~]$ sudo docker network ls
[sudo] password for user: 
NETWORK ID          NAME                DRIVER              SCOPE
ec1c5c12d4d9        bridge              bridge              local
b6cb8715d8a5        host                host                local
02cc99bae340        none                null                local
[user@control ~]$ docker run -it centos:latest 
[root@72b930a2e3f2 /]# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=55 time=37.1 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=55 time=38.1 ms
^C
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 2 received, 33.3333% packet loss, time 6ms
rtt min/avg/max/mdev = 37.062/37.557/38.052/0.495 ms
[root@72b930a2e3f2 /]# exit
exit


[user@control ~]$ ip a s
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:e8:0f:58 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 414sec preferred_lft 414sec
    inet6 fe80::66fc:7fc9:3b3b:d6d8/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:cf:ca:a1 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.100/24 brd 192.168.56.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::b70d:d7b5:86ce:c257/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
4: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:74:67:e8 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
5: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN group default qlen 1000
    link/ether 52:54:00:74:67:e8 brd ff:ff:ff:ff:ff:ff
6: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ee:aa:84:6d brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:eeff:feaa:846d/64 scope link 
       valid_lft forever preferred_lft forever
10: veth7db3bee@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether d6:82:63:a4:ad:97 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::d482:63ff:fea4:ad97/64 scope link 
       valid_lft forever preferred_lft forever
11: br-8ea2a715dce7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:24:6d:27:e5 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global br-8ea2a715dce7
       valid_lft forever preferred_lft forever
    inet6 fe80::42:24ff:fe6d:27e5/64 scope link 
       valid_lft forever preferred_lft forever
13: vethf00d3d8@if12: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-8ea2a715dce7 state UP group default 
    link/ether fe:d0:a5:44:cc:61 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::fcd0:a5ff:fe44:cc61/64 scope link 
       valid_lft forever preferred_lft forever





[user@control ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
72b930a2e3f2        centos:latest       "/bin/bash"              3 minutes ago       Exited (0) 2 minutes ago                       awesome_gates
5c205c085889        centos:latest       "/bin/bash"              18 hours ago        Exited (0) 17 hours ago                        os7
c9326476cf7a        centos:latest       "/bin/bash"              18 hours ago        Exited (0) 17 hours ago                        os6
954b255baf7a        mysql:5.7           "docker-entrypoint.s…"   19 hours ago        Exited (0) 17 hours ago                        db2
304473e833e9        mysql:5.7           "docker-entrypoint.s…"   19 hours ago        Exited (1) 19 hours ago                        db1
f56ecb95c2e0        centos:latest       "/bin/bash"              19 hours ago        Exited (0) 17 hours ago                        os5
7d8dec544a81        centos:latest       "/bin/bash"              19 hours ago        Exited (0) 19 hours ago                        os4
f07bc745561d        centos:latest       "/bin/bash"              19 hours ago        Exited (0) 19 hours ago                        os3
a5eab3338ea5        httpd:latest        "httpd-foreground"       20 hours ago        Exited (0) 19 hours ago                        web2
c71d40769b82        centos:latest       "/bin/bash"              20 hours ago        Exited (0) 17 hours ago                        os2
1dc2c54f2525        httpd:latest        "httpd-foreground"       20 hours ago        Exited (0) 17 hours ago            web1

컨테이너가 실행중인게 없어서 사용중인 IP, NETWORK가 존재하지 않음
스타트해준다.

[user@control ~]$ docker start os7
os7

[user@control ~]$ docker inspect bridge
[
    {
        "Name": "bridge",
        "Id": "ec1c5c12d4d9f601aba1658518ffbfdcc5232b881e0e0d86dab9ab8dcb22d758",
        "Created": "2020-08-11T10:11:40.767717054+09:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "5c205c0858895c3030cb452de2992860f750fe1ee07f33e27a3882aabc2568d3": {
                "Name": "os7",
                "EndpointID": "78ff754119428e48c858303ced4ee3295c5013848cdd50012c34c8294c444acc",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]

[user@control ~]$ sudo docker network create --subnet 172.18.0.0/16 --gateway 172.18.0.1 net1
8ea2a715dce7962c972e61ec26a4578cd26ff0ab98aaaa3871f86718cf600789

[user@control ~]$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
ec1c5c12d4d9        bridge              bridge              local
b6cb8715d8a5        host                host                local
8ea2a715dce7        net1                bridge              local
02cc99bae340        none                null                local


[user@control ~]$ docker run -dit --name os8 --network net1 centos:latest 
68d587a30d4d253ee61b6234b90d80769cb90a7a03ca866efe40e2c55b072c77

[user@control ~]$ docker exec os8 ip a s
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
12: eth0@if13: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:12:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.18.0.2/16 brd 172.18.255.255 scope global eth0
       valid_lft forever preferred_lft forever
      

```







* 호스트네트워크

  컨테이너의 네트워크 격리를 해제하여 호스트의 네트워크 정보를 공유한다.

  컨테이너는 호스트 입장에서 하나의 프로세스에 해당하기 때문에 가상머신과 다르게 네트워크 공유가 가능하다.

  호스트의 포트와 컨테이너의 포트가 충돌하지 않도록 한다.

  충돌시 호스트의 포트가 표출된다.

```
[user@control ~]$ docker run -d --name web4 --network host httpd:latest
75a5714c23b3cb0bf7c9ad27d48ef28ea475b6d3b133031078446875d8d5dd30
[user@control ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:e8:0f:58 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 576sec preferred_lft 576sec
    inet6 fe80::66fc:7fc9:3b3b:d6d8/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:cf:ca:a1 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.100/24 brd 192.168.56.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::b70d:d7b5:86ce:c257/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
4: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:74:67:e8 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
5: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN group default qlen 1000
    link/ether 52:54:00:74:67:e8 brd ff:ff:ff:ff:ff:ff
6: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ee:aa:84:6d brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:eeff:feaa:846d/64 scope link 
       valid_lft forever preferred_lft forever
10: veth7db3bee@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether d6:82:63:a4:ad:97 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::d482:63ff:fea4:ad97/64 scope link 
       valid_lft forever preferred_lft forever
11: br-8ea2a715dce7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:24:6d:27:e5 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global br-8ea2a715dce7
       valid_lft forever preferred_lft forever
    inet6 fe80::42:24ff:fe6d:27e5/64 scope link 
       valid_lft forever preferred_lft forever
13: vethf00d3d8@if12: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-8ea2a715dce7 state UP group default 
    link/ether fe:d0:a5:44:cc:61 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::fcd0:a5ff:fe44:cc61/64 scope link 
       valid_lft forever preferred_lft forever
[user@control ~]$ curl localhost
<html><body><h1>It works!</h1></body></html>

[user@control ~]$ ss -tan
State      Recv-Q Send-Q                           Local Address:Port                                          Peer Address:Port              
LISTEN     0      5                                192.168.122.1:53                                                       *:*                  
LISTEN     0      128                                          *:22                                                       *:*                  
LISTEN     0      128                                  127.0.0.1:631                                                      *:*                  
LISTEN     0      100                                  127.0.0.1:25                                                       *:*                  
LISTEN     0      128                                          *:111                                                      *:*                  
ESTAB      0      0                               192.168.56.100:22                                            192.168.56.1:43012              
LISTEN     0      128                                       [::]:22                                                    [::]:*                  
LISTEN     0      128                                      [::1]:631                                                   [::]:*                  
LISTEN     0      100                                      [::1]:25                                                    [::]:*                  
LISTEN     0      128                                       [::]:111                                                   [::]:*                  
LISTEN     0      128                                       [::]:80                                                    [::]:*                  
TIME-WAIT  0      0                                        [::1]:47046                                                [::1]:80                 
[user@control ~]$ systemctl status httpd
Unit httpd.service could not be found.
[user@control ~]$ docker inspect web4 | grep -i ipaddress
            "SecondaryIPAddresses": null,
            "IPAddress": "",
                    "IPAddress": "",
[user@control ~]$ docker run -it --network host --name os9 --rm centos:latest 
[root@control /]# ip a s 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:e8:0f:58 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
       valid_lft 385sec preferred_lft 385sec
    inet6 fe80::66fc:7fc9:3b3b:d6d8/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:cf:ca:a1 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.100/24 brd 192.168.56.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::b70d:d7b5:86ce:c257/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
4: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:74:67:e8 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
5: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN group default qlen 1000
    link/ether 52:54:00:74:67:e8 brd ff:ff:ff:ff:ff:ff
6: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ee:aa:84:6d brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:eeff:feaa:846d/64 scope link 
       valid_lft forever preferred_lft forever
10: veth7db3bee@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether d6:82:63:a4:ad:97 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::d482:63ff:fea4:ad97/64 scope link 
       valid_lft forever preferred_lft forever
11: br-8ea2a715dce7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:24:6d:27:e5 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global br-8ea2a715dce7
       valid_lft forever preferred_lft forever
    inet6 fe80::42:24ff:fe6d:27e5/64 scope link 
       valid_lft forever preferred_lft forever
13: vethf00d3d8@if12: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-8ea2a715dce7 state UP group default 
    link/ether fe:d0:a5:44:cc:61 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::fcd0:a5ff:fe44:cc61/64 scope link 
       valid_lft forever preferred_lft forever
호스트의 아이피를 공유하고 있기 때문에 그대로 가져옴
```



* 

```
[user@control ~]$ docker stop web4
web4
[user@control ~]$ sudo yum -y install httpd
Complete!
[user@control ~]$ sudo systemctl start httpd
[user@control ~]$ systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: active (running) since 화 2020-08-11 11:00:04 KST; 19s ago
     Docs: man:httpd(8)
           man:apachectl(8)
 Main PID: 10947 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
    Tasks: 6
   Memory: 2.9M
   CGroup: /system.slice/httpd.service
           ├─10947 /usr/sbin/httpd -DFOREGROUND
           ├─10952 /usr/sbin/httpd -DFOREGROUND
           ├─10953 /usr/sbin/httpd -DFOREGROUND
           ├─10954 /usr/sbin/httpd -DFOREGROUND
           ├─10955 /usr/sbin/httpd -DFOREGROUND
           └─10956 /usr/sbin/httpd -DFOREGROUND

 8월 11 11:00:04 control.nobreak.local systemd[1]: Starting The Apache HTTP Server...
 8월 11 11:00:04 control.nobreak.local httpd[10947]: AH00558: httpd: Could not reliably determine the server's fully qualified domain ...essage
 8월 11 11:00:04 control.nobreak.local systemd[1]: Started The Apache HTTP Server.
Hint: Some lines were ellipsized, use -l to show in full.

[user@control ~]$ curl localhost
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd"><html><head>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
		<title>Apache HTTP Server Test Page powered by CentOS</title>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
......
            <p>Additionally to being a popular choice for web hosting, CentOS also provides a rich platform for open source communities to build upon. For more information
               please visit the <a href="http://www.centos.org/">CentOS website</a>.</p>
          </div>
        </div>
		  </div>
    </div>
  </div>
</body></html>


[user@control ~]$ docker run -d --name webserver2 --network host httpd
fa606eca6ccc3a53ec3b0eeb864bd64348f7905ff1c8d41e3e82667f6016a6f7
[user@control ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                          PORTS               NAMES
fa606eca6ccc        httpd               "httpd-foreground"       8 seconds ago       Exited (1) 7 seconds ago                            webserver2
75a5714c23b3        httpd:latest        "httpd-foreground"       6 minutes ago       Exited (0) About a minute ago                       web4
68d587a30d4d        centos:latest       "/bin/bash"              26 minutes ago      Up 26 minutes                                       os8
72b930a2e3f2        centos:latest       "/bin/bash"              35 minutes ago      Exited (0) 34 minutes ago                           awesome_gates
5c205c085889        centos:latest       "/bin/bash"              19 hours ago        Up 31 minutes                                       os7
c9326476cf7a        centos:latest       "/bin/bash"              19 hours ago        Exited (0) 18 hours ago                             os6
954b255baf7a        mysql:5.7           "docker-entrypoint.s…"   19 hours ago        Exited (0) 18 hours ago                             db2
304473e833e9        mysql:5.7           "docker-entrypoint.s…"   19 hours ago        Exited (1) 19 hours ago                             db1
f56ecb95c2e0        centos:latest       "/bin/bash"              20 hours ago        Exited (0) 18 hours ago                             os5
7d8dec544a81        centos:latest       "/bin/bash"              20 hours ago        Exited (0) 20 hours ago                             os4
f07bc745561d        centos:latest       "/bin/bash"              20 hours ago        Exited (0) 20 hours ago                             os3
a5eab3338ea5        httpd:latest        "httpd-foreground"       20 hours ago        Exited (0) 19 hours ago                             web2
c71d40769b82        centos:latest       "/bin/bash"              20 hours ago        Exited (0) 18 hours ago                             os2
1dc2c54f2525        httpd:latest        "httpd-foreground"       21 hours ago        Exited (0) 18 hours ago                             web1
[user@control ~]$ curl localhost
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd"><html><head>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
		<title>Apache HTTP Server Test Page powered by CentOS</title>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
......
            <p>Additionally to being a popular choice for web hosting, CentOS also provides a rich platform for open source communities to build upon. For more information
               please visit the <a href="http://www.centos.org/">CentOS website</a>.</p>
          </div>
        </div>
		  </div>
    </div>
  </div>
</body></html>
80번 포트를 점유하고 있지만 호스트가 80번 포트를 사용하기를 요구할 시 호스트의 httpd가 표출됨
```







* NIC의 promisc 모드 

```
[user@control ~]$ ip a show enp0s8
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:cf:ca:a1 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.100/24 brd 192.168.56.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::b70d:d7b5:86ce:c257/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
[user@control ~]$ sudo ip link set enp0s8 promisc on
[sudo] password for user: 
[user@control ~]$ ip a show enp0s8
3: enp0s8: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:cf:ca:a1 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.100/24 brd 192.168.56.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::b70d:d7b5:86ce:c257/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever

```

  promiscuous mode



```
[user@control ~]$ docker network create -d macvlan --subnet 192.168.56.0/24 --gateway 192.168.56.1 --ip-range=192.168.56.128/25 -o parent=enp0s8 macvlan1
018602e91be69d686dcbe32090d28f86543c84465c20ce7e4a621af61611b74a
[user@control ~]$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
ec1c5c12d4d9        bridge              bridge              local
b6cb8715d8a5        host                host                local
018602e91be6        macvlan1            macvlan             local
8ea2a715dce7        net1                bridge              local
02cc99bae340        none                null                local
[user@control ~]$ docker run -it --network macvlan1 --rm centos:latest 
[root@49b0e7186f70 /]# ip a s
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
14: eth0@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default 
    link/ether 02:42:c0:a8:38:80 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.168.56.128/24 brd 192.168.56.255 scope global eth0
       valid_lft forever preferred_lft forever
[root@49b0e7186f70 /]# exit
exit


링크를 연결하려면 연결하려는 컨테이너가 구동중이어야 한다. start 해준다. 
[user@control ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
fa606eca6ccc        httpd               "httpd-foreground"       25 minutes ago      Exited (1) 25 minutes ago                       webserver2
75a5714c23b3        httpd:latest        "httpd-foreground"       32 minutes ago      Exited (0) 27 minutes ago                       web4
68d587a30d4d        centos:latest       "/bin/bash"              51 minutes ago      Up 51 minutes                                   os8
72b930a2e3f2        centos:latest       "/bin/bash"              About an hour ago   Exited (0) 59 minutes ago                       awesome_gates
5c205c085889        centos:latest       "/bin/bash"              19 hours ago        Up 57 minutes                                   os7
c9326476cf7a        centos:latest       "/bin/bash"              19 hours ago        Exited (0) 18 hours ago                         os6
954b255baf7a        mysql:5.7           "docker-entrypoint.s…"   19 hours ago        Exited (0) 18 hours ago                         db2
304473e833e9        mysql:5.7           "docker-entrypoint.s…"   20 hours ago        Exited (1) 20 hours ago                         db1
f56ecb95c2e0        centos:latest       "/bin/bash"              20 hours ago        Exited (0) 18 hours ago                         os5
7d8dec544a81        centos:latest       "/bin/bash"              20 hours ago        Exited (0) 20 hours ago                         os4
f07bc745561d        centos:latest       "/bin/bash"              20 hours ago        Exited (0) 20 hours ago                         os3
a5eab3338ea5        httpd:latest        "httpd-foreground"       21 hours ago        Exited (0) 20 hours ago                         web2
c71d40769b82        centos:latest       "/bin/bash"              21 hours ago        Exited (0) 18 hours ago                         os2
1dc2c54f2525        httpd:latest        "httpd-foreground"       21 hours ago        Exited (0) 18 hours ago                         web1
[user@control ~]$ docker start web4
web4
[user@control ~]$ docker run -dit --name os10 --link web4 centos:latest
docker: Error response from daemon: conflicting options: host type networking can't be used with links. This would result in undefined behavior.
See 'docker run --help'.
[user@control ~]$ sudo docker run -dit --name os10 --link web4 centos:latest
[sudo] password for user: 
docker: Error response from daemon: conflicting options: host type networking can't be used with links. This would result in undefined behavior.
See 'docker run --help'.
[user@control ~]$ docker run -itd --name os10 --link web4 centos:latest 
docker: Error response from daemon: conflicting options: host type networking can't be used with links. This would result in undefined behavior.
See 'docker run --help'.
[user@control ~]$ docker start web2
web2
[user@control ~]$ docker run -itd --name os10 --link web2 centos:latest 
58db52fb535592826501ebaafe2e6477fbfa2de43dd8082525e1403290dab2cd
[user@control ~]$ cat /etc/host
cat: /etc/host: No such file or directory
[user@control ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
[user@control ~]$ docker exec os10 curl web2
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    45  100    45    0     0   6428      0 --:--:-- --:--:-- --:--:--  6428
<html><body><h1>It works!</h1></body></html>
[user@control ~]$ docker exec os10 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.3	web2 a5eab3338ea5
172.17.0.4	58db52fb5355
[user@control ~]$ docker run -itd --name os11 --link web2:apache centos:latest 
f80432e9669e77cc09e93a0dc31eade79e019964fcf7746548b3cef5d001bbc7
[user@control ~]$ docker exec os11 curl apache
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    45  100    45    0     0   7500      0 --:--:-- --:--:-- --:--:--  7500
<html><body><h1>It works!</h1></body></html>
[user@control ~]$ docker exec os11 /etc/hosts
OCI runtime exec failed: exec failed: container_linux.go:349: starting container process caused "exec: \"/etc/hosts\": permission denied": unknown
[user@control ~]$ docker exec os11 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.3	apache a5eab3338ea5 web2
172.17.0.5	f80432e9669e
[user@control ~]$ docker run -d --name web1 -p 8080:80 httpd:latest 
docker: Error response from daemon: Conflict. The container name "/web1" is already in use by container "1dc2c54f2525bab8d1ec6d083cebbe98507601d59cec0796fb8df325687ebe83". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
[user@control ~]$ docker start web1
web1
[user@control ~]$ docker run -d --name web1 -p 8080:80 httpd:latest 
docker: Error response from daemon: Conflict. The container name "/web1" is already in use by container "1dc2c54f2525bab8d1ec6d083cebbe98507601d59cec0796fb8df325687ebe83". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
[user@control ~]$ docker run -d web5 httpd:latest
Unable to find image 'web5:latest' locally
docker: Error response from daemon: pull access denied for web5, repository does not exist or may require 'docker login': denied: requested access to the resource is denied.
See 'docker run --help'.
[user@control ~]$ sudo docker run -d web5 httpd:latest
[sudo] password for user: 
Unable to find image 'web5:latest' locally
docker: Error response from daemon: pull access denied for web5, repository does not exist or may require 'docker login': denied: requested access to the resource is denied.
See 'docker run --help'.
[user@control ~]$ sudo docker run -d --name web5 httpd:latest
f2d166e820e04d77c3a98d7262b2f0b87f4e2a495ef23b2a35861a18167da25e
[user@control ~]$ docker run -d --name web5 -p 8080:80 httpd:latest 
docker: Error response from daemon: Conflict. The container name "/web5" is already in use by container "f2d166e820e04d77c3a98d7262b2f0b87f4e2a495ef23b2a35861a18167da25e". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
[user@control ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                         PORTS               NAMES
f2d166e820e0        httpd:latest        "httpd-foreground"       26 seconds ago      Up 25 seconds                  80/tcp              web5
f80432e9669e        centos:latest       "/bin/bash"              4 minutes ago       Up 4 minutes                                       os11
58db52fb5355        centos:latest       "/bin/bash"              6 minutes ago       Up 6 minutes                                       os10
fa606eca6ccc        httpd               "httpd-foreground"       33 minutes ago      Exited (1) 33 minutes ago                          webserver2
75a5714c23b3        httpd:latest        "httpd-foreground"       40 minutes ago      Exited (1) 7 minutes ago                           web4
68d587a30d4d        centos:latest       "/bin/bash"              About an hour ago   Up About an hour                                   os8
72b930a2e3f2        centos:latest       "/bin/bash"              About an hour ago   Exited (0) About an hour ago                       awesome_gates
5c205c085889        centos:latest       "/bin/bash"              19 hours ago        Up About an hour                                   os7
c9326476cf7a        centos:latest       "/bin/bash"              19 hours ago        Exited (0) 18 hours ago                            os6
954b255baf7a        mysql:5.7           "docker-entrypoint.s…"   20 hours ago        Exited (0) 18 hours ago                            db2
304473e833e9        mysql:5.7           "docker-entrypoint.s…"   20 hours ago        Exited (1) 20 hours ago                            db1
f56ecb95c2e0        centos:latest       "/bin/bash"              20 hours ago        Exited (0) 18 hours ago                            os5
7d8dec544a81        centos:latest       "/bin/bash"              20 hours ago        Exited (0) 20 hours ago                            os4
f07bc745561d        centos:latest       "/bin/bash"              20 hours ago        Exited (0) 20 hours ago                            os3
a5eab3338ea5        httpd:latest        "httpd-foreground"       21 hours ago        Up 6 minutes                   80/tcp              web2
c71d40769b82        centos:latest       "/bin/bash"              21 hours ago        Exited (0) 18 hours ago                            os2
1dc2c54f2525        httpd:latest        "httpd-foreground"       21 hours ago        Up About a minute              80/tcp              web1
[user@control ~]$ docker stop web5
web5
[user@control ~]$ docker run -d --name web5 -p 8080:80 httpd:latest 
docker: Error response from daemon: Conflict. The container name "/web5" is already in use by container "f2d166e820e04d77c3a98d7262b2f0b87f4e2a495ef23b2a35861a18167da25e". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
[user@control ~]$ sudo firewall-cmd --add-port=8080/tcp
success
[user@control ~]$ docker run -d --name web6 -p 8080:80 httpd:latest 
6d1e0d1f9a6f3c912e035f8d4eec88c2072738e0f9da797e30f34ecc4fba3c27
[user@control ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                          PORTS                  NAMES
6d1e0d1f9a6f        httpd:latest        "httpd-foreground"       10 seconds ago       Up 10 seconds                   0.0.0.0:8080->80/tcp   web6
f2d166e820e0        httpd:latest        "httpd-foreground"       About a minute ago   Exited (0) About a minute ago                          web5
f80432e9669e        centos:latest       "/bin/bash"              5 minutes ago        Up 5 minutes                                           os11
58db52fb5355        centos:latest       "/bin/bash"              7 minutes ago        Up 7 minutes                                           os10
fa606eca6ccc        httpd               "httpd-foreground"       35 minutes ago       Exited (1) 35 minutes ago                              webserver2
75a5714c23b3        httpd:latest        "httpd-foreground"       42 minutes ago       Exited (1) 9 minutes ago                               web4
68d587a30d4d        centos:latest       "/bin/bash"              About an hour ago    Up About an hour                                       os8
72b930a2e3f2        centos:latest       "/bin/bash"              About an hour ago    Exited (0) About an hour ago                           awesome_gates
5c205c085889        centos:latest       "/bin/bash"              19 hours ago         Up About an hour                                       os7
c9326476cf7a        centos:latest       "/bin/bash"              19 hours ago         Exited (0) 18 hours ago                                os6
954b255baf7a        mysql:5.7           "docker-entrypoint.s…"   20 hours ago         Exited (0) 18 hours ago                                db2
304473e833e9        mysql:5.7           "docker-entrypoint.s…"   20 hours ago         Exited (1) 20 hours ago                                db1
f56ecb95c2e0        centos:latest       "/bin/bash"              20 hours ago         Exited (0) 18 hours ago                                os5
7d8dec544a81        centos:latest       "/bin/bash"              20 hours ago         Exited (0) 20 hours ago                                os4
f07bc745561d        centos:latest       "/bin/bash"              20 hours ago         Exited (0) 20 hours ago                                os3
a5eab3338ea5        httpd:latest        "httpd-foreground"       21 hours ago         Up 7 minutes                    80/tcp                 web2
c71d40769b82        centos:latest       "/bin/bash"              21 hours ago         Exited (0) 18 hours ago                                os2
1dc2c54f2525        httpd:latest        "httpd-foreground"       21 hours ago         Up 2 minutes                    80/tcp                 web1
[user@control ~]$ curl locahost:8080
[user@control ~]$ curl 192.168.56.100:8080
<html><body><h1>It works!</h1></body></html>

```







## Docker 이미지제작

* Docker TAG

```
[user@control ~]$ docker tag centos:latest mycentos:latest
[user@control ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              a6ea92c35c43        5 days ago          166MB
mysql               5.7                 718a6da099d8        6 days ago          448MB
centos              latest              831691599b88        7 weeks ago         215MB
mycentos            latest              831691599b88        7 weeks ago         215MB
hello-world         latest              bf756fb1ae65        7 months ago        13.3kB
[user@control ~]$ docker rmi centos:latest
Untagged: centos:latest
[user@control ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              a6ea92c35c43        5 days ago          166MB
mysql               5.7                 718a6da099d8        6 days ago          448MB
mycentos            latest              831691599b88        7 weeks ago         215MB
hello-world         latest              bf756fb1ae65        7 months ago        13.3kB
[user@control ~]$ docker run -it --rm mycentos:latest 
[root@ae6b3814cae5 /]# exit
exit
[user@control ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              a6ea92c35c43        5 days ago          166MB
mysql               5.7                 718a6da099d8        6 days ago          448MB
mycentos            latest              831691599b88        7 weeks ago         215MB
hello-world         latest              bf756fb1ae65        7 months ago        13.3kB

```

```
[user@control ~]$ docker tag mycentos:latest nobreak/docker:centos
[user@control ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              a6ea92c35c43        5 days ago          166MB
mysql               5.7                 718a6da099d8        6 days ago          448MB
mycentos            latest              831691599b88        7 weeks ago         215MB
nobreak/docker      centos              831691599b88        7 weeks ago         215MB
hello-world         latest              bf756fb1ae65        7 months ago        13.3kB
[user@control ~]$ docker tag mycentos:latest pohibebohu/mycentos:latest
[user@control ~]$ docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
httpd                 latest              a6ea92c35c43        5 days ago          166MB
mysql                 5.7                 718a6da099d8        6 days ago          448MB
nobreak/docker        centos              831691599b88        7 weeks ago         215MB
pohibebohu/mycentos   latest              831691599b88        7 weeks ago         215MB
mycentos              latest              831691599b88        7 weeks ago         215MB
hello-world           latest              bf756fb1ae65        7 months ago        13.3kB

```



* 로그인하고 저장소에 이미지 업로드하기

  ```
  [user@control ~]$ docker login
  Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
  Username: pohibebohu
  Password: 
  WARNING! Your password will be stored unencrypted in /home/user/.docker/config.json.
  Configure a credential helper to remove this warning. See
  https://docs.docker.com/engine/reference/commandline/login/#credentials-store
  
  Login Succeeded
  [user@control ~]$ docker push pohibebohu/mycentos:latest 
  The push refers to repository [docker.io/pohibebohu/mycentos]
  eb29745b8228: Mounted from library/centos 
  latest: digest: sha256:fd84102fc72960dd1b8da0ee3b4c13e3b0c1d2a085de118bc4c97821cd986e02 size: 529
  
  ```

  ![Screenshot from 2020-08-11 12-07-02](/home/student/Pictures/Screenshot from 2020-08-11 12-07-02.png)

도커 허브 계정의 repositories에서 확인이 가능하다.

![Screenshot from 2020-08-11 12-08-34](/home/student/Pictures/Screenshot from 2020-08-11 12-08-34.png)

이후 검색이 가능하게 되면 docker search 명령어로도 검색할 수 있다.



![Screenshot from 2020-08-11 12-10-41](/home/student/Pictures/Screenshot from 2020-08-11 12-10-41.png)

```
[user@control ~]$ docker rmi pohibebohu/mycentos:latest 
Untagged: pohibebohu/mycentos:latest
Untagged: pohibebohu/mycentos@sha256:fd84102fc72960dd1b8da0ee3b4c13e3b0c1d2a085de118bc4c97821cd986e02
[user@control ~]$ docker pull pohibebohu/mycentos:latest
latest: Pulling from pohibebohu/mycentos
Digest: sha256:fd84102fc72960dd1b8da0ee3b4c13e3b0c1d2a085de118bc4c97821cd986e02
Status: Downloaded newer image for pohibebohu/mycentos:latest
docker.io/pohibebohu/mycentos:latest
```





* commit

  commit 명령어는 생성된 컨테이너 기반으로 이미지를 생성한다.

  commit 으로 이미지를 생성하면 inspect로 확인시 이미지의 레이어가 하나 더 추가된 것을 확인할 수 있다.

  ```
   276  docker ps 
    277  docker commit web6 pohibebohu/myweb:0.1
    278  docker imgaes
    279  docker images
    280  docker inspect pohibebohu/myweb:0.1 
    281  docker inspect httpd:latest 
  ```



* image export

  ```
  docker export -o export.tar web6
  docker export -o export.tar os7
  ll export.tar
  tar tvf export.tar 
  ```

  

* image import

  import 명령은 export로 추출된 아카이브 파일을 이미지화 시킨다. import한 이미지를 inspect로 살펴보면 레이어가 하나로 통합된 걸 확인할 수 있다. 

  import는 기존의 레이어를 유지하지 않고 새로운 레이어를 생성시킨다. 레이어를 재생성하며 이미지에 내재되어 있던 명령어들이 전부 사라진다. 그러므로 이 이미지를 통해 컨테이너를 실행하려면 반드시 이미지 뒤에 명령어를 지정해야 한다.

  ```
  [user@control ~]$ docker import export.tar myweb:import
  sha256:a24d0e151e300b95b8db4ef46856d91c6a4ba08d18a82c667e9bbcbd4ddcf115
  [user@control ~]$ docker inspect myweb:import
  [
      {
          "Id": "sha256:a24d0e151e300b95b8db4ef46856d91c6a4ba08d18a82c667e9bbcbd4ddcf115",
          "RepoTags": [
              "myweb:import"
          ],
          "RepoDigests": [],
          "Parent": "",
          "Comment": "Imported from -",
          "Created": "2020-08-11T03:22:33.35933762Z",
          "Container": "",
          "ContainerConfig": {
              "Hostname": "",
              "Domainname": "",
              "User": "",
              "AttachStdin": false,
              "AttachStdout": false,
              "AttachStderr": false,
              "Tty": false,
              "OpenStdin": false,
              "StdinOnce": false,
              "Env": null,
              "Cmd": null,
              "Image": "",
              "Volumes": null,
              "WorkingDir": "",
              "Entrypoint": null,
              "OnBuild": null,
              "Labels": null
          },
          "DockerVersion": "19.03.12",
          "Author": "",
          "Config": {
              "Hostname": "",
              "Domainname": "",
              "User": "",
              "AttachStdin": false,
              "AttachStdout": false,
              "AttachStderr": false,
              "Tty": false,
              "OpenStdin": false,
              "StdinOnce": false,
              "Env": null,
              "Cmd": null,
              "Image": "",
              "Volumes": null,
              "WorkingDir": "",
              "Entrypoint": null,
              "OnBuild": null,
              "Labels": null
          },
          "Architecture": "amd64",
          "Os": "linux",
          "Size": 215320150,
          "VirtualSize": 215320150,
          "GraphDriver": {
              "Data": {
                  "MergedDir": "/var/lib/docker/overlay2/be8612163bf7e93ac98dfbcc8e0344f0c10f78d46283bc275daef95501bbd3e4/merged",
                  "UpperDir": "/var/lib/docker/overlay2/be8612163bf7e93ac98dfbcc8e0344f0c10f78d46283bc275daef95501bbd3e4/diff",
                  "WorkDir": "/var/lib/docker/overlay2/be8612163bf7e93ac98dfbcc8e0344f0c10f78d46283bc275daef95501bbd3e4/work"
              },
              "Name": "overlay2"
          },
          "RootFS": {
              "Type": "layers",
              "Layers": [
                  "sha256:a5575c21f540c2399a8a9e4a2b7876d9acc807089ae36b642b9a13118ace9199"
              ]
          },
          "Metadata": {
              "LastTagTime": "2020-08-11T12:22:33.369897098+09:00"
          }
      }
  ]
  
  ```







* 도커파일

  ```
  [user@control ~]$ mkdir centos_web
  [user@control ~]$ cd centos_web/
  [user@control centos_web]$ vi Dockerfile
  [user@control centos_web]$ cat Dockerfile 
  FROM centos:latest
  MAINTAINER nobreak
  RUN yum -y install httpd
  CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
  ENV WEBPORT 80
  EXPOSE ${WEBPORT}
  EXPOSE 443
  VOLUME /var/www/html/
  COPY index.html /var/www/html/index.html
  [user@control centos_web]$ vi index.html
  [user@control centos_web]$ docker build -t pohibebohu/myweb:latest .
  Sending build context to Docker daemon  3.072kB
  Step 1/9 : FROM centos:latest
  latest: Pulling from library/centos
  3c72a8ed6814: Pull complete 
  Digest: sha256:76d24f3ba3317fa945743bb3746fbaf3a0b752f10b10376960de01da70685fbd
  Status: Downloaded newer image for centos:latest
   ---> 0d120b6ccaa8
  Step 2/9 : MAINTAINER nobreak
   ---> Running in 8e981d45f976
  Removing intermediate container 8e981d45f976
   ---> 044e7126f4ea
  Step 3/9 : RUN yum -y install httpd
   ---> Running in 66646e565a87
  CentOS-8 - AppStream                            2.3 MB/s | 5.8 MB     00:02    
  CentOS-8 - Base                                 3.7 MB/s | 2.2 MB     00:00    
  CentOS-8 - Extras                               9.2 kB/s | 7.3 kB     00:00    
  Dependencies resolved.
  ================================================================================
   Package           Arch   Version                               Repo       Size
  ================================================================================
  Installing:
   httpd             x86_64 2.4.37-21.module_el8.2.0+382+15b0afa8 AppStream 1.7 M
  Installing dependencies:
   apr               x86_64 1.6.3-9.el8                           AppStream 125 k
   apr-util          x86_64 1.6.1-6.el8                           AppStream 105 k
   brotli            x86_64 1.0.6-1.el8                           BaseOS    323 k
   centos-logos-httpd
                     noarch 80.5-2.el8                            BaseOS     24 k
   httpd-filesystem  noarch 2.4.37-21.module_el8.2.0+382+15b0afa8 AppStream  36 k
   httpd-tools       x86_64 2.4.37-21.module_el8.2.0+382+15b0afa8 AppStream 103 k
   mailcap           noarch 2.1.48-3.el8                          BaseOS     39 k
   mod_http2         x86_64 1.11.3-3.module_el8.2.0+307+4d18d695  AppStream 157 k
  Installing weak dependencies:
   apr-util-bdb      x86_64 1.6.1-6.el8                           AppStream  25 k
   apr-util-openssl  x86_64 1.6.1-6.el8                           AppStream  27 k
  Enabling module streams:
   httpd                    2.4                                                  
  
  Transaction Summary
  ================================================================================
  Install  11 Packages
  
  Total download size: 2.6 M
  Installed size: 7.5 M
  Downloading Packages:
  (1/11): apr-util-bdb-1.6.1-6.el8.x86_64.rpm     534 kB/s |  25 kB     00:00    
  (2/11): apr-util-1.6.1-6.el8.x86_64.rpm         2.1 MB/s | 105 kB     00:00    
  (3/11): apr-1.6.3-9.el8.x86_64.rpm              2.4 MB/s | 125 kB     00:00    
  (4/11): apr-util-openssl-1.6.1-6.el8.x86_64.rpm 3.8 MB/s |  27 kB     00:00    
  (5/11): httpd-filesystem-2.4.37-21.module_el8.2 2.8 MB/s |  36 kB     00:00    
  (6/11): httpd-tools-2.4.37-21.module_el8.2.0+38 5.2 MB/s | 103 kB     00:00    
  (7/11): mod_http2-1.11.3-3.module_el8.2.0+307+4 5.1 MB/s | 157 kB     00:00    
  (8/11): centos-logos-httpd-80.5-2.el8.noarch.rp 3.8 MB/s |  24 kB     00:00    
  (9/11): mailcap-2.1.48-3.el8.noarch.rpm         2.8 MB/s |  39 kB     00:00    
  (10/11): brotli-1.0.6-1.el8.x86_64.rpm          6.7 MB/s | 323 kB     00:00    
  (11/11): httpd-2.4.37-21.module_el8.2.0+382+15b 9.2 MB/s | 1.7 MB     00:00    
  --------------------------------------------------------------------------------
  Total                                           2.4 MB/s | 2.6 MB     00:01     
  CentOS-8 - AppStream                            1.6 MB/s | 1.6 kB     00:00    
  warning: /var/cache/dnf/AppStream-02e86d1c976ab532/packages/apr-1.6.3-9.el8.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 8483c65d: NOKEY
  Importing GPG key 0x8483C65D:
   Userid     : "CentOS (CentOS Official Signing Key) <security@centos.org>"
   Fingerprint: 99DB 70FA E1D7 CE22 7FB6 4882 05B5 55B3 8483 C65D
   From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
  Key imported successfully
  Running transaction check
  Transaction check succeeded.
  Running transaction test
  Transaction test succeeded.
  Running transaction
    Preparing        :                                                        1/1 
    Installing       : apr-1.6.3-9.el8.x86_64                                1/11 
    Running scriptlet: apr-1.6.3-9.el8.x86_64                                1/11 
    Installing       : apr-util-bdb-1.6.1-6.el8.x86_64                       2/11 
    Installing       : apr-util-openssl-1.6.1-6.el8.x86_64                   3/11 
    Installing       : apr-util-1.6.1-6.el8.x86_64                           4/11 
    Running scriptlet: apr-util-1.6.1-6.el8.x86_64                           4/11 
    Installing       : httpd-tools-2.4.37-21.module_el8.2.0+382+15b0afa8.    5/11 
    Installing       : mailcap-2.1.48-3.el8.noarch                           6/11 
    Installing       : centos-logos-httpd-80.5-2.el8.noarch                  7/11 
    Installing       : brotli-1.0.6-1.el8.x86_64                             8/11 
    Running scriptlet: httpd-filesystem-2.4.37-21.module_el8.2.0+382+15b0    9/11 
    Installing       : httpd-filesystem-2.4.37-21.module_el8.2.0+382+15b0    9/11 
    Installing       : mod_http2-1.11.3-3.module_el8.2.0+307+4d18d695.x86   10/11 
    Installing       : httpd-2.4.37-21.module_el8.2.0+382+15b0afa8.x86_64   11/11 
    Running scriptlet: httpd-2.4.37-21.module_el8.2.0+382+15b0afa8.x86_64   11/11 
    Verifying        : apr-1.6.3-9.el8.x86_64                                1/11 
    Verifying        : apr-util-1.6.1-6.el8.x86_64                           2/11 
    Verifying        : apr-util-bdb-1.6.1-6.el8.x86_64                       3/11 
    Verifying        : apr-util-openssl-1.6.1-6.el8.x86_64                   4/11 
    Verifying        : httpd-2.4.37-21.module_el8.2.0+382+15b0afa8.x86_64    5/11 
    Verifying        : httpd-filesystem-2.4.37-21.module_el8.2.0+382+15b0    6/11 
    Verifying        : httpd-tools-2.4.37-21.module_el8.2.0+382+15b0afa8.    7/11 
    Verifying        : mod_http2-1.11.3-3.module_el8.2.0+307+4d18d695.x86    8/11 
    Verifying        : brotli-1.0.6-1.el8.x86_64                             9/11 
    Verifying        : centos-logos-httpd-80.5-2.el8.noarch                 10/11 
    Verifying        : mailcap-2.1.48-3.el8.noarch                          11/11 
  
  Installed:
    apr-1.6.3-9.el8.x86_64                                                        
    apr-util-1.6.1-6.el8.x86_64                                                   
    apr-util-bdb-1.6.1-6.el8.x86_64                                               
    apr-util-openssl-1.6.1-6.el8.x86_64                                           
    brotli-1.0.6-1.el8.x86_64                                                     
    centos-logos-httpd-80.5-2.el8.noarch                                          
    httpd-2.4.37-21.module_el8.2.0+382+15b0afa8.x86_64                            
    httpd-filesystem-2.4.37-21.module_el8.2.0+382+15b0afa8.noarch                 
    httpd-tools-2.4.37-21.module_el8.2.0+382+15b0afa8.x86_64                      
    mailcap-2.1.48-3.el8.noarch                                                   
    mod_http2-1.11.3-3.module_el8.2.0+307+4d18d695.x86_64                         
  
  Complete!
  Removing intermediate container 66646e565a87
   ---> 7c79e3e53088
  Step 4/9 : CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
   ---> Running in 5db1bb68394d
  Removing intermediate container 5db1bb68394d
   ---> 17b943cced94
  Step 5/9 : ENV WEBPORT 80
   ---> Running in f83a8a7de958
  Removing intermediate container f83a8a7de958
   ---> 8c41afea198d
  Step 6/9 : EXPOSE ${WEBPORT}
   ---> Running in 0b78f9c4b62a
  Removing intermediate container 0b78f9c4b62a
   ---> 802f39312e8e
  Step 7/9 : EXPOSE 443
   ---> Running in a45e0ef6537e
  Removing intermediate container a45e0ef6537e
   ---> 7b0f41fa7013
  Step 8/9 : VOLUME /var/www/html/
   ---> Running in 19afbafb3961
  Removing intermediate container 19afbafb3961
   ---> 13b5941370e3
  Step 9/9 : COPY index.html /var/www/html/index.html
   ---> f0ca7927cdc2
  Successfully built f0ca7927cdc2
  Successfully tagged pohibebohu/myweb:latest
  
  [user@control centos_web]$ docker run -d --rm --name centosweb pohibebohu/myweb:latest 
  141a64aad1bb6031c94f3604afdf9faf0f774ac9e274f1b5a741790ee165b222
  [user@control centos_web]$ docker inspect centosweb 
  [
      {
          "Id": "141a64aad1bb6031c94f3604afdf9faf0f774ac9e274f1b5a741790ee165b222",
          "Created": "2020-08-11T04:55:00.827929128Z",
          "Path": "/usr/sbin/httpd",
          "Args": [
              "-D",
              "FOREGROUND"
          ],
          "State": {
              "Status": "running",
              "Running": true,
              "Paused": false,
              "Restarting": false,
              "OOMKilled": false,
              "Dead": false,
              "Pid": 20451,
              "ExitCode": 0,
              "Error": "",
              "StartedAt": "2020-08-11T04:55:01.251094144Z",
              "FinishedAt": "0001-01-01T00:00:00Z"
          },
          "Image": "sha256:f0ca7927cdc2bf4478aa595cc8b6838d47ce0f9ba1cf0d08161821b3ae4d2e54",
          "ResolvConfPath": "/var/lib/docker/containers/141a64aad1bb6031c94f3604afdf9faf0f774ac9e274f1b5a741790ee165b222/resolv.conf",
          "HostnamePath": "/var/lib/docker/containers/141a64aad1bb6031c94f3604afdf9faf0f774ac9e274f1b5a741790ee165b222/hostname",
          "HostsPath": "/var/lib/docker/containers/141a64aad1bb6031c94f3604afdf9faf0f774ac9e274f1b5a741790ee165b222/hosts",
          "LogPath": "/var/lib/docker/containers/141a64aad1bb6031c94f3604afdf9faf0f774ac9e274f1b5a741790ee165b222/141a64aad1bb6031c94f3604afdf9faf0f774ac9e274f1b5a741790ee165b222-json.log",
          "Name": "/centosweb",
          "RestartCount": 0,
          "Driver": "overlay2",
          "Platform": "linux",
          "MountLabel": "",
          "ProcessLabel": "",
          "AppArmorProfile": "",
          "ExecIDs": null,
          "HostConfig": {
              "Binds": null,
              "ContainerIDFile": "",
              "LogConfig": {
                  "Type": "json-file",
                  "Config": {}
              },
              "NetworkMode": "default",
              "PortBindings": {},
              "RestartPolicy": {
                  "Name": "no",
                  "MaximumRetryCount": 0
              },
              "AutoRemove": true,
              "VolumeDriver": "",
              "VolumesFrom": null,
              "CapAdd": null,
              "CapDrop": null,
              "Capabilities": null,
              "Dns": [],
              "DnsOptions": [],
              "DnsSearch": [],
              "ExtraHosts": null,
              "GroupAdd": null,
              "IpcMode": "private",
              "Cgroup": "",
              "Links": null,
              "OomScoreAdj": 0,
              "PidMode": "",
              "Privileged": false,
              "PublishAllPorts": false,
              "ReadonlyRootfs": false,
              "SecurityOpt": null,
              "UTSMode": "",
              "UsernsMode": "",
              "ShmSize": 67108864,
              "Runtime": "runc",
              "ConsoleSize": [
                  0,
                  0
              ],
              "Isolation": "",
              "CpuShares": 0,
              "Memory": 0,
              "NanoCpus": 0,
              "CgroupParent": "",
              "BlkioWeight": 0,
              "BlkioWeightDevice": [],
              "BlkioDeviceReadBps": null,
              "BlkioDeviceWriteBps": null,
              "BlkioDeviceReadIOps": null,
              "BlkioDeviceWriteIOps": null,
              "CpuPeriod": 0,
              "CpuQuota": 0,
              "CpuRealtimePeriod": 0,
              "CpuRealtimeRuntime": 0,
              "CpusetCpus": "",
              "CpusetMems": "",
              "Devices": [],
              "DeviceCgroupRules": null,
              "DeviceRequests": null,
              "KernelMemory": 0,
              "KernelMemoryTCP": 0,
              "MemoryReservation": 0,
              "MemorySwap": 0,
              "MemorySwappiness": null,
              "OomKillDisable": false,
              "PidsLimit": null,
              "Ulimits": null,
              "CpuCount": 0,
              "CpuPercent": 0,
              "IOMaximumIOps": 0,
              "IOMaximumBandwidth": 0,
              "MaskedPaths": [
                  "/proc/asound",
                  "/proc/acpi",
                  "/proc/kcore",
                  "/proc/keys",
                  "/proc/latency_stats",
                  "/proc/timer_list",
                  "/proc/timer_stats",
                  "/proc/sched_debug",
                  "/proc/scsi",
                  "/sys/firmware"
              ],
              "ReadonlyPaths": [
                  "/proc/bus",
                  "/proc/fs",
                  "/proc/irq",
                  "/proc/sys",
                  "/proc/sysrq-trigger"
              ]
          },
          "GraphDriver": {
              "Data": {
                  "LowerDir": "/var/lib/docker/overlay2/f15b203c9c0597eaa2be59e25a0a339d3fe8b66db686898e5ce63f119ba8bc52-init/diff:/var/lib/docker/overlay2/9d9de3c89afbd06dec78eae2d8db263a48f9ce5d72839813078d8d44057069c2/diff:/var/lib/docker/overlay2/9225334c918348fc9140041edd81248823957b83908d68038c8ffb5b54a70ce1/diff:/var/lib/docker/overlay2/d876fa72e508c83a0db33cf7eba3c02f8151c098cc40e83281418688546fe71e/diff",
                  "MergedDir": "/var/lib/docker/overlay2/f15b203c9c0597eaa2be59e25a0a339d3fe8b66db686898e5ce63f119ba8bc52/merged",
                  "UpperDir": "/var/lib/docker/overlay2/f15b203c9c0597eaa2be59e25a0a339d3fe8b66db686898e5ce63f119ba8bc52/diff",
                  "WorkDir": "/var/lib/docker/overlay2/f15b203c9c0597eaa2be59e25a0a339d3fe8b66db686898e5ce63f119ba8bc52/work"
              },
              "Name": "overlay2"
          },
          "Mounts": [
              {
                  "Type": "volume",
                  "Name": "7c07f5a37480574e7c2b423845f0c16b8fa7f82e9aff1edabae6a335f900d622",
                  "Source": "/var/lib/docker/volumes/7c07f5a37480574e7c2b423845f0c16b8fa7f82e9aff1edabae6a335f900d622/_data",
                  "Destination": "/var/www/html",
                  "Driver": "local",
                  "Mode": "",
                  "RW": true,
                  "Propagation": ""
              }
          ],
          "Config": {
              "Hostname": "141a64aad1bb",
              "Domainname": "",
              "User": "",
              "AttachStdin": false,
              "AttachStdout": false,
              "AttachStderr": false,
              "ExposedPorts": {
                  "443/tcp": {},
                  "80/tcp": {}
              },
              "Tty": false,
              "OpenStdin": false,
              "StdinOnce": false,
              "Env": [
                  "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                  "WEBPORT=80"
              ],
              "Cmd": [
                  "/usr/sbin/httpd",
                  "-D",
                  "FOREGROUND"
              ],
              "Image": "pohibebohu/myweb:latest",
              "Volumes": {
                  "/var/www/html/": {}
              },
              "WorkingDir": "",
              "Entrypoint": null,
              "OnBuild": null,
              "Labels": {
                  "org.label-schema.build-date": "20200809",
                  "org.label-schema.license": "GPLv2",
                  "org.label-schema.name": "CentOS Base Image",
                  "org.label-schema.schema-version": "1.0",
                  "org.label-schema.vendor": "CentOS"
              }
          },
          "NetworkSettings": {
              "Bridge": "",
              "SandboxID": "0380824889f0f7b648d5be6ca023f71afa9cb59ae7de4e63f3d1f26989a9c5f8",
              "HairpinMode": false,
              "LinkLocalIPv6Address": "",
              "LinkLocalIPv6PrefixLen": 0,
              "Ports": {
                  "443/tcp": null,
                  "80/tcp": null
              },
              "SandboxKey": "/var/run/docker/netns/0380824889f0",
              "SecondaryIPAddresses": null,
              "SecondaryIPv6Addresses": null,
              "EndpointID": "62f56b0d394a7dfb0f6943274402b3ac99b2064cbc28392186b123a0a0347472",
              "Gateway": "172.17.0.1",
              "GlobalIPv6Address": "",
              "GlobalIPv6PrefixLen": 0,
              "IPAddress": "172.17.0.8",
              "IPPrefixLen": 16,
              "IPv6Gateway": "",
              "MacAddress": "02:42:ac:11:00:08",
              "Networks": {
                  "bridge": {
                      "IPAMConfig": null,
                      "Links": null,
                      "Aliases": null,
                      "NetworkID": "ec1c5c12d4d9f601aba1658518ffbfdcc5232b881e0e0d86dab9ab8dcb22d758",
                      "EndpointID": "62f56b0d394a7dfb0f6943274402b3ac99b2064cbc28392186b123a0a0347472",
                      "Gateway": "172.17.0.1",
                      "IPAddress": "172.17.0.8",
                      "IPPrefixLen": 16,
                      "IPv6Gateway": "",
                      "GlobalIPv6Address": "",
                      "GlobalIPv6PrefixLen": 0,
                      "MacAddress": "02:42:ac:11:00:08",
                      "DriverOpts": null
                  }
              }
          }
      }
  ]
  [user@control centos_web]$ curl 172.17.0.9
  curl: (7) Failed connect to 172.17.0.9:80; No route to host
  [user@control centos_web]$ curl 172.17.0.8
  
  hello world. its dockerfile~~~~~~~~~~~~~~~~~~~~~!
  
  
  
  ```







```
[user@control centos_web]$ cd
[user@control ~]$ docker search resistry
NAME                DESCRIPTION         STARS               OFFICIAL            AUTOMATED
[user@control ~]$ docker search registry
NAME                                 DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
registry                             The Docker Registry 2.0 implementation for s…   3049                [OK]                
distribution/registry                WARNING: NOT the registry official image!!! …   57                                      [OK]
stefanscherer/registry-windows       Containerized docker registry for Windows Se…   32                                      
budry/registry-arm                   Docker registry build for Raspberry PI 2 and…   18                                      
deis/registry                        Docker image registry for the Deis open sour…   12                                      
jc21/registry-ui                     A nice web interface for managing your Docke…   11                                      
anoxis/registry-cli                  You can list and delete tags from your priva…   10                                      [OK]
vmware/registry                                                                      6                                       
pallet/registry-swift                Add swift storage support to the official do…   4                                       [OK]
allingeek/registry                   A specialization of registry:2 configured fo…   4                                       [OK]
arm32v6/registry                     The Docker Registry 2.0 implementation for s…   3                                       
goharbor/registry-photon                                                             2                                       
concourse/registry-image-resource                                                    1                                       
conjurinc/registry-oauth-server      Docker registry authn/authz server backed by…   1                                       
ibmcom/registry                      Docker Image for IBM Cloud private-CE (Commu…   1                                       
metadata/registry                    Metadata Registry is a tool which helps you …   1                                       [OK]
webhippie/registry                   Docker images for Registry                      1                                       [OK]
kontena/registry                     Kontena Registry                                0                                       
dwpdigital/registry-image-resource   Concourse resource type                         0                                       
gisjedi/registry-proxy               Reverse proxy of registry mirror image gisje…   0                                       
convox/registry                                                                      0                                       
lorieri/registry-ceph                Ceph Rados Gateway (and any other S3 compati…   0                                       
pivnet/registry-gcloud-image                                                         0                                       
digillect/registry-cleaner           Tool to remove unused images from Docker reg…   0                                       [OK]
upmcenterprises/registry-creds                                                       0                                       
[user@control ~]$ docker pull registry
Using default tag: latest
latest: Pulling from library/registry
cbdbe7a5bc2a: Pull complete 
47112e65547d: Pull complete 
46bcb632e506: Pull complete 
c1cc712bcecd: Pull complete 
3db6272dcbfa: Pull complete 
Digest: sha256:8be26f81ffea54106bae012c6f349df70f4d5e7e2ec01b143c46e2c03b9e551d
Status: Downloaded newer image for registry:latest
docker.io/library/registry:latest
[user@control ~]$ docker run -d -p 5000:5000 --name registry registry:latest 
6e51bfb75786b2aeb82bb4945163ed3a77c3ee386405ff386c87e14a88a5d952
[user@control ~]$ docker ps | grep registry
6e51bfb75786        registry:latest           "/entrypoint.sh /etc…"   11 seconds ago      Up 11 seconds       0.0.0.0:5000->5000/tcp   registry
[user@control ~]$ docker tag centos:latest localhost:5000/mycentos:latest
[user@control ~]$ docker push localhost:5000/mycentos
The push refers to repository [localhost:5000/mycentos]
291f6e44771a: Pushed 
latest: digest: sha256:fc4a234b91cc4b542bac8a6ad23b2ddcee60ae68fc4dbd4a52efb5f1b0baad71 size: 529
[user@control ~]$ docker push localhost:5000/mycentos:latest 
The push refers to repository [localhost:5000/mycentos]
291f6e44771a: Layer already exists 
latest: digest: sha256:fc4a234b91cc4b542bac8a6ad23b2ddcee60ae68fc4dbd4a52efb5f1b0baad71 size: 529
[user@control ~]$ docker rmi centos:latest 
Untagged: centos:latest
Untagged: centos@sha256:76d24f3ba3317fa945743bb3746fbaf3a0b752f10b10376960de01da70685fbd
[user@control ~]$ docker rmi localhost:5000/mycentos:latest 
Untagged: localhost:5000/mycentos:latest
Untagged: localhost:5000/mycentos@sha256:fc4a234b91cc4b542bac8a6ad23b2ddcee60ae68fc4dbd4a52efb5f1b0baad71
[user@control ~]$ docker pull localhost:5000/mycentos:latest
latest: Pulling from mycentos
Digest: sha256:fc4a234b91cc4b542bac8a6ad23b2ddcee60ae68fc4dbd4a52efb5f1b0baad71
Status: Downloaded newer image for localhost:5000/mycentos:latest
localhost:5000/mycentos:latest
[user@control ~]$ sudo poweroff
[sudo] password for user: 
Connection to 192.168.56.100 closed by remote host.
Connection to 192.168.56.100 closed.
student@CCCR01:~$ ssh user@192.168.56.100
user@192.168.56.100's password: 
Last login: Tue Aug 11 10:12:36 2020 from 192.168.56.1
[user@control ~]$ systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:httpd(8)
           man:apachectl(8)
[user@control ~]$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[user@control ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
6e51bfb75786        registry:latest     "/entrypoint.sh /etc…"   9 minutes ago       Exited (2) 4 minutes ago                       registry
6d1e0d1f9a6f        httpd:latest        "httpd-foreground"       3 hours ago         Exited (0) 4 minutes ago                       web6
f2d166e820e0        httpd:latest        "httpd-foreground"       3 hours ago         Exited (0) 3 hours ago                         web5
f80432e9669e        831691599b88        "/bin/bash"              3 hours ago         Exited (0) 4 minutes ago                       os11
58db52fb5355        831691599b88        "/bin/bash"              3 hours ago         Exited (0) 4 minutes ago                       os10
fa606eca6ccc        httpd               "httpd-foreground"       3 hours ago         Exited (1) 3 hours ago                         webserver2
75a5714c23b3        httpd:latest        "httpd-foreground"       3 hours ago         Exited (1) 3 hours ago                         web4
68d587a30d4d        831691599b88        "/bin/bash"              4 hours ago         Exited (0) 4 minutes ago                       os8
72b930a2e3f2        831691599b88        "/bin/bash"              4 hours ago         Exited (0) 4 hours ago                         awesome_gates
5c205c085889        831691599b88        "/bin/bash"              22 hours ago        Exited (0) 4 minutes ago                       os7
c9326476cf7a        831691599b88        "/bin/bash"              22 hours ago        Exited (0) 21 hours ago                        os6
954b255baf7a        mysql:5.7           "docker-entrypoint.s…"   22 hours ago        Exited (0) 21 hours ago                        db2
304473e833e9        mysql:5.7           "docker-entrypoint.s…"   22 hours ago        Exited (1) 22 hours ago                        db1
f56ecb95c2e0        831691599b88        "/bin/bash"              23 hours ago        Exited (0) 21 hours ago                        os5
7d8dec544a81        831691599b88        "/bin/bash"              23 hours ago        Exited (0) 23 hours ago                        os4
f07bc745561d        831691599b88        "/bin/bash"              23 hours ago        Exited (0) 23 hours ago                        os3
a5eab3338ea5        httpd:latest        "httpd-foreground"       23 hours ago        Exited (0) 4 minutes ago                       web2
c71d40769b82        831691599b88        "/bin/bash"              23 hours ago        Exited (0) 21 hours ago                        os2
1dc2c54f2525        httpd:latest        "httpd-foreground"       24 hours ago        Exited (0) 4 minutes ago                       web1
[user@control ~]$ docker system prune
WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all dangling images
  - all dangling build cache

Are you sure you want to continue? [y/N] y
Deleted Containers:
6e51bfb75786b2aeb82bb4945163ed3a77c3ee386405ff386c87e14a88a5d952
6d1e0d1f9a6f3c912e035f8d4eec88c2072738e0f9da797e30f34ecc4fba3c27
f2d166e820e04d77c3a98d7262b2f0b87f4e2a495ef23b2a35861a18167da25e
f80432e9669e77cc09e93a0dc31eade79e019964fcf7746548b3cef5d001bbc7
58db52fb535592826501ebaafe2e6477fbfa2de43dd8082525e1403290dab2cd
fa606eca6ccc3a53ec3b0eeb864bd64348f7905ff1c8d41e3e82667f6016a6f7
75a5714c23b3cb0bf7c9ad27d48ef28ea475b6d3b133031078446875d8d5dd30
68d587a30d4d253ee61b6234b90d80769cb90a7a03ca866efe40e2c55b072c77
72b930a2e3f295862e293c79edb295288007b4ef1a7209c402fd33eabb5d8078
5c205c0858895c3030cb452de2992860f750fe1ee07f33e27a3882aabc2568d3
c9326476cf7abfba6eeafb38a0707926ed5af2bde92fba873f712754d5902e8a
954b255baf7a592b7053c7eb8761829b7650400fbf3a811ae7051ef914718188
304473e833e907d8486c79052aaee89f001beb72c2ff33199e5c3101d2f4df91
f56ecb95c2e06b59c5114955fbc0a431d6b474a81cacd03fb3cdc25595e2fe0f
7d8dec544a813884c9dab9cf6b2bfd93892b34989a35072251635fd48c69e305
f07bc745561d208a2b18a883820524e4afe2083959730fa9da0f3ec9c11d7dc9
a5eab3338ea58de2c0c54a66883d838615042c4f8a56385d4d5f50c72295bf42
c71d40769b828d26eb1c28fc622b5e6430dea48f1f90d605d0d69778eda79cd7
1dc2c54f2525bab8d1ec6d083cebbe98507601d59cec0796fb8df325687ebe83

Deleted Networks:
net1
macvlan1

Total reclaimed space: 2.716kB
[user@control ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[user@control ~]$ docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
pohibebohu/myweb          latest              f0ca7927cdc2        25 minutes ago      254MB
myweb                     import              a24d0e151e30        2 hours ago         215MB
pohibebohu/myweb          0.1                 c57f1012354f        2 hours ago         166MB
localhost:5000/mycentos   latest              0d120b6ccaa8        11 hours ago        215MB
httpd                     latest              a6ea92c35c43        5 days ago          166MB
mysql                     5.7                 718a6da099d8        6 days ago          448MB
registry                  latest              2d4f4b5309b1        7 weeks ago         26.2MB
pohibebohu/mycentos       latest              831691599b88        7 weeks ago         215MB
mycentos                  latest              831691599b88        7 weeks ago         215MB
nobreak/docker            centos              831691599b88        7 weeks ago         215MB
hello-world               latest              bf756fb1ae65        7 months ago        13.3kB
[user@control ~]$ docker image rm `docker images -q`
Untagged: pohibebohu/myweb:latest
Deleted: sha256:f0ca7927cdc2bf4478aa595cc8b6838d47ce0f9ba1cf0d08161821b3ae4d2e54
Deleted: sha256:f7a7b6e1bc6bc3ebab6c05486c1533d439fcde50f9ab60a674c02c78b079425d
Deleted: sha256:13b5941370e399fb2eceb911a1c3f5d370dce48f4ac33d0f88a976ee17ca0b8b
Deleted: sha256:7b0f41fa7013f0bf5783cfaeb9ebbfe4f09148e231dd864be9e43e5ff06de8e0
Deleted: sha256:802f39312e8ef49d247f2322bdbec32893f828238d3a5032891b1d3b3ba8241c
Deleted: sha256:8c41afea198d71d803907f784f7a566b9a37c8bfffa3b22f205c83af37e97be5
Deleted: sha256:17b943cced944bcf67aa636baeefc50b4c48e7e36090905d3a51842d59d0d50d
Deleted: sha256:7c79e3e5308836b25d683b0e8a3f1c7b75e6a0690f67fa0f952d5d4f072626ed
Deleted: sha256:1308d05c373488167e7d6299fa270cbba3bc96481a8b952b489a2cad45abbb32
Deleted: sha256:044e7126f4ea6e1cdb097bec94e7ab19ef2737ca272ef7115d111b13578a0194
Untagged: myweb:import
Deleted: sha256:a24d0e151e300b95b8db4ef46856d91c6a4ba08d18a82c667e9bbcbd4ddcf115
Deleted: sha256:a5575c21f540c2399a8a9e4a2b7876d9acc807089ae36b642b9a13118ace9199
Untagged: pohibebohu/myweb:0.1
Deleted: sha256:c57f1012354f5f5f377e28c6eac9e5bfea6e55ed6643399781dda893797cca24
Deleted: sha256:cc9ea2b43e4708beb5d3c05be037bc8df2e1ce86289bab0a9e65999145867ccf
Untagged: localhost:5000/mycentos:latest
Untagged: localhost:5000/mycentos@sha256:fc4a234b91cc4b542bac8a6ad23b2ddcee60ae68fc4dbd4a52efb5f1b0baad71
Deleted: sha256:0d120b6ccaa8c5e149176798b3501d4dd1885f961922497cd0abef155c869566
Deleted: sha256:291f6e44771a7b4399b0c6fb40ab4fe0331ddf76eda11080f052b003d96c7726
Untagged: httpd:latest
Deleted: sha256:a6ea92c35c43206ac8a508b2be7d6d6b5ecf5f40e7a9042a35669bcbcb2da201
Deleted: sha256:074e0e3314f787dd955937eb5b17694b1f7fc64631f404223a62e2a4a1292fb6
Deleted: sha256:b05020dd1c0b21291751d69304826e21518a0fa056229bb5c5646a2f80aa2ce5
Deleted: sha256:0724735f53919994876490bd1e9633792f9533a646f2f5cd6a2c607e4c86909a
Deleted: sha256:378cb5ce0d682b9abc91a9cf70ffc7b0ab650d282d90c60439af8158c944041d
Untagged: mysql:5.7
Untagged: mysql@sha256:da58f943b94721d46e87d5de208dc07302a8b13e638cd1d24285d222376d6d84
Deleted: sha256:718a6da099d82183c064a964523c0deca80619cb033aadd15854771fe592a480
Deleted: sha256:058d93ef2bfb943ba6a19d8b679c702be96e34337901da9e1a07ad62b772bf3d
Deleted: sha256:7bca77783fcf15499a0386127dd7d5c679328a21b6566c8be861ba424ac13e49
Deleted: sha256:183d05512fa88dfa8c17abb9b6f09a79922d9e9ee001a33ef34d1bc094bf8f9f
Deleted: sha256:165805124136fdee738ed19021a522bb53de75c2ca9b6ca87076f51c27385fd7
Deleted: sha256:904abdc2d0bea0edbb1a8171d1a1353fa6de22150a9c5d81358799a5b6c38c8d
Deleted: sha256:d26f7649f78cf789267fbbca8aeb234932e230109c728632c6b9fbc60ca5591b
Deleted: sha256:7fcf7796e23ea5b42eb3bbd5bec160ba5f5f47ecb239053762f9cf766c143942
Deleted: sha256:826130797a5760bcd2bb19a6c6d92b5f4860bbffbfa954f5d3fc627904a76e9d
Deleted: sha256:53e0181c63e41fb85bce681ec8aadfa323cd00f70509107f7001a1d0614e5adf
Deleted: sha256:d6854b83e83d7eb48fb0ef778c58a8b839adb932dd036a085d94a7c2db98f890
Deleted: sha256:d0f104dc0a1f9c744b65b23b3fd4d4d3236b4656e67f776fe13f8ad8423b955c
Untagged: registry:latest
Untagged: registry@sha256:8be26f81ffea54106bae012c6f349df70f4d5e7e2ec01b143c46e2c03b9e551d
Deleted: sha256:2d4f4b5309b1e41b4f83ae59b44df6d673ef44433c734b14c1c103ebca82c116
Deleted: sha256:36a83751a1193f6edf65d21f62fe7988394d562d71904ee20cb4aa72f14148bd
Deleted: sha256:c2588dfa0db64031c1c0683ed59a00ad0943889efb819bffd0455ae9b232016c
Deleted: sha256:6eb97e254200a2d82064e63977f84626e376256fe4d4c033a8b7817b2950071d
Deleted: sha256:7c6634cc9948baef3600559e298228a962bd9499cbbaf840d4d4889f5abaca2a
Deleted: sha256:3e207b409db364b595ba862cdc12be96dcdad8e36c59a03b7b3b61c946a5741a
Untagged: hello-world:latest
Untagged: hello-world@sha256:49a1c8800c94df04e9658809b006fd8a686cab8028d33cfba2cc049724254202
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b
Deleted: sha256:9c27e219663c25e0f28493790cc0b88bc973ba3b1686355f221c38a36978ac63
Error response from daemon: conflict: unable to delete 831691599b88 (must be forced) - image is referenced in multiple repositories
Error response from daemon: conflict: unable to delete 831691599b88 (must be forced) - image is referenced in multiple repositories
Error response from daemon: conflict: unable to delete 831691599b88 (must be forced) - image is referenced in multiple repositories
[user@control ~]$ 
[user@control ~]$ docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
mycentos              latest              831691599b88        7 weeks ago         215MB
nobreak/docker        centos              831691599b88        7 weeks ago         215MB
pohibebohu/mycentos   latest              831691599b88        7 weeks ago         215MB
[user@control ~]$ docker rmi -f `docker images -q`
Untagged: mycentos:latest
Untagged: nobreak/docker:centos
Untagged: pohibebohu/mycentos:latest
Untagged: pohibebohu/mycentos@sha256:fd84102fc72960dd1b8da0ee3b4c13e3b0c1d2a085de118bc4c97821cd986e02
Deleted: sha256:831691599b88ad6cc2a4abbd0e89661a121aff14cfa289ad840fd3946f274f1f
Deleted: sha256:eb29745b8228e1e97c01b1d5c2554a319c00a94d8dd5746a3904222ad65a13f8
Error: No such image: 831691599b88
Error: No such image: 831691599b88
[user@control ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE

```





tar zxf harbor-offline-install





```
[user@control ~]$ sudo curl -L "https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
[sudo] password for user: 
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   638  100   638    0     0   3929      0 --:--:-- --:--:-- --:--:--  3962
100 16.3M  100 16.3M    0     0  4037k      0  0:00:04  0:00:04 --:--:-- 5072k
[user@control ~]$ ls -l /usr/local/bin/docker-compose 
-rw-r--r--. 1 root root 17176256  8월 11 14:45 /usr/local/bin/docker-compose
[user@control ~]$ md5sum /usr/local/bin/docker-compose 
28c8173eb3d1c3d5005c28dabfb7cb4e  /usr/local/bin/docker-compose
[user@control ~]$ 
[user@control ~]$ 
[user@control ~]$ sudo chmod +x /usr/local/bin/docker-compose 
[sudo] password for user: 
[user@control ~]$ docker-compose --version
docker-compose version 1.25.4, build 8d51620a
[user@control ~]$ ls 
bm1  centos_web  Desktop  Documents  Downloads  export.tar  img.tar  Music  passwd  Pictures  Public  Templates  Videos
[user@control ~]$ wget https://github.com/goharbor/harbor/releases/download/v1.10.1/harbor-offline-installer-v1.10.1.tgz
--2020-08-11 14:59:40--  https://github.com/goharbor/harbor/releases/download/v1.10.1/harbor-offline-installer-v1.10.1.tgz
Resolving github.com (github.com)... 52.78.231.108
Connecting to github.com (github.com)|52.78.231.108|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://github-production-release-asset-2e65be.s3.amazonaws.com/50613991/456bbd00-4f2e-11ea-98ff-0e673c1c6893?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20200811%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20200811T055923Z&X-Amz-Expires=300&X-Amz-Signature=e880e4dc7f1b41665fbccdb0e0cde70d5bda00bfce2e7d071e599e80fbd91cca&X-Amz-SignedHeaders=host&actor_id=0&repo_id=50613991&response-content-disposition=attachment%3B%20filename%3Dharbor-offline-installer-v1.10.1.tgz&response-content-type=application%2Foctet-stream [following]
--2020-08-11 14:59:40--  https://github-production-release-asset-2e65be.s3.amazonaws.com/50613991/456bbd00-4f2e-11ea-98ff-0e673c1c6893?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20200811%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20200811T055923Z&X-Amz-Expires=300&X-Amz-Signature=e880e4dc7f1b41665fbccdb0e0cde70d5bda00bfce2e7d071e599e80fbd91cca&X-Amz-SignedHeaders=host&actor_id=0&repo_id=50613991&response-content-disposition=attachment%3B%20filename%3Dharbor-offline-installer-v1.10.1.tgz&response-content-type=application%2Foctet-stream
Resolving github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)... 54.231.11.152
Connecting to github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)|54.231.11.152|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 674078519 (643M) [application/octet-stream]
Saving to: ‘harbor-offline-installer-v1.10.1.tgz’

100%[=================================================================================================================================>] 674,078,519 9.33MB/s   in 81s    

2020-08-11 15:01:03 (7.91 MB/s) - ‘harbor-offline-installer-v1.10.1.tgz’ saved [674078519/674078519]

[user@control ~]$ tar zxf harbor-offline-installer-v1.10.1.tgz 
[user@control ~]$ ls
bm1  centos_web  Desktop  Documents  Downloads  export.tar  harbor  harbor-offline-installer-v1.10.1.tgz  img.tar  Music  passwd  Pictures  Public  Templates  Videos
[user@control ~]$ tar zxf harbor-offline-installer-v1.10.1.tgz 
[user@control ~]$ ls
bm1  centos_web  Desktop  Documents  Downloads  export.tar  harbor  harbor-offline-installer-v1.10.1.tgz  img.tar  Music  passwd  Pictures  Public  Templates  Videos
[user@control ~]$ cd harbor/
[user@control harbor]$ la
bash: la: command not found...
[user@control harbor]$ ls
common.sh  harbor.v1.10.1.tar.gz  harbor.yml  install.sh  LICENSE  prepare
[user@control harbor]$ hostnamectl
   Static hostname: control.nobreak.local
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 4cd7f8ced528464eb55d0a14baede17a
           Boot ID: 2ae5f775296c4517bc7730797ee06823
    Virtualization: kvm
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-1127.18.2.el7.x86_64
      Architecture: x86-64
[user@control harbor]$ sudo vi harbor.yml 
[sudo] password for user: 
[user@control harbor]$ sudo vi /etc/docker/daemon.json
[user@control harbor]$ sudo vi /etc/host
[user@control harbor]$ sudo vi /etc/hosts
[user@control harbor]$ sudo systemctl restart docker
[user@control harbor]$ cd /etc/docker/
[user@control docker]$ ls
key.json
[user@control docker]$ sudo vi daemon.json
[user@control docker]$ docker info
Client:
 Debug Mode: false

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 19.03.12
 Storage Driver: overlay2
  Backing Filesystem: xfs
  Supports d_type: true
  Native Overlay Diff: true
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc version: dc9208a3303feef5b3839f4323d9beb36df0a9dd
 init version: fec3683
 Security Options:
  seccomp
   Profile: default
 Kernel Version: 3.10.0-1127.18.2.el7.x86_64
 Operating System: CentOS Linux 7 (Core)
 OSType: linux
 Architecture: x86_64
 CPUs: 2
 Total Memory: 3.701GiB
 Name: control.nobreak.local
 ID: TQAH:4AEG:HLR7:T4H4:3ZR5:HKCP:SOZ2:NQK4:WQKC:CUXY:O24O:DHJ4
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Username: pohibebohu
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false

WARNING: bridge-nf-call-iptables is disabled
WARNING: bridge-nf-call-ip6tables is disabled
[user@control docker]$ 
[user@control docker]$ sudo vi /etc/docker/daemon.json 
[user@control docker]$ sudo systemctl restart docker
[user@control docker]$ sudo vi /etc/docker/daemon.json 
[user@control docker]$ sudo systemctl restart docker
[user@control docker]$ cat /etc/docker/daemon.json 
{
"insecure-registries" : ["192.168.56.100"]
}
[user@control docker]$ sudo systemctl restart docker
[user@control docker]$ cd ..
[user@control etc]$ cd..
bash: cd..: command not found...
[user@control etc]$ cd ..
[user@control /]$ ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[user@control /]$ cd ~
[user@control ~]$ ls
bm1  centos_web  Desktop  Documents  Downloads  export.tar  harbor  harbor-offline-installer-v1.10.1.tgz  img.tar  Music  passwd  Pictures  Public  Templates  Videos
[user@control ~]$ cd harbor/
[user@control harbor]$ docker info
Client:
 Debug Mode: false

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 19.03.12
 Storage Driver: overlay2
  Backing Filesystem: xfs
  Supports d_type: true
  Native Overlay Diff: true
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc version: dc9208a3303feef5b3839f4323d9beb36df0a9dd
 init version: fec3683
 Security Options:
  seccomp
   Profile: default
 Kernel Version: 3.10.0-1127.18.2.el7.x86_64
 Operating System: CentOS Linux 7 (Core)
 OSType: linux
 Architecture: x86_64
 CPUs: 2
 Total Memory: 3.701GiB
 Name: control.nobreak.local
 ID: TQAH:4AEG:HLR7:T4H4:3ZR5:HKCP:SOZ2:NQK4:WQKC:CUXY:O24O:DHJ4
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Username: pohibebohu
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  192.168.56.100
  127.0.0.0/8
 Live Restore Enabled: false

WARNING: bridge-nf-call-iptables is disabled
WARNING: bridge-nf-call-ip6tables is disabled
[user@control harbor]$ host localhost
localhost.nobreak.local has address 218.38.137.27
[user@control harbor]$ vi harbor.yml 
[user@control harbor]$ ping control.nobreak.local
PING control.nobreak.local (218.38.137.27) 56(84) bytes of data.
^Z
[1]+  Stopped                 ping control.nobreak.local
[user@control harbor]$ sudo vi /etc/hosts
[user@control harbor]$ sudo ./install.sh 

[Step 0]: checking if docker is installed ...

Note: docker version: 19.03.12

[Step 1]: checking docker-compose is installed ...
✖ Need to install docker-compose(1.18.0+) by yourself first and run this script again.
[user@control harbor]$ echo $PATH
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/user/.local/bin:/home/user/bin
[user@control harbor]$ ^C
[user@control harbor]$ ^C
[user@control harbor]$ exit
logout
There are stopped jobs.
[user@control harbor]$ 
[user@control harbor]$ logout
Connection to 192.168.56.100 closed.
student@CCCR01:~$ sudo -E env "/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/user/.local/bin:/home/user/bin
> ^C
student@CCCR01:~$ sudo -E env "PATH=$PATH" ./install.sh
[sudo] password for student: 
env: ‘./install.sh’: Permission denied
student@CCCR01:~$ sudo -E env "PATH=$PATH" ./install.sh
env: ‘./install.sh’: Permission denied
student@CCCR01:~$ ls
 CentOS-7-x86_64-Minimal-2003.iso   Documents          GNS3              install.sh          nano.save     Public      Templates        'win7(ubntu-vb).ova'
 Desktop                            Downloads          hangyeol.tar.gz   일러스트레이터.md   nano.save.1   rtl8821ce   Videos
'Docker Network.md'                 examples.desktop   index.html        Music               Pictures      snap       'VirtualBox VMs'
student@CCCR01:~$ ssh user@192.168.56.100
user@192.168.56.100's password: 
Last login: Tue Aug 11 14:11:52 2020 from 192.168.56.1
[user@control ~]$ sudo -E env "PATH=$PATH" ./install.sh
[sudo] password for user: 
env: ./install.sh: No such file or directory
[user@control ~]$ ls
bm1  centos_web  Desktop  Documents  Downloads  export.tar  harbor  harbor-offline-installer-v1.10.1.tgz  img.tar  Music  passwd  Pictures  Public  Templates  Videos
[user@control ~]$ cd harbor/
[user@control harbor]$ sudo -E env "PATH=$PATH" ./install.sh

[Step 0]: checking if docker is installed ...

Note: docker version: 19.03.12

[Step 1]: checking docker-compose is installed ...

Note: docker-compose version: 1.25.4

[Step 2]: loading Harbor images ...
47a4bb1cfbc7: Loading layer [==================================================>]  34.26MB/34.26MB
c2d9cf7a4eaf: Loading layer [==================================================>]  9.056MB/9.056MB
32b7eca4f03e: Loading layer [==================================================>]   9.71MB/9.71MB
5d104292391f: Loading layer [==================================================>]   9.71MB/9.71MB
Loaded image: goharbor/clair-adapter-photon:v1.0.1-v1.10.1
0039915754c6: Loading layer [==================================================>]  12.83MB/12.83MB
0a4ba5bc8bc2: Loading layer [==================================================>]  49.37MB/49.37MB
Loaded image: goharbor/harbor-jobservice:v1.10.1
db95a8bece9e: Loading layer [==================================================>]  78.32MB/78.32MB
f9aa9f9a3393: Loading layer [==================================================>]  3.072kB/3.072kB
f0baa86a6ccd: Loading layer [==================================================>]   59.9kB/59.9kB
2b8f0a44b78f: Loading layer [==================================================>]  61.95kB/61.95kB
Loaded image: goharbor/redis-photon:v1.10.1
a9f6374f6301: Loading layer [==================================================>]   9.05MB/9.05MB
b3c39a738965: Loading layer [==================================================>]  6.239MB/6.239MB
dce1aa68054e: Loading layer [==================================================>]  16.04MB/16.04MB
612aac9b538e: Loading layer [==================================================>]  28.24MB/28.24MB
8612386fb841: Loading layer [==================================================>]  22.02kB/22.02kB
53883649d694: Loading layer [==================================================>]  50.52MB/50.52MB
Loaded image: goharbor/notary-server-photon:v0.6.1-v1.10.1
f3967aa0de5f: Loading layer [==================================================>]  115.8MB/115.8MB
42fd590be962: Loading layer [==================================================>]  12.14MB/12.14MB
096b03f3e32b: Loading layer [==================================================>]  3.072kB/3.072kB
aa66bf3b8224: Loading layer [==================================================>]  49.15kB/49.15kB
8e76391190c9: Loading layer [==================================================>]  3.584kB/3.584kB
131573c722d5: Loading layer [==================================================>]  13.02MB/13.02MB
Loaded image: goharbor/clair-photon:v2.1.1-v1.10.1
69e43242ff64: Loading layer [==================================================>]  50.39MB/50.39MB
5a3a8e835993: Loading layer [==================================================>]  3.584kB/3.584kB
4dff480c159c: Loading layer [==================================================>]  3.072kB/3.072kB
2be454a39481: Loading layer [==================================================>]   2.56kB/2.56kB
d6b4877d5c4e: Loading layer [==================================================>]  3.072kB/3.072kB
609537197cce: Loading layer [==================================================>]  3.584kB/3.584kB
b52d06cad304: Loading layer [==================================================>]  12.29kB/12.29kB
Loaded image: goharbor/harbor-log:v1.10.1
badcf9296df7: Loading layer [==================================================>]  9.056MB/9.056MB
8b17ed0102b2: Loading layer [==================================================>]  3.584kB/3.584kB
4d1cd50b8d4f: Loading layer [==================================================>]  3.072kB/3.072kB
491c41e50d6a: Loading layer [==================================================>]  21.76MB/21.76MB
0101dc4148c7: Loading layer [==================================================>]  22.59MB/22.59MB
Loaded image: goharbor/registry-photon:v2.7.1-patch-2819-2553-v1.10.1
77965b8f534b: Loading layer [==================================================>]  14.61MB/14.61MB
940bf05c9601: Loading layer [==================================================>]  28.24MB/28.24MB
7ca206eb72e3: Loading layer [==================================================>]  22.02kB/22.02kB
9898774a5bad: Loading layer [==================================================>]  49.09MB/49.09MB
Loaded image: goharbor/notary-signer-photon:v0.6.1-v1.10.1
86340c56281e: Loading layer [==================================================>]  9.055MB/9.055MB
8ac45c34690f: Loading layer [==================================================>]  42.31MB/42.31MB
4aa70453f992: Loading layer [==================================================>]  3.072kB/3.072kB
6cbc6ab111b2: Loading layer [==================================================>]  3.584kB/3.584kB
d6d7b70c18b5: Loading layer [==================================================>]  43.14MB/43.14MB
Loaded image: goharbor/chartmuseum-photon:v0.9.0-v1.10.1
07efa003923d: Loading layer [==================================================>]  9.056MB/9.056MB
96dd65c291de: Loading layer [==================================================>]  3.584kB/3.584kB
802a1397679e: Loading layer [==================================================>]  21.76MB/21.76MB
92c283f6895a: Loading layer [==================================================>]  3.072kB/3.072kB
8b61b91240a6: Loading layer [==================================================>]  8.662MB/8.662MB
1fd753477b6f: Loading layer [==================================================>]  31.24MB/31.24MB
Loaded image: goharbor/harbor-registryctl:v1.10.1
48cda078e98d: Loading layer [==================================================>]  10.89MB/10.89MB
Loaded image: goharbor/nginx-photon:v1.10.1
2c00d9af2c3f: Loading layer [==================================================>]  34.31MB/34.31MB
0e4ae15c3d4e: Loading layer [==================================================>]  339.9MB/339.9MB
27810c9ff1a9: Loading layer [==================================================>]  135.2kB/135.2kB
Loaded image: goharbor/harbor-migrator:v1.10.1
93e0577272a9: Loading layer [==================================================>]  34.24MB/34.24MB
fb3507ff707e: Loading layer [==================================================>]  117.4MB/117.4MB
f59c6315bf8a: Loading layer [==================================================>]  42.32MB/42.32MB
5c5501748347: Loading layer [==================================================>]   2.56kB/2.56kB
3710d94e58dd: Loading layer [==================================================>]  1.536kB/1.536kB
06e0f1585c01: Loading layer [==================================================>]  166.4kB/166.4kB
bf5ac9b9c61c: Loading layer [==================================================>]   3.01MB/3.01MB
Loaded image: goharbor/prepare:v1.10.1
9ea2dad46741: Loading layer [==================================================>]  10.89MB/10.89MB
b280b9e7ca3b: Loading layer [==================================================>]  7.696MB/7.696MB
a1ce7c1130ee: Loading layer [==================================================>]  223.2kB/223.2kB
3b0492a7358b: Loading layer [==================================================>]  195.1kB/195.1kB
c64d6e6c80dd: Loading layer [==================================================>]  15.36kB/15.36kB
ace4e9064f06: Loading layer [==================================================>]  3.584kB/3.584kB
Loaded image: goharbor/harbor-portal:v1.10.1
b74d8257d4e7: Loading layer [==================================================>]  12.83MB/12.83MB
92f141040819: Loading layer [==================================================>]   42.5MB/42.5MB
33c99383fac5: Loading layer [==================================================>]  5.632kB/5.632kB
2bacc160d10f: Loading layer [==================================================>]  40.45kB/40.45kB
f51bed904fbe: Loading layer [==================================================>]   42.5MB/42.5MB
Loaded image: goharbor/harbor-core:v1.10.1
a2ffdaaa3434: Loading layer [==================================================>]  63.56MB/63.56MB
5745ac9e0297: Loading layer [==================================================>]  54.44MB/54.44MB
dc1d24cbb1d5: Loading layer [==================================================>]  5.632kB/5.632kB
4280f2f98340: Loading layer [==================================================>]  2.048kB/2.048kB
0f9279b20eec: Loading layer [==================================================>]   2.56kB/2.56kB
8b9162d25131: Loading layer [==================================================>]   2.56kB/2.56kB
50ad7481e5af: Loading layer [==================================================>]   2.56kB/2.56kB
54b809bfb5ec: Loading layer [==================================================>]  10.24kB/10.24kB
Loaded image: goharbor/harbor-db:v1.10.1


[Step 3]: preparing environment ...

[Step 4]: preparing harbor configs ...
prepare base dir is set to /home/user/harbor
WARNING:root:WARNING: HTTP protocol is insecure. Harbor will deprecate http protocol in the future. Please make sure to upgrade to https
Generated configuration file: /config/log/logrotate.conf
Generated configuration file: /config/log/rsyslog_docker.conf
Generated configuration file: /config/nginx/nginx.conf
Generated configuration file: /config/core/env
Generated configuration file: /config/core/app.conf
Generated configuration file: /config/registry/config.yml
Generated configuration file: /config/registryctl/env
Generated configuration file: /config/db/env
Generated configuration file: /config/jobservice/env
Generated configuration file: /config/jobservice/config.yml
Generated and saved secret to file: /secret/keys/secretkey
Generated certificate, key file: /secret/core/private_key.pem, cert file: /secret/registry/root.crt
Generated configuration file: /compose_location/docker-compose.yml
Clean up the input dir



[Step 5]: starting Harbor ...
Creating network "harbor_harbor" with the default driver
Creating harbor-log ... done
Creating registryctl   ... done
Creating redis         ... done
Creating harbor-db     ... done
Creating harbor-portal ... done
Creating registry      ... done
Creating harbor-core   ... done
Creating nginx             ... done
Creating harbor-jobservice ... done
✔ ----Harbor has been installed and started successfully.----

```





```
[user@control harbor]$ ls
common  common.sh  docker-compose.yml  harbor.v1.10.1.tar.gz  harbor.yml  install.sh  LICENSE  prepare
[user@control harbor]$ vi harbor.yml 
[user@control harbor]$ docker login -u admin -p Harbor12345 192.168.56.100
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Error response from daemon: Get http://192.168.56.100/v2/: Get http://control.nobreak.local/service/token?account=admin&client_id=docker&offline_token=true&service=harbor-registry: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers) (Client.Timeout exceeded while awaiting headers)
[user@control harbor]$ docker login -u admin -p Harbor12345 192.168.56.100
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Error response from daemon: Get http://192.168.56.100/v2/: Get http://control.nobreak.local/service/token?account=admin&client_id=docker&offline_token=true&service=harbor-registry: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers) (Client.Timeout exceeded while awaiting headers)
[user@control harbor]$ sudo vi /etc/docker/daemon.json 
[sudo] password for user: 
[user@control harbor]$ docker login -u admin -p Harbor12345 192.168.56.100
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Error response from daemon: Get http://192.168.56.100/v2/: Get http://control.nobreak.local/service/token?account=admin&client_id=docker&offline_token=true&service=harbor-registry: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
[user@control harbor]$ ^C
[user@control harbor]$ ping control.nobreak.local
PING control.nobreak.local (218.38.137.27) 56(84) bytes of data.
^C
--- control.nobreak.local ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 1999ms

[user@control harbor]$ vi harbor.yml 
[user@control harbor]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
[user@control harbor]$ ping control.nobreak.local
PING control.nobreak.local (218.38.137.27) 56(84) bytes of data.
^C
--- control.nobreak.local ping statistics ---
2 packets transmitted, 0 received, 100% packet loss, time 1000ms

[user@control harbor]$ sudo vi /etc/hosts
[user@control harbor]$ docker login -u admin -p Harbor12345 192.168.56.100
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /home/user/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[user@control harbor]$ sudo systemctl restart docker
[user@control harbor]$ sudo ./install.sh

[Step 0]: checking if docker is installed ...

Note: docker version: 19.03.12

[Step 1]: checking docker-compose is installed ...
✖ Need to install docker-compose(1.18.0+) by yourself first and run this script again.
[user@control harbor]$ docker tag centos:latest 192.168.56.100/library/docker:centos
Error response from daemon: No such image: centos:latest
[user@control harbor]$ docker image

Usage:	docker image COMMAND

Manage images

Commands:
  build       Build an image from a Dockerfile
  history     Show the history of an image
  import      Import the contents from a tarball to create a filesystem image
  inspect     Display detailed information on one or more images
  load        Load an image from a tar archive or STDIN
  ls          List images
  prune       Remove unused images
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rm          Remove one or more images
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

Run 'docker image COMMAND --help' for more information on a command.
[user@control harbor]$ docker images
REPOSITORY                      TAG                              IMAGE ID            CREATED             SIZE
goharbor/chartmuseum-photon     v0.9.0-v1.10.1                   0245d66323de        6 months ago        128MB
goharbor/harbor-migrator        v1.10.1                          a4f99495e0b0        6 months ago        364MB
goharbor/redis-photon           v1.10.1                          550a58b0a311        6 months ago        111MB
goharbor/clair-adapter-photon   v1.0.1-v1.10.1                   2ec99537693f        6 months ago        61.6MB
goharbor/clair-photon           v2.1.1-v1.10.1                   622624e16994        6 months ago        171MB
goharbor/notary-server-photon   v0.6.1-v1.10.1                   e4ff6d1f71f9        6 months ago        143MB
goharbor/notary-signer-photon   v0.6.1-v1.10.1                   d3aae2fc17c6        6 months ago        140MB
goharbor/harbor-registryctl     v1.10.1                          ddef86de6480        6 months ago        104MB
goharbor/registry-photon        v2.7.1-patch-2819-2553-v1.10.1   1a0c5f22cfa7        6 months ago        86.5MB
goharbor/nginx-photon           v1.10.1                          01276d086ad6        6 months ago        44MB
goharbor/harbor-log             v1.10.1                          1f5c9ea164bf        6 months ago        82.3MB
goharbor/harbor-jobservice      v1.10.1                          689368d30108        6 months ago        143MB
goharbor/harbor-core            v1.10.1                          14151d58ac3f        6 months ago        130MB
goharbor/harbor-portal          v1.10.1                          8a9856c37798        6 months ago        52.1MB
goharbor/harbor-db              v1.10.1                          18548720d8ad        6 months ago        148MB
goharbor/prepare                v1.10.1                          897a4d535ced        6 months ago        192MB
[user@control harbor]$ docker pull centos:latest
latest: Pulling from library/centos
3c72a8ed6814: Pull complete 
Digest: sha256:76d24f3ba3317fa945743bb3746fbaf3a0b752f10b10376960de01da70685fbd
Status: Downloaded newer image for centos:latest
docker.io/library/centos:latest
[user@control harbor]$ docker images
REPOSITORY                      TAG                              IMAGE ID            CREATED             SIZE
centos                          latest                           0d120b6ccaa8        12 hours ago        215MB
goharbor/chartmuseum-photon     v0.9.0-v1.10.1                   0245d66323de        6 months ago        128MB
goharbor/harbor-migrator        v1.10.1                          a4f99495e0b0        6 months ago        364MB
goharbor/redis-photon           v1.10.1                          550a58b0a311        6 months ago        111MB
goharbor/clair-adapter-photon   v1.0.1-v1.10.1                   2ec99537693f        6 months ago        61.6MB
goharbor/clair-photon           v2.1.1-v1.10.1                   622624e16994        6 months ago        171MB
goharbor/notary-server-photon   v0.6.1-v1.10.1                   e4ff6d1f71f9        6 months ago        143MB
goharbor/notary-signer-photon   v0.6.1-v1.10.1                   d3aae2fc17c6        6 months ago        140MB
goharbor/harbor-registryctl     v1.10.1                          ddef86de6480        6 months ago        104MB
goharbor/registry-photon        v2.7.1-patch-2819-2553-v1.10.1   1a0c5f22cfa7        6 months ago        86.5MB
goharbor/nginx-photon           v1.10.1                          01276d086ad6        6 months ago        44MB
goharbor/harbor-log             v1.10.1                          1f5c9ea164bf        6 months ago        82.3MB
goharbor/harbor-jobservice      v1.10.1                          689368d30108        6 months ago        143MB
goharbor/harbor-core            v1.10.1                          14151d58ac3f        6 months ago        130MB
goharbor/harbor-portal          v1.10.1                          8a9856c37798        6 months ago        52.1MB
goharbor/harbor-db              v1.10.1                          18548720d8ad        6 months ago        148MB
goharbor/prepare                v1.10.1                          897a4d535ced        6 months ago        192MB
[user@control harbor]$ docker tag centos:latest 192.168.56.100/library/docker:centos
[user@control harbor]$ docker push 192.168.56.100/library/docker:centos
The push refers to repository [192.168.56.100/library/docker]
Get http://192.168.56.100/v2/: dial tcp 192.168.56.100:80: connect: connection refused
[user@control harbor]$ sudo docker push 192.168.56.100/library/docker:centos
The push refers to repository [192.168.56.100/library/docker]
Get http://192.168.56.100/v2/: dial tcp 192.168.56.100:80: connect: connection refused
[user@control harbor]$ docker images
REPOSITORY                      TAG                              IMAGE ID            CREATED             SIZE
192.168.56.100/library/docker   centos                           0d120b6ccaa8        12 hours ago        215MB
centos                          latest                           0d120b6ccaa8        12 hours ago        215MB
goharbor/chartmuseum-photon     v0.9.0-v1.10.1                   0245d66323de        6 months ago        128MB
goharbor/harbor-migrator        v1.10.1                          a4f99495e0b0        6 months ago        364MB
goharbor/redis-photon           v1.10.1                          550a58b0a311        6 months ago        111MB
goharbor/clair-adapter-photon   v1.0.1-v1.10.1                   2ec99537693f        6 months ago        61.6MB
goharbor/clair-photon           v2.1.1-v1.10.1                   622624e16994        6 months ago        171MB
goharbor/notary-server-photon   v0.6.1-v1.10.1                   e4ff6d1f71f9        6 months ago        143MB
goharbor/notary-signer-photon   v0.6.1-v1.10.1                   d3aae2fc17c6        6 months ago        140MB
goharbor/harbor-registryctl     v1.10.1                          ddef86de6480        6 months ago        104MB
goharbor/registry-photon        v2.7.1-patch-2819-2553-v1.10.1   1a0c5f22cfa7        6 months ago        86.5MB
goharbor/nginx-photon           v1.10.1                          01276d086ad6        6 months ago        44MB
goharbor/harbor-log             v1.10.1                          1f5c9ea164bf        6 months ago        82.3MB
goharbor/harbor-jobservice      v1.10.1                          689368d30108        6 months ago        143MB
goharbor/harbor-core            v1.10.1                          14151d58ac3f        6 months ago        130MB
goharbor/harbor-portal          v1.10.1                          8a9856c37798        6 months ago        52.1MB
goharbor/harbor-db              v1.10.1                          18548720d8ad        6 months ago        148MB
goharbor/prepare                v1.10.1                          897a4d535ced        6 months ago        192MB
[user@control harbor]$ docker push 192.168.56.100/library/docker:centos
The push refers to repository [192.168.56.100/library/docker]
Get http://192.168.56.100/v2/: dial tcp 192.168.56.100:80: connect: connection refused

```

갑자기 거절당한 나의 하버.......





```
vagrant status
vagrant plugin list
vagrant box list
vagrant box add ubuntu/bionic64
vagrant plugin vagrant-hostmanager
vagrant plugin vagrant-disksize
vagrant up
```

```
student@CCCR01:~$ vi vagrantfile
student@CCCR01:~$ ls
 CentOS-7-x86_64-Minimal-2003.iso   Documents          GNS3              install.sh          nano.save     Public      Templates        'win7(ubntu-vb).ova'
 Desktop                            Downloads          hangyeol.tar.gz   일러스트레이터.md   nano.save.1   rtl8821ce   Videos
'Docker Network.md'                 examples.desktop   index.html        Music               Pictures      snap       'VirtualBox VMs'
student@CCCR01:~$ cd Downloads/
student@CCCR01:~/Downloads$ sudo vi Vagrantfile 
student@CCCR01:~/Downloads$ cd ~
student@CCCR01:~$ mkdir vagrant
student@CCCR01:~$ cp ~/Downloads/Vagrantfile ~/vagrant/
student@CCCR01:~$ cd vagrant/
student@CCCR01:~/vagrant$ ls
Vagrantfile
student@CCCR01:~/vagrant$ pwd
/home/student/vagrant
student@CCCR01:~/vagrant$ ls -l
total 4
-rw-r--r-- 1 student student 1992  8월 11 15:51 Vagrantfile
student@CCCR01:~/vagrant$ vagrant status
Current machine states:

kube-master1              not created (virtualbox)
kube-node1                not created (virtualbox)
kube-node2                not created (virtualbox)
kube-node3                not created (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
student@CCCR01:~/vagrant$ vagrant box list
ubuntu/bionic64 (virtualbox, 20200807.0.0)
student@CCCR01:~/vagrant$ vagrant plugin list
No plugins installed.
student@CCCR01:~/vagrant$ vagrant plugin install vagrant-hostmanager
Installing the 'vagrant-hostmanager' plugin. This can take a few minutes...
Fetching vagrant-hostmanager-1.8.9.gem
Installed the plugin 'vagrant-hostmanager (1.8.9)'!
student@CCCR01:~/vagrant$ vagrant plugin install vagrant-disksize
Installing the 'vagrant-disksize' plugin. This can take a few minutes...
y7Fetching vagrant-disksize-0.1.3.gem
Installed the plugin 'vagrant-disksize (0.1.3)'!
student@CCCR01:~/vagrant$ vagrant up
Bringing machine 'kube-master1' up with 'virtualbox' provider...
Bringing machine 'kube-node1' up with 'virtualbox' provider...
Bringing machine 'kube-node2' up with 'virtualbox' provider...
Bringing machine 'kube-node3' up with 'virtualbox' provider...
==> kube-master1: Importing base box 'ubuntu/bionic64'...
==> kube-master1: Matching MAC address for NAT networking...
........


student@CCCR01:~/vagrant$ vagrant status
Current machine states:

kube-master1              running (virtualbox)
kube-node1                running (virtualbox)
kube-node2                running (virtualbox)
kube-node3                running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
student@CCCR01:~/vagrant$ vagrant ssh kube-master1
Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 4.15.0-112-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Aug 11 07:20:55 UTC 2020

  System load:  0.0               Processes:             95
  Usage of /:   3.5% of 29.02GB   Users logged in:       0
  Memory usage: 4%                IP address for enp0s3: 10.0.2.15
  Swap usage:   0%                IP address for enp0s8: 192.168.56.11


0 packages can be updated.
0 updates are security updates.


```

```
student@CCCR01:~/vagrant$ vagrant ssh kube-master1
Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 4.15.0-112-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Aug 11 07:20:55 UTC 2020

  System load:  0.0               Processes:             95
  Usage of /:   3.5% of 29.02GB   Users logged in:       0
  Memory usage: 4%                IP address for enp0s3: 10.0.2.15
  Swap usage:   0%                IP address for enp0s8: 192.168.56.11


0 packages can be updated.
0 updates are security updates.


vagrant@kube-master1:~$ hostname
kube-master1
vagrant@kube-master1:~$ exit
logout
Connection to 127.0.0.1 closed.
student@CCCR01:~/vagrant$ ssh vagrant@192.168.56.11
The authenticity of host '192.168.56.11 (192.168.56.11)' can't be established.
ECDSA key fingerprint is SHA256:sSZMc2yUExKzqtPjZHkUxmynd9x+RgRLjjPDm0o24kM.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.56.11' (ECDSA) to the list of known hosts.
Password: 
Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 4.15.0-112-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Aug 11 07:23:02 UTC 2020

  System load:  0.01              Processes:             98
  Usage of /:   3.5% of 29.02GB   Users logged in:       0
  Memory usage: 4%                IP address for enp0s3: 10.0.2.15
  Swap usage:   0%                IP address for enp0s8: 192.168.56.11


0 packages can be updated.
0 updates are security updates.


Last login: Tue Aug 11 07:20:55 2020 from 10.0.2.2

```

vagrant suspend

vagrant halt

vagrant resume



kubespray 자동화해주는 설정 도구 



```
vagrant@kube-master1:~$ cat /etc/hosts
127.0.0.1	localhost

# The following lines are desirable for IPv6 capable hosts
::1	ip6-localhost	ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
ff02::3	ip6-allhosts
127.0.1.1	kube-master1	kube-master1

## vagrant-hostmanager-start
192.168.56.23	kube-node3

192.168.56.22	kube-node2

192.168.56.11	kube-master1

192.168.56.21	kube-node1

## vagrant-hostmanager-end

vagrant@kube-master1:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/vagrant/.ssh/id_rsa.
Your public key has been saved in /home/vagrant/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:b7zln+O625g7UrjbmblwwXi/rhc2TBMWuJa/A7ztUbk vagrant@kube-master1
The key's randomart image is:
+---[RSA 2048]----+
|             ... |
|            . o  |
|             + . |
|           o+ o .|
|        S .+++ o.|
|         o..+o*..|
|          =oo=oE |
|         .oBoB*+ |
|          oo/#Oo |
+----[SHA256]-----+
vagrant@kube-master1:~$ ssh-copy-id vagrant@kube-node1
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
The authenticity of host 'kube-node1 (192.168.56.21)' can't be established.
ECDSA key fingerprint is SHA256:Z2wpFEt+vpJ9/djxSAYLMDIezHNDb0ImUgA1oufLs7s.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
Password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@kube-node1'"
and check to make sure that only the key(s) you wanted were added.

vagrant@kube-master1:~$ ssh-copy-id vagrant@kube-node2
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
The authenticity of host 'kube-node2 (192.168.56.22)' can't be established.
ECDSA key fingerprint is SHA256:/i77RF10j3Kq5jf4l+PYCjn4bpDq1hBg2Jn8gY2ExiU.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
Password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@kube-node2'"
and check to make sure that only the key(s) you wanted were added.

vagrant@kube-master1:~$ ssh-copy-id vagrant@kube-node3
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
The authenticity of host 'kube-node3 (192.168.56.23)' can't be established.
ECDSA key fingerprint is SHA256:g7x6sEYItl6PqA8CjSwtM2V+3rfiXux1vaDNc3DgI74.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
Password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@kube-node3'"
and check to make sure that only the key(s) you wanted were added.

vagrant@kube-master1:~$ ssh-copy-id vagrant@localhost
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:sSZMc2yUExKzqtPjZHkUxmynd9x+RgRLjjPDm0o24kM.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
Password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@localhost'"
and check to make sure that only the key(s) you wanted were added.

키젠 생성하여 모든 아이디에 키 복사하기위해 로그인해준다. 비번 vagrant
```



```
vagrant@kube-master1:~$ sudo apt install python3 python3-pip git
Reading package lists... Done
Building dependency tree       
Reading state information... Done
E: Unable to locate package python3-pip
vagrant@kube-master1:~$ sudo apt update
Get:1 http://mirror.kakao.com/ubuntu bionic InRelease [242 kB]
Get:2 http://mirror.kakao.com/ubuntu bionic-updates InRelease [88.7 kB]
Get:3 http://mirror.kakao.com/ubuntu bionic-backports InRelease [74.6 kB]
Get:4 http://mirror.kakao.com/ubuntu bionic-security InRelease [88.7 kB]
Get:5 http://mirror.kakao.com/ubuntu bionic/main amd64 Packages [1019 kB]
Get:6 http://mirror.kakao.com/ubuntu bionic/main Translation-en [516 kB]
Get:7 http://mirror.kakao.com/ubuntu bionic/restricted amd64 Packages [9184 B]
Get:8 http://mirror.kakao.com/ubuntu bionic/restricted Translation-en [3584 B]
Get:9 http://mirror.kakao.com/ubuntu bionic/universe amd64 Packages [8570 kB]
Get:10 http://mirror.kakao.com/ubuntu bionic/universe Translation-en [4941 kB]
Get:11 http://mirror.kakao.com/ubuntu bionic/multiverse amd64 Packages [151 kB]
Get:12 http://mirror.kakao.com/ubuntu bionic/multiverse Translation-en [108 kB]
Get:13 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 Packages [1032 kB]
Get:14 http://mirror.kakao.com/ubuntu bionic-updates/main Translation-en [346 kB]
Get:15 http://mirror.kakao.com/ubuntu bionic-updates/restricted amd64 Packages [84.7 kB]
Get:16 http://mirror.kakao.com/ubuntu bionic-updates/restricted Translation-en [18.7 kB]
Get:17 http://mirror.kakao.com/ubuntu bionic-updates/universe amd64 Packages [1097 kB]
Get:18 http://mirror.kakao.com/ubuntu bionic-updates/universe Translation-en [342 kB]
Get:19 http://mirror.kakao.com/ubuntu bionic-updates/multiverse amd64 Packages [19.2 kB]
Get:20 http://mirror.kakao.com/ubuntu bionic-updates/multiverse Translation-en [6712 B]
Get:21 http://mirror.kakao.com/ubuntu bionic-backports/main amd64 Packages [7516 B]
Get:22 http://mirror.kakao.com/ubuntu bionic-backports/main Translation-en [4764 B]
Get:23 http://mirror.kakao.com/ubuntu bionic-backports/universe amd64 Packages [7736 B]
Get:24 http://mirror.kakao.com/ubuntu bionic-backports/universe Translation-en [4588 B]
Get:25 http://mirror.kakao.com/ubuntu bionic-security/main amd64 Packages [808 kB]
Get:26 http://mirror.kakao.com/ubuntu bionic-security/main Translation-en [254 kB]
Get:27 http://mirror.kakao.com/ubuntu bionic-security/restricted amd64 Packages [75.6 kB]
Get:28 http://mirror.kakao.com/ubuntu bionic-security/restricted Translation-en [16.5 kB]
Get:29 http://mirror.kakao.com/ubuntu bionic-security/universe amd64 Packages [689 kB]
Get:30 http://mirror.kakao.com/ubuntu bionic-security/universe Translation-en [228 kB]
Get:31 http://mirror.kakao.com/ubuntu bionic-security/multiverse amd64 Packages [8112 B]
Get:32 http://mirror.kakao.com/ubuntu bionic-security/multiverse Translation-en [2852 B]
Fetched 20.9 MB in 7s (3131 kB/s)                                                                                                                                         
Reading package lists... Done
Building dependency tree       
Reading state information... Done
All packages are up to date.
vagrant@kube-master1:~$ sudo apt install python3 python3-pip git
Reading package lists... Done
Building dependency tree       
Reading state information... Done
git is already the newest version (1:2.17.1-1ubuntu0.7).
git set to manually installed.
python3 is already the newest version (3.6.7-1~18.04).
python3 set to manually installed.
The following additional packages will be installed:
  binutils binutils-common binutils-x86-64-linux-gnu build-essential cpp cpp-7 dh-python dpkg-dev fakeroot g++ g++-7 gcc gcc-7 gcc-7-base libalgorithm-diff-perl
  libalgorithm-diff-xs-perl libalgorithm-merge-perl libasan4 libatomic1 libbinutils libc-dev-bin libc6-dev libcc1-0 libcilkrts5 libdpkg-perl libexpat1-dev libfakeroot
  libfile-fcntllock-perl libgcc-7-dev libgomp1 libisl19 libitm1 liblsan0 libmpc3 libmpx2 libpython3-dev libpython3.6-dev libquadmath0 libstdc++-7-dev libtsan0 libubsan0
  linux-libc-dev make manpages-dev python-pip-whl python3-crypto python3-dev python3-distutils python3-keyring python3-keyrings.alt python3-lib2to3 python3-secretstorage
  python3-setuptools python3-wheel python3-xdg python3.6-dev
Suggested packages:
  binutils-doc cpp-doc gcc-7-locales debian-keyring g++-multilib g++-7-multilib gcc-7-doc libstdc++6-7-dbg gcc-multilib autoconf automake libtool flex bison gdb gcc-doc
  gcc-7-multilib libgcc1-dbg libgomp1-dbg libitm1-dbg libatomic1-dbg libasan4-dbg liblsan0-dbg libtsan0-dbg libubsan0-dbg libcilkrts5-dbg libmpx2-dbg libquadmath0-dbg
  glibc-doc bzr libstdc++-7-doc make-doc python-crypto-doc gnome-keyring libkf5wallet-bin gir1.2-gnomekeyring-1.0 python-secretstorage-doc python-setuptools-doc
The following NEW packages will be installed:
  binutils binutils-common binutils-x86-64-linux-gnu build-essential cpp cpp-7 dh-python dpkg-dev fakeroot g++ g++-7 gcc gcc-7 gcc-7-base libalgorithm-diff-perl
  libalgorithm-diff-xs-perl libalgorithm-merge-perl libasan4 libatomic1 libbinutils libc-dev-bin libc6-dev libcc1-0 libcilkrts5 libdpkg-perl libexpat1-dev libfakeroot
  libfile-fcntllock-perl libgcc-7-dev libgomp1 libisl19 libitm1 liblsan0 libmpc3 libmpx2 libpython3-dev libpython3.6-dev libquadmath0 libstdc++-7-dev libtsan0 libubsan0
  linux-libc-dev make manpages-dev python-pip-whl python3-crypto python3-dev python3-distutils python3-keyring python3-keyrings.alt python3-lib2to3 python3-pip
  python3-secretstorage python3-setuptools python3-wheel python3-xdg python3.6-dev
0 upgraded, 57 newly installed, 0 to remove and 0 not upgraded.
Need to get 91.2 MB of archives.
After this operation, 252 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 binutils-common amd64 2.30-21ubuntu1~18.04.4 [196 kB]
Get:2 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libbinutils amd64 2.30-21ubuntu1~18.04.4 [488 kB]
Get:3 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 binutils-x86-64-linux-gnu amd64 2.30-21ubuntu1~18.04.4 [1839 kB]
Get:4 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 binutils amd64 2.30-21ubuntu1~18.04.4 [3392 B]
Get:5 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libc-dev-bin amd64 2.27-3ubuntu1.2 [71.8 kB]
Get:6 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 linux-libc-dev amd64 4.15.0-112.113 [982 kB]
Get:7 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libc6-dev amd64 2.27-3ubuntu1.2 [2585 kB]
Get:8 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 gcc-7-base amd64 7.5.0-3ubuntu1~18.04 [18.3 kB]
Get:9 http://mirror.kakao.com/ubuntu bionic/main amd64 libisl19 amd64 0.19-1 [551 kB]
Get:10 http://mirror.kakao.com/ubuntu bionic/main amd64 libmpc3 amd64 1.1.0-1 [40.8 kB]
Get:11 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 cpp-7 amd64 7.5.0-3ubuntu1~18.04 [8591 kB]
Get:12 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 cpp amd64 4:7.4.0-1ubuntu2.3 [27.7 kB]
Get:13 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libcc1-0 amd64 8.4.0-1ubuntu1~18.04 [39.4 kB]
Get:14 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libgomp1 amd64 8.4.0-1ubuntu1~18.04 [76.5 kB]
Get:15 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libitm1 amd64 8.4.0-1ubuntu1~18.04 [27.9 kB]
Get:16 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libatomic1 amd64 8.4.0-1ubuntu1~18.04 [9192 B]
Get:17 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libasan4 amd64 7.5.0-3ubuntu1~18.04 [358 kB]
Get:18 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 liblsan0 amd64 8.4.0-1ubuntu1~18.04 [133 kB]
Get:19 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libtsan0 amd64 8.4.0-1ubuntu1~18.04 [288 kB]
Get:20 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libubsan0 amd64 7.5.0-3ubuntu1~18.04 [126 kB]
Get:21 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libcilkrts5 amd64 7.5.0-3ubuntu1~18.04 [42.5 kB]
Get:22 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libmpx2 amd64 8.4.0-1ubuntu1~18.04 [11.6 kB]
Get:23 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libquadmath0 amd64 8.4.0-1ubuntu1~18.04 [134 kB]
Get:24 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libgcc-7-dev amd64 7.5.0-3ubuntu1~18.04 [2378 kB]
Get:25 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 gcc-7 amd64 7.5.0-3ubuntu1~18.04 [9381 kB]
Get:26 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 gcc amd64 4:7.4.0-1ubuntu2.3 [5184 B]                                                                     
Get:27 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libstdc++-7-dev amd64 7.5.0-3ubuntu1~18.04 [1471 kB]                                                      
Get:28 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 g++-7 amd64 7.5.0-3ubuntu1~18.04 [9697 kB]                                                                
Get:29 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 g++ amd64 4:7.4.0-1ubuntu2.3 [1568 B]                                                                     
Get:30 http://mirror.kakao.com/ubuntu bionic/main amd64 make amd64 4.1-9.1ubuntu1 [154 kB]                                                                                
Get:31 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libdpkg-perl all 1.19.0.5ubuntu2.3 [211 kB]                                                               
Get:32 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 dpkg-dev all 1.19.0.5ubuntu2.3 [607 kB]                                                                   
Get:33 http://mirror.kakao.com/ubuntu bionic/main amd64 build-essential amd64 12.4ubuntu1 [4758 B]                                                                        
Get:34 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 python3-lib2to3 all 3.6.9-1~18.04 [77.4 kB]                                                               
Get:35 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 python3-distutils all 3.6.9-1~18.04 [144 kB]                                                              
Get:36 http://mirror.kakao.com/ubuntu bionic/main amd64 dh-python all 3.20180325ubuntu2 [89.2 kB]                                                                         
Get:37 http://mirror.kakao.com/ubuntu bionic/main amd64 libfakeroot amd64 1.22-2ubuntu1 [25.9 kB]                                                                         
Get:38 http://mirror.kakao.com/ubuntu bionic/main amd64 fakeroot amd64 1.22-2ubuntu1 [62.3 kB]                                                                            
Get:39 http://mirror.kakao.com/ubuntu bionic/main amd64 libalgorithm-diff-perl all 1.19.03-1 [47.6 kB]                                                                    
Get:40 http://mirror.kakao.com/ubuntu bionic/main amd64 libalgorithm-diff-xs-perl amd64 0.04-5 [11.1 kB]                                                                  
Get:41 http://mirror.kakao.com/ubuntu bionic/main amd64 libalgorithm-merge-perl all 0.08-3 [12.0 kB]                                                                      
Get:42 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libexpat1-dev amd64 2.2.5-3ubuntu0.2 [122 kB]                                                             
Get:43 http://mirror.kakao.com/ubuntu bionic/main amd64 libfile-fcntllock-perl amd64 0.22-3build2 [33.2 kB]                                                               
Get:44 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libpython3.6-dev amd64 3.6.9-1~18.04ubuntu1.1 [44.9 MB]                                                   
Get:45 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 libpython3-dev amd64 3.6.7-1~18.04 [7328 B]                                                               
Get:46 http://mirror.kakao.com/ubuntu bionic/main amd64 manpages-dev all 4.15-1 [2217 kB]                                                                                 
Get:47 http://mirror.kakao.com/ubuntu bionic-updates/universe amd64 python-pip-whl all 9.0.1-2.3~ubuntu1.18.04.1 [1653 kB]                                                
Get:48 http://mirror.kakao.com/ubuntu bionic/main amd64 python3-crypto amd64 2.6.1-8ubuntu2 [244 kB]                                                                      
Get:49 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 python3.6-dev amd64 3.6.9-1~18.04ubuntu1.1 [508 kB]                                                       
Get:50 http://mirror.kakao.com/ubuntu bionic-updates/main amd64 python3-dev amd64 3.6.7-1~18.04 [1288 B]                                                                  
Get:51 http://mirror.kakao.com/ubuntu bionic/main amd64 python3-secretstorage all 2.3.1-2 [12.1 kB]                                                                       
Get:52 http://mirror.kakao.com/ubuntu bionic/main amd64 python3-keyring all 10.6.0-1 [26.7 kB]                                                                            
Get:53 http://mirror.kakao.com/ubuntu bionic/main amd64 python3-keyrings.alt all 3.0-1 [16.6 kB]                                                                          
Get:54 http://mirror.kakao.com/ubuntu bionic-updates/universe amd64 python3-pip all 9.0.1-2.3~ubuntu1.18.04.1 [114 kB]                                                    
Get:55 http://mirror.kakao.com/ubuntu bionic/main amd64 python3-setuptools all 39.0.1-2 [248 kB]                                                                          
Get:56 http://mirror.kakao.com/ubuntu bionic/universe amd64 python3-wheel all 0.30.0-0.2 [36.5 kB]                                                                        
Get:57 http://mirror.kakao.com/ubuntu bionic/main amd64 python3-xdg all 0.25-4ubuntu1 [31.4 kB]                                                                           
Fetched 91.2 MB in 16s (5671 kB/s)                                                                                                                                        
Extracting templates from packages: 100%
Selecting previously unselected package binutils-common:amd64.
(Reading database ... 59798 files and directories currently installed.)
Preparing to unpack .../00-binutils-common_2.30-21ubuntu1~18.04.4_amd64.deb ...
Unpacking binutils-common:amd64 (2.30-21ubuntu1~18.04.4) ...
Selecting previously unselected package libbinutils:amd64.
Preparing to unpack .../01-libbinutils_2.30-21ubuntu1~18.04.4_amd64.deb ...
Unpacking libbinutils:amd64 (2.30-21ubuntu1~18.04.4) ...
Selecting previously unselected package binutils-x86-64-linux-gnu.
Preparing to unpack .../02-binutils-x86-64-linux-gnu_2.30-21ubuntu1~18.04.4_amd64.deb ...
Unpacking binutils-x86-64-linux-gnu (2.30-21ubuntu1~18.04.4) ...
Selecting previously unselected package binutils.
Preparing to unpack .../03-binutils_2.30-21ubuntu1~18.04.4_amd64.deb ...
Unpacking binutils (2.30-21ubuntu1~18.04.4) ...
Selecting previously unselected package libc-dev-bin.
Preparing to unpack .../04-libc-dev-bin_2.27-3ubuntu1.2_amd64.deb ...
Unpacking libc-dev-bin (2.27-3ubuntu1.2) ...
Selecting previously unselected package linux-libc-dev:amd64.
Preparing to unpack .../05-linux-libc-dev_4.15.0-112.113_amd64.deb ...
Unpacking linux-libc-dev:amd64 (4.15.0-112.113) ...
Selecting previously unselected package libc6-dev:amd64.
Preparing to unpack .../06-libc6-dev_2.27-3ubuntu1.2_amd64.deb ...
Unpacking libc6-dev:amd64 (2.27-3ubuntu1.2) ...
Selecting previously unselected package gcc-7-base:amd64.
Preparing to unpack .../07-gcc-7-base_7.5.0-3ubuntu1~18.04_amd64.deb ...
Unpacking gcc-7-base:amd64 (7.5.0-3ubuntu1~18.04) ...
Selecting previously unselected package libisl19:amd64.
Preparing to unpack .../08-libisl19_0.19-1_amd64.deb ...
Unpacking libisl19:amd64 (0.19-1) ...
Selecting previously unselected package libmpc3:amd64.
Preparing to unpack .../09-libmpc3_1.1.0-1_amd64.deb ...
Unpacking libmpc3:amd64 (1.1.0-1) ...
Selecting previously unselected package cpp-7.
Preparing to unpack .../10-cpp-7_7.5.0-3ubuntu1~18.04_amd64.deb ...
Unpacking cpp-7 (7.5.0-3ubuntu1~18.04) ...
Selecting previously unselected package cpp.
Preparing to unpack .../11-cpp_4%3a7.4.0-1ubuntu2.3_amd64.deb ...
Unpacking cpp (4:7.4.0-1ubuntu2.3) ...
Selecting previously unselected package libcc1-0:amd64.
Preparing to unpack .../12-libcc1-0_8.4.0-1ubuntu1~18.04_amd64.deb ...
Unpacking libcc1-0:amd64 (8.4.0-1ubuntu1~18.04) ...
Selecting previously unselected package libgomp1:amd64.
Preparing to unpack .../13-libgomp1_8.4.0-1ubuntu1~18.04_amd64.deb ...
Unpacking libgomp1:amd64 (8.4.0-1ubuntu1~18.04) ...
Selecting previously unselected package libitm1:amd64.
Preparing to unpack .../14-libitm1_8.4.0-1ubuntu1~18.04_amd64.deb ...
Unpacking libitm1:amd64 (8.4.0-1ubuntu1~18.04) ...
Selecting previously unselected package libatomic1:amd64.
Preparing to unpack .../15-libatomic1_8.4.0-1ubuntu1~18.04_amd64.deb ...
Unpacking libatomic1:amd64 (8.4.0-1ubuntu1~18.04) ...
Selecting previously unselected package libasan4:amd64.
Preparing to unpack .../16-libasan4_7.5.0-3ubuntu1~18.04_amd64.deb ...
Unpacking libasan4:amd64 (7.5.0-3ubuntu1~18.04) ...
Selecting previously unselected package liblsan0:amd64.
Preparing to unpack .../17-liblsan0_8.4.0-1ubuntu1~18.04_amd64.deb ...
Unpacking liblsan0:amd64 (8.4.0-1ubuntu1~18.04) ...
Selecting previously unselected package libtsan0:amd64.
Preparing to unpack .../18-libtsan0_8.4.0-1ubuntu1~18.04_amd64.deb ...
Unpacking libtsan0:amd64 (8.4.0-1ubuntu1~18.04) ...
Selecting previously unselected package libubsan0:amd64.
Preparing to unpack .../19-libubsan0_7.5.0-3ubuntu1~18.04_amd64.deb ...
Unpacking libubsan0:amd64 (7.5.0-3ubuntu1~18.04) ...
Selecting previously unselected package libcilkrts5:amd64.
Preparing to unpack .../20-libcilkrts5_7.5.0-3ubuntu1~18.04_amd64.deb ...
Unpacking libcilkrts5:amd64 (7.5.0-3ubuntu1~18.04) ...
Selecting previously unselected package libmpx2:amd64.
Preparing to unpack .../21-libmpx2_8.4.0-1ubuntu1~18.04_amd64.deb ...
Unpacking libmpx2:amd64 (8.4.0-1ubuntu1~18.04) ...
Selecting previously unselected package libquadmath0:amd64.
Preparing to unpack .../22-libquadmath0_8.4.0-1ubuntu1~18.04_amd64.deb ...
Unpacking libquadmath0:amd64 (8.4.0-1ubuntu1~18.04) ...
Selecting previously unselected package libgcc-7-dev:amd64.
Preparing to unpack .../23-libgcc-7-dev_7.5.0-3ubuntu1~18.04_amd64.deb ...
Unpacking libgcc-7-dev:amd64 (7.5.0-3ubuntu1~18.04) ...
Selecting previously unselected package gcc-7.
Preparing to unpack .../24-gcc-7_7.5.0-3ubuntu1~18.04_amd64.deb ...
Unpacking gcc-7 (7.5.0-3ubuntu1~18.04) ...
Selecting previously unselected package gcc.
Preparing to unpack .../25-gcc_4%3a7.4.0-1ubuntu2.3_amd64.deb ...
Unpacking gcc (4:7.4.0-1ubuntu2.3) ...
Selecting previously unselected package libstdc++-7-dev:amd64.
Preparing to unpack .../26-libstdc++-7-dev_7.5.0-3ubuntu1~18.04_amd64.deb ...
Unpacking libstdc++-7-dev:amd64 (7.5.0-3ubuntu1~18.04) ...
Selecting previously unselected package g++-7.
Preparing to unpack .../27-g++-7_7.5.0-3ubuntu1~18.04_amd64.deb ...
Unpacking g++-7 (7.5.0-3ubuntu1~18.04) ...
Selecting previously unselected package g++.
Preparing to unpack .../28-g++_4%3a7.4.0-1ubuntu2.3_amd64.deb ...
Unpacking g++ (4:7.4.0-1ubuntu2.3) ...
Selecting previously unselected package make.
Preparing to unpack .../29-make_4.1-9.1ubuntu1_amd64.deb ...
Unpacking make (4.1-9.1ubuntu1) ...
Selecting previously unselected package libdpkg-perl.
Preparing to unpack .../30-libdpkg-perl_1.19.0.5ubuntu2.3_all.deb ...
Unpacking libdpkg-perl (1.19.0.5ubuntu2.3) ...
Selecting previously unselected package dpkg-dev.
Preparing to unpack .../31-dpkg-dev_1.19.0.5ubuntu2.3_all.deb ...
Unpacking dpkg-dev (1.19.0.5ubuntu2.3) ...
Selecting previously unselected package build-essential.
Preparing to unpack .../32-build-essential_12.4ubuntu1_amd64.deb ...
Unpacking build-essential (12.4ubuntu1) ...
Selecting previously unselected package python3-lib2to3.
Preparing to unpack .../33-python3-lib2to3_3.6.9-1~18.04_all.deb ...
Unpacking python3-lib2to3 (3.6.9-1~18.04) ...
Selecting previously unselected package python3-distutils.
Preparing to unpack .../34-python3-distutils_3.6.9-1~18.04_all.deb ...
Unpacking python3-distutils (3.6.9-1~18.04) ...
Selecting previously unselected package dh-python.
Preparing to unpack .../35-dh-python_3.20180325ubuntu2_all.deb ...
Unpacking dh-python (3.20180325ubuntu2) ...
Selecting previously unselected package libfakeroot:amd64.
Preparing to unpack .../36-libfakeroot_1.22-2ubuntu1_amd64.deb ...
Unpacking libfakeroot:amd64 (1.22-2ubuntu1) ...
Selecting previously unselected package fakeroot.
Preparing to unpack .../37-fakeroot_1.22-2ubuntu1_amd64.deb ...
Unpacking fakeroot (1.22-2ubuntu1) ...
Selecting previously unselected package libalgorithm-diff-perl.
Preparing to unpack .../38-libalgorithm-diff-perl_1.19.03-1_all.deb ...
Unpacking libalgorithm-diff-perl (1.19.03-1) ...
Selecting previously unselected package libalgorithm-diff-xs-perl.
Preparing to unpack .../39-libalgorithm-diff-xs-perl_0.04-5_amd64.deb ...
Unpacking libalgorithm-diff-xs-perl (0.04-5) ...
Selecting previously unselected package libalgorithm-merge-perl.
Preparing to unpack .../40-libalgorithm-merge-perl_0.08-3_all.deb ...
Unpacking libalgorithm-merge-perl (0.08-3) ...
Selecting previously unselected package libexpat1-dev:amd64.
Preparing to unpack .../41-libexpat1-dev_2.2.5-3ubuntu0.2_amd64.deb ...
Unpacking libexpat1-dev:amd64 (2.2.5-3ubuntu0.2) ...
Selecting previously unselected package libfile-fcntllock-perl.
Preparing to unpack .../42-libfile-fcntllock-perl_0.22-3build2_amd64.deb ...
Unpacking libfile-fcntllock-perl (0.22-3build2) ...
Selecting previously unselected package libpython3.6-dev:amd64.
Preparing to unpack .../43-libpython3.6-dev_3.6.9-1~18.04ubuntu1.1_amd64.deb ...
Unpacking libpython3.6-dev:amd64 (3.6.9-1~18.04ubuntu1.1) ...
Selecting previously unselected package libpython3-dev:amd64.
Preparing to unpack .../44-libpython3-dev_3.6.7-1~18.04_amd64.deb ...
Unpacking libpython3-dev:amd64 (3.6.7-1~18.04) ...
Selecting previously unselected package manpages-dev.
Preparing to unpack .../45-manpages-dev_4.15-1_all.deb ...
Unpacking manpages-dev (4.15-1) ...
Selecting previously unselected package python-pip-whl.
Preparing to unpack .../46-python-pip-whl_9.0.1-2.3~ubuntu1.18.04.1_all.deb ...
Unpacking python-pip-whl (9.0.1-2.3~ubuntu1.18.04.1) ...
Selecting previously unselected package python3-crypto.
Preparing to unpack .../47-python3-crypto_2.6.1-8ubuntu2_amd64.deb ...
Unpacking python3-crypto (2.6.1-8ubuntu2) ...
Selecting previously unselected package python3.6-dev.
Preparing to unpack .../48-python3.6-dev_3.6.9-1~18.04ubuntu1.1_amd64.deb ...
Unpacking python3.6-dev (3.6.9-1~18.04ubuntu1.1) ...
Selecting previously unselected package python3-dev.
Preparing to unpack .../49-python3-dev_3.6.7-1~18.04_amd64.deb ...
Unpacking python3-dev (3.6.7-1~18.04) ...
Selecting previously unselected package python3-secretstorage.
Preparing to unpack .../50-python3-secretstorage_2.3.1-2_all.deb ...
Unpacking python3-secretstorage (2.3.1-2) ...
Selecting previously unselected package python3-keyring.
Preparing to unpack .../51-python3-keyring_10.6.0-1_all.deb ...
Unpacking python3-keyring (10.6.0-1) ...
Selecting previously unselected package python3-keyrings.alt.
Preparing to unpack .../52-python3-keyrings.alt_3.0-1_all.deb ...
Unpacking python3-keyrings.alt (3.0-1) ...
Selecting previously unselected package python3-pip.
Preparing to unpack .../53-python3-pip_9.0.1-2.3~ubuntu1.18.04.1_all.deb ...
Unpacking python3-pip (9.0.1-2.3~ubuntu1.18.04.1) ...
Selecting previously unselected package python3-setuptools.
Preparing to unpack .../54-python3-setuptools_39.0.1-2_all.deb ...
Unpacking python3-setuptools (39.0.1-2) ...
Selecting previously unselected package python3-wheel.
Preparing to unpack .../55-python3-wheel_0.30.0-0.2_all.deb ...
Unpacking python3-wheel (0.30.0-0.2) ...
Selecting previously unselected package python3-xdg.
Preparing to unpack .../56-python3-xdg_0.25-4ubuntu1_all.deb ...
Unpacking python3-xdg (0.25-4ubuntu1) ...
Setting up libquadmath0:amd64 (8.4.0-1ubuntu1~18.04) ...
Setting up libgomp1:amd64 (8.4.0-1ubuntu1~18.04) ...
Setting up libatomic1:amd64 (8.4.0-1ubuntu1~18.04) ...
Setting up python-pip-whl (9.0.1-2.3~ubuntu1.18.04.1) ...
Setting up libcc1-0:amd64 (8.4.0-1ubuntu1~18.04) ...
Setting up make (4.1-9.1ubuntu1) ...
Setting up python3-crypto (2.6.1-8ubuntu2) ...
Setting up libtsan0:amd64 (8.4.0-1ubuntu1~18.04) ...
Setting up python3-xdg (0.25-4ubuntu1) ...
Setting up python3-keyrings.alt (3.0-1) ...
Setting up linux-libc-dev:amd64 (4.15.0-112.113) ...
Setting up libdpkg-perl (1.19.0.5ubuntu2.3) ...
Setting up python3-wheel (0.30.0-0.2) ...
Setting up liblsan0:amd64 (8.4.0-1ubuntu1~18.04) ...
Setting up gcc-7-base:amd64 (7.5.0-3ubuntu1~18.04) ...
Setting up binutils-common:amd64 (2.30-21ubuntu1~18.04.4) ...
Setting up libfile-fcntllock-perl (0.22-3build2) ...
Setting up libmpx2:amd64 (8.4.0-1ubuntu1~18.04) ...
Setting up libfakeroot:amd64 (1.22-2ubuntu1) ...
Setting up libalgorithm-diff-perl (1.19.03-1) ...
Setting up libmpc3:amd64 (1.1.0-1) ...
Setting up libc-dev-bin (2.27-3ubuntu1.2) ...
Setting up python3-lib2to3 (3.6.9-1~18.04) ...
Setting up python3-secretstorage (2.3.1-2) ...
Setting up manpages-dev (4.15-1) ...
Setting up libc6-dev:amd64 (2.27-3ubuntu1.2) ...
Setting up python3-distutils (3.6.9-1~18.04) ...
Setting up libitm1:amd64 (8.4.0-1ubuntu1~18.04) ...
Setting up libisl19:amd64 (0.19-1) ...
Setting up libasan4:amd64 (7.5.0-3ubuntu1~18.04) ...
Setting up python3-keyring (10.6.0-1) ...
Setting up libbinutils:amd64 (2.30-21ubuntu1~18.04.4) ...
Setting up libcilkrts5:amd64 (7.5.0-3ubuntu1~18.04) ...
Setting up libubsan0:amd64 (7.5.0-3ubuntu1~18.04) ...
Setting up fakeroot (1.22-2ubuntu1) ...
update-alternatives: using /usr/bin/fakeroot-sysv to provide /usr/bin/fakeroot (fakeroot) in auto mode
Setting up libgcc-7-dev:amd64 (7.5.0-3ubuntu1~18.04) ...
Setting up cpp-7 (7.5.0-3ubuntu1~18.04) ...
Setting up libstdc++-7-dev:amd64 (7.5.0-3ubuntu1~18.04) ...
Setting up libalgorithm-merge-perl (0.08-3) ...
Setting up libalgorithm-diff-xs-perl (0.04-5) ...
Setting up python3-pip (9.0.1-2.3~ubuntu1.18.04.1) ...
Setting up libexpat1-dev:amd64 (2.2.5-3ubuntu0.2) ...
Setting up python3-setuptools (39.0.1-2) ...
Setting up dh-python (3.20180325ubuntu2) ...
Setting up binutils-x86-64-linux-gnu (2.30-21ubuntu1~18.04.4) ...
Setting up cpp (4:7.4.0-1ubuntu2.3) ...
Setting up libpython3.6-dev:amd64 (3.6.9-1~18.04ubuntu1.1) ...
Setting up binutils (2.30-21ubuntu1~18.04.4) ...
Setting up python3.6-dev (3.6.9-1~18.04ubuntu1.1) ...
Setting up libpython3-dev:amd64 (3.6.7-1~18.04) ...
Setting up gcc-7 (7.5.0-3ubuntu1~18.04) ...
Setting up g++-7 (7.5.0-3ubuntu1~18.04) ...
Setting up python3-dev (3.6.7-1~18.04) ...
Setting up gcc (4:7.4.0-1ubuntu2.3) ...
Setting up dpkg-dev (1.19.0.5ubuntu2.3) ...
Setting up g++ (4:7.4.0-1ubuntu2.3) ...
update-alternatives: using /usr/bin/g++ to provide /usr/bin/c++ (c++) in auto mode
Setting up build-essential (12.4ubuntu1) ...
Processing triggers for man-db (2.8.3-2ubuntu0.1) ...
Processing triggers for libc-bin (2.27-3ubuntu1.2) ...
vagrant@kube-master1:~$ git clone --single-branch v2.12.3 https://github.com/kubernetes-sigs/kubespray.git
fatal: repository 'v2.12.3' does not exist
vagrant@kube-master1:~$ git clone --single-branch --branch v2.12.3 https://github.com/kubernetes-sigs/kubespray.git
Cloning into 'kubespray'...
remote: Enumerating objects: 2, done.
remote: Counting objects: 100% (2/2), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 39203 (delta 0), reused 2 (delta 0), pack-reused 39201
Receiving objects: 100% (39203/39203), 11.60 MiB | 4.23 MiB/s, done.
Resolving deltas: 100% (21572/21572), done.
Note: checking out 'e1815303339da8c6241f7e61ae0b61a7e8912849'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

vagrant@kube-master1:~$ ls
kubespray
vagrant@kube-master1:~$ ls -l
total 4
drwxrwxr-x 15 vagrant vagrant 4096 Aug 11 07:35 kubespray
vagrant@kube-master1:~$ cd kubespray/
vagrant@kube-master1:~/kubespray$ ls
CNAME            Makefile        RELEASE.md         ansible.cfg         docs             library                    remove-node.yml   scale.yml  test-infra
CONTRIBUTING.md  OWNERS          SECURITY_CONTACTS  cluster.yml         extra_playbooks  logo                       requirements.txt  scripts    tests
Dockerfile       OWNERS_ALIASES  Vagrantfile        code-of-conduct.md  index.html       mitogen.yaml               reset.yml         setup.cfg  upgrade-cluster.yml
LICENSE          README.md       _config.yml        contrib             inventory        recover-control-plane.yml  roles             setup.py
vagrant@kube-master1:~/kubespray$ sudo pip3 install -y requirements.txt 

Usage:   
  pip install [options] <requirement specifier> [package-index-options] ...
  pip install [options] -r <requirements file> [package-index-options] ...
  pip install [options] [-e] <vcs project url> ...
  pip install [options] [-e] <local project path> ...
  pip install [options] <archive url/path> ...

no such option: -y
vagrant@kube-master1:~/kubespray$ sudo pip3 install requirements.txt 
The directory '/home/vagrant/.cache/pip/http' or its parent directory is not owned by the current user and the cache has been disabled. Please check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
The directory '/home/vagrant/.cache/pip' or its parent directory is not owned by the current user and caching wheels has been disabled. check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
Collecting requirements.txt
Exception:
Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/pip/basecommand.py", line 215, in main
    status = self.run(options, args)
  File "/usr/lib/python3/dist-packages/pip/commands/install.py", line 342, in run
    requirement_set.prepare_files(finder)
  File "/usr/lib/python3/dist-packages/pip/req/req_set.py", line 380, in prepare_files
    ignore_dependencies=self.ignore_dependencies))
  File "/usr/lib/python3/dist-packages/pip/req/req_set.py", line 554, in _prepare_file
    require_hashes
  File "/usr/lib/python3/dist-packages/pip/req/req_install.py", line 278, in populate_link
    self.link = finder.find_requirement(self, upgrade)
  File "/usr/lib/python3/dist-packages/pip/index.py", line 465, in find_requirement
    all_candidates = self.find_all_candidates(req.name)
  File "/usr/lib/python3/dist-packages/pip/index.py", line 423, in find_all_candidates
    for page in self._get_pages(url_locations, project_name):
  File "/usr/lib/python3/dist-packages/pip/index.py", line 568, in _get_pages
    page = self._get_page(location)
  File "/usr/lib/python3/dist-packages/pip/index.py", line 683, in _get_page
    return HTMLPage.get_page(link, session=self.session)
  File "/usr/lib/python3/dist-packages/pip/index.py", line 795, in get_page
    resp.raise_for_status()
  File "/usr/share/python-wheels/requests-2.18.4-py2.py3-none-any.whl/requests/models.py", line 935, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 404 Client Error: Not Found for url: https://pypi.org/simple/requirements-txt/
vagrant@kube-master1:~/kubespray$ sudo pip3 install -r requirements.txt 
The directory '/home/vagrant/.cache/pip/http' or its parent directory is not owned by the current user and the cache has been disabled. Please check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
The directory '/home/vagrant/.cache/pip' or its parent directory is not owned by the current user and caching wheels has been disabled. check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
Collecting ansible==2.7.12 (from -r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/0a/ad/7c05268f24c9a9234a6a10c6b96271ac7b71fcac3e6ab6955dcc7761bcc3/ansible-2.7.12.tar.gz (11.9MB)
    100% |████████████████████████████████| 11.9MB 156kB/s 
Collecting jinja2==2.10.1 (from -r requirements.txt (line 2))
  Downloading https://files.pythonhosted.org/packages/1d/e7/fd8b501e7a6dfe492a433deb7b9d833d39ca74916fa8bc63dd1a4947a671/Jinja2-2.10.1-py2.py3-none-any.whl (124kB)
    100% |████████████████████████████████| 133kB 152kB/s 
Collecting netaddr==0.7.19 (from -r requirements.txt (line 3))
  Downloading https://files.pythonhosted.org/packages/ba/97/ce14451a9fd7bdb5a397abf99b24a1a6bb7a1a440b019bebd2e9a0dbec74/netaddr-0.7.19-py2.py3-none-any.whl (1.6MB)
    100% |████████████████████████████████| 1.6MB 1.1MB/s 
Collecting pbr==5.2.0 (from -r requirements.txt (line 4))
  Downloading https://files.pythonhosted.org/packages/07/3e/22d1d35a4b51706ca3590c54359aeb5fa7ea60df46180143a3ea13d45f29/pbr-5.2.0-py2.py3-none-any.whl (107kB)
    100% |████████████████████████████████| 112kB 8.4MB/s 
Collecting hvac==0.8.2 (from -r requirements.txt (line 5))
  Downloading https://files.pythonhosted.org/packages/97/96/ee2d10b985bb756cbcc8f177bb4eb5cb780a749fb15cff443f9a33751de5/hvac-0.8.2-py2.py3-none-any.whl (91kB)
    100% |████████████████████████████████| 92kB 6.1MB/s 
Collecting jmespath==0.9.4 (from -r requirements.txt (line 6))
  Downloading https://files.pythonhosted.org/packages/83/94/7179c3832a6d45b266ddb2aac329e101367fbdb11f425f13771d27f225bb/jmespath-0.9.4-py2.py3-none-any.whl
Collecting ruamel.yaml==0.15.96 (from -r requirements.txt (line 7))
  Downloading https://files.pythonhosted.org/packages/7e/a7/c1af60c298a8b7142a67302426f72b535448b2e47de378c87dfcf780914b/ruamel.yaml-0.15.96-cp36-cp36m-manylinux1_x86_64.whl (652kB)
    100% |████████████████████████████████| 655kB 2.3MB/s 
Requirement already satisfied: PyYAML in /usr/lib/python3/dist-packages (from ansible==2.7.12->-r requirements.txt (line 1))
Requirement already satisfied: cryptography in /usr/lib/python3/dist-packages (from ansible==2.7.12->-r requirements.txt (line 1))
Collecting paramiko (from ansible==2.7.12->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/06/1e/1e08baaaf6c3d3df1459fd85f0e7d2d6aa916f33958f151ee1ecc9800971/paramiko-2.7.1-py2.py3-none-any.whl (206kB)
    100% |████████████████████████████████| 215kB 6.4MB/s 
Requirement already satisfied: setuptools in /usr/lib/python3/dist-packages (from ansible==2.7.12->-r requirements.txt (line 1))
Requirement already satisfied: MarkupSafe>=0.23 in /usr/lib/python3/dist-packages (from jinja2==2.10.1->-r requirements.txt (line 2))
Collecting requests>=2.21.0 (from hvac==0.8.2->-r requirements.txt (line 5))
  Downloading https://files.pythonhosted.org/packages/45/1e/0c169c6a5381e241ba7404532c16a21d86ab872c9bed8bdcd4c423954103/requests-2.24.0-py2.py3-none-any.whl (61kB)
    100% |████████████████████████████████| 71kB 6.9MB/s 
Collecting bcrypt>=3.1.3 (from paramiko->ansible==2.7.12->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/8b/1d/82826443777dd4a624e38a08957b975e75df859b381ae302cfd7a30783ed/bcrypt-3.1.7-cp34-abi3-manylinux1_x86_64.whl (56kB)
    100% |████████████████████████████████| 61kB 4.9MB/s 
Collecting pynacl>=1.0.1 (from paramiko->ansible==2.7.12->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/9d/57/2f5e6226a674b2bcb6db531e8b383079b678df5b10cdaa610d6cf20d77ba/PyNaCl-1.4.0-cp35-abi3-manylinux1_x86_64.whl (961kB)
    100% |████████████████████████████████| 962kB 1.5MB/s 
Requirement already satisfied: urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1 in /usr/lib/python3/dist-packages (from requests>=2.21.0->hvac==0.8.2->-r requirements.txt (line 5))
Requirement already satisfied: idna<3,>=2.5 in /usr/lib/python3/dist-packages (from requests>=2.21.0->hvac==0.8.2->-r requirements.txt (line 5))
Requirement already satisfied: chardet<4,>=3.0.2 in /usr/lib/python3/dist-packages (from requests>=2.21.0->hvac==0.8.2->-r requirements.txt (line 5))
Requirement already satisfied: certifi>=2017.4.17 in /usr/lib/python3/dist-packages (from requests>=2.21.0->hvac==0.8.2->-r requirements.txt (line 5))
Requirement already satisfied: six>=1.4.1 in /usr/lib/python3/dist-packages (from bcrypt>=3.1.3->paramiko->ansible==2.7.12->-r requirements.txt (line 1))
Collecting cffi>=1.1 (from bcrypt>=3.1.3->paramiko->ansible==2.7.12->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/eb/9c/c6b452e414ea289822f3cc44bdf4822066dc7b947f03eaa2a2b97dda5032/cffi-1.14.1-cp36-cp36m-manylinux1_x86_64.whl (400kB)
    100% |████████████████████████████████| 409kB 4.1MB/s 
Collecting pycparser (from cffi>=1.1->bcrypt>=3.1.3->paramiko->ansible==2.7.12->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/ae/e7/d9c3a176ca4b02024debf82342dab36efadfc5776f9c8db077e8f6e71821/pycparser-2.20-py2.py3-none-any.whl (112kB)
    100% |████████████████████████████████| 112kB 6.8MB/s 
Installing collected packages: jinja2, pycparser, cffi, bcrypt, pynacl, paramiko, ansible, netaddr, pbr, requests, hvac, jmespath, ruamel.yaml
  Found existing installation: Jinja2 2.10
    Not uninstalling jinja2 at /usr/lib/python3/dist-packages, outside environment /usr
  Running setup.py install for ansible ... done
  Found existing installation: requests 2.18.4
    Not uninstalling requests at /usr/lib/python3/dist-packages, outside environment /usr
Successfully installed ansible-2.7.12 bcrypt-3.1.7 cffi-1.14.1 hvac-0.8.2 jinja2-2.10.1 jmespath-0.9.4 netaddr-0.7.19 paramiko-2.7.1 pbr-5.2.0 pycparser-2.20 pynacl-1.4.0 requests-2.24.0 ruamel.yaml-0.15.96
vagrant@kube-master1:~/kubespray$ pwd
/home/vagrant/kubespray

vagrant@kube-master1:~/kubespray$ pwd
/home/vagrant/kubespray
vagrant@kube-master1:~/kubespray$ cp -rfp inventory/sample inventory/mycluster
vagrant@kube-master1:~/kubespray$ vi inventory/mycluster/inventory.ini 
vagrant@kube-master1:~/kubespray$ vi inventory/mycluster/inventory.ini 
vagrant@kube-master1:~/kubespray$ ansible -i inventory/mycluster/inventory.ini -m ping all
kube-master1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
kube-node3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
kube-node2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
kube-node1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

```



```
vagrant@kube-master1:~/kubespray$ ls 
CNAME            Makefile        RELEASE.md         ansible.cfg         docs             library                    remove-node.yml   scale.yml  test-infra
CONTRIBUTING.md  OWNERS          SECURITY_CONTACTS  cluster.yml         extra_playbooks  logo                       requirements.txt  scripts    tests
Dockerfile       OWNERS_ALIASES  Vagrantfile        code-of-conduct.md  index.html       mitogen.yaml               reset.yml         setup.cfg  upgrade-cluster.yml
LICENSE          README.md       _config.yml        contrib             inventory        recover-control-plane.yml  roles             setup.py
     
vagrant@kube-master1:~/kubespray$ sudo vi inventory/mycluster/group_vars/k8s-cluster/addons.yml 

> metrics_server_enabled: true
> ingress_nginx_enabled: true


vagrant@kube-master1:~/kubespray$ ansible all -i inventory/mycluster/inventory.ini -m apt -a 'update_cache=yes' --become
kube-master1 | CHANGED => {
    "cache_update_time": 1597132282,
    "cache_updated": true,
    "changed": true
}
kube-node3 | CHANGED => {
    "cache_update_time": 1597132293,
    "cache_updated": true,
    "changed": true
}
kube-node2 | CHANGED => {
    "cache_update_time": 1597132297,
    "cache_updated": true,
    "changed": true
}
kube-node1 | CHANGED => {
    "cache_update_time": 1597132301,
    "cache_updated": true,
    "changed": true
}
vagrant@kube-master1:~/kubespray$ ansible-playbook -i inventory/mycluster/inventory.ini cluster.yml --become

```

metrics_server_enabled: true

ingress_nginx_enabled: true

