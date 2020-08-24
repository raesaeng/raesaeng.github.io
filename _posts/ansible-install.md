# 2020-08-20 ansible

Created: Aug 20, 2020 10:17 AM
Reviewed: No

### ansible

Ansible은 자동화된 인프라 구성을 가능하게 하는 도구로 여러 개의 서버를 효율적으로 관리할 수 있게 해준다. 플레이북이라는 yaml 형식으로 애플리케이션을 실행할 구성을 선언해 두면 필요시마다 자동으로 실행시켜 동일한 환경을 구성할 수 있다. 간소하고 신속한 처리가 특징이다.

 

- ansible의 3요소

    인벤토리 / 플레이북 / 모듈

    인벤토리는 어디서 수행 할 것인지?

    플레이북은 무엇을 수행 할 것인지?

    모듈은 어떻게 수행할 것 인지? 

    - inventory

        호스트 그룹에 정의되며 앤서블에 의해 제어될 대상을 정의합니다. 일반적으로 hosts.ini 파일에 정의해 사용 하며, 여러 서버들의 SSH접근 iP, 포트, 리눅스 사용자 와 같은 접속 정보를 입력한다.

    - playbook

        플레이북은 인벤토리 파일에서 정의한 대상들이 무엇을 수행할 것인지 정의하는 역할을한다. yaml 형식으로 작성된다. 단독으로 사용되지 않고 인벤토리와 플레이북의 조합으로 같이 사용된다.

    - module

        모듈은 플레이북에서 task가 어떻게 수행될지를 정의하는 요소이다. 타겟 호스트로 실제 작업을 처리하는 단위로 이 Module이라는 개념을 사용한다. 앤서블은 Python Code를 호출하여 실행하기 때문에 Python이 필수적으로 필요하며, 실제로 앤서블을 설치해보면 다양한 모듈이 같이 설치되는 것을 볼 수가 있습니다. yum sysctl systemd copy git docker_container ... 등. 예를 들어 yum 명령어를 통해 패키지를 설치할때 yum 모듈을 사용하면 되며 위 플레이북 예시에서 yum 부분이 yum 모듈을 정의하고 이를 사용하겠다는 의미이다.

### 앤서블 시작하기

### ansible 설치

- hostname과 ip 설정하기
    - ansible-master1
    - ansible-mhost1
    - ansible-mhost2
    - ansible-mhost3

    ```jsx
    hostnamectl set-hostname cnode.cccr.local
    nmcli connection add con-name static ifname enp0s8 type ethernet ipv4.method manual ipv4.addresses 192.168.56.111/24

    mhost1 192.168.56.121
    mhost2 192.168.56.122
    mhost3 192.168.56.123

    nmcli connection up static
    ```

- ansible 설치하기

    ```jsx
    student@CCCR03:~$ ssh user@192.168.56.111
    user@192.168.56.111's password: 
    Last login: Thu Aug 20 21:41:06 2020
    [user@cnode ~]$ sudo yum -y install epel-release

    We trust you have received the usual lecture from the local System
    Administrator. It usually boils down to these three things:

        #1) Respect the privacy of others.
        #2) Think before you type.
        #3) With great power comes great responsibility.

    [sudo] password for user: 

    [user@cnode ~]$ yum repolist
    [user@cnode ~]$ sudo yum -y install ansible
    Loaded plugins: fastestmirror, langpacks
    Loading mirror speeds from cached hostfile
    epel/x86_64/metalink                                     | 7.3 kB     00:00     
     * base: mirror.navercorp.com
     * epel: fedora.cs.nctu.edu.tw
     * extras: mirror.navercorp.com
     * updates: mirror.navercorp.com
    .......
      python2-pyasn1.noarch 0:0.1.9-7.el7                                           
      sshpass.x86_64 0:1.06-2.el7                                                   
    Complete!

    [user@cnode ~]$ ansible --version
    ansible 2.9.10
      config file = /etc/ansible/ansible.cfg
      configured module search path = [u'/home/user/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
      ansible python module location = /usr/lib/python2.7/site-packages/ansible
      executable location = /usr/bin/ansible
      python version = 2.7.5 (default, Apr  2 2020, 13:16:51) [GCC 4.8.5 20150623 (Red Hat 4.8.5-39)]
      

    ```

- ansible bash completion 설치하기

    ```jsx
    [user@cnode ~]$ wget https://github.com/dysosmus/ansible-completion/archive/master.zip
    --2020-08-20 21:44:13--  https://github.com/dysosmus/ansible-completion/archive/master.zip
    Resolving github.com (github.com)... 52.78.231.108
    Connecting to github.com (github.com)|52.78.231.108|:443... connected.
    ......
    [user@cnode ~]$ sudo cp ansible-completion-master/*.bash /etc/bash_completion.d/
    [user@cnode ~]$ exec bash

    >> 미리 한번씩 ssh 접속해준다.
    [user@cnode ~]$ sudo vi /etc/hosts
    [user@cnode ~]$ ssh user@mhost
    [user@mhost1 ~]$ exit
    [user@cnode ~]$ ssh user@mhost2
    [user@cnode ~]$ ssh user@mhost3
    [user@mhost3 ~]$ exit
    logout
    Connection to mhost3 closed.
    ```

- /etc/hosts 파일 수정하기

    수정후 미리 ssh 접속해본다.

    ```jsx
    [user@cnode ~]$ sudo vi /etc/hosts
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
    192.168.56.111  cnode cnode.cccr.local
    192.168.56.121  mhost1 mhost1.cccr.local
    192.168.56.122  mhost2 mhost2.cccr.local
    192.168.56.123  mhost3 mhost3.cccr.local

    [user@cnode ~]$ ssh user@mhost
    [user@mhost1 ~]$ exit
    [user@cnode ~]$ ssh user@mhost2
    [user@cnode ~]$ ssh user@mhost3
    [user@mhost3 ~]$ exit
    logout
    Connection to mhost3 closed.
    ```

- ssh key 사용하기

    ssh user@mhost1 아이피주소가 아닌 별칭으로 접속이 가능하다.

    ```jsx
    [user@cnode ~]$ ssh-keygen
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/user/.ssh/id_rsa): 
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 
    Your identification has been saved in /home/user/.ssh/id_rsa.
    Your public key has been saved in /home/user/.ssh/id_rsa.pub.
    The key fingerprint is:
    SHA256:KuqDmDIX0MjnmIq7j1Mm8HFvijiX/lrpodQCzdScy1M user@cnode.cccr.local
    The key's randomart image is:
    +---[RSA 2048]----+
    |                 |
    |   o .           |
    |.o. + E          |
    |+=oo.o           |
    |ooBo+.  S        |
    |.+=o oo.         |
    |+Bo+*o.          |
    |%o**.o           |
    |=%B+o            |
    +----[SHA256]-----+
    [user@cnode ~]$ ssh-copy-id user@mhost1
    /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/user/.ssh/id_rsa.pub"
    /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
    /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
    user@mhost1's password: 

    Number of key(s) added: 1

    Now try logging into the machine, with:   "ssh 'user@mhost1'"
    and check to make sure that only the key(s) you wanted were added.

    [user@cnode ~]$ ssh-copy-id user@mhost2
    [user@cnode ~]$ ssh-copy-id user@mhost3

    [user@cnode ~]$ ssh user@mhost1
    Last login: Thu Aug 20 21:45:55 2020 from 192.168.56.111
    ```

- 인벤토리 파일 수정

    /etc/ansible/hosts 파일에 추가해주면 hosts 인식이 된다.

    ```jsx
    [user@cnode ~]$ sudo vi /etc/ansible/hosts 
    mhost1
    mhost2
    mhost3

    [user@cnode ~]$ ansible --list-hosts all
      hosts (3):
        mhost1
        mhost2
        mhost3
    ```

- 호스트에게 ping

    ```jsx
    [user@cnode ~]$ ansible all -m ping
    mhost1 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": false, 
        "ping": "pong"
    }
    mhost2 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": false, 
        "ping": "pong"
    }
    mhost3 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": false, 
        "ping": "pong"
    }
    ```

- 호스트 user 들에게 sudo 권한 부여하기

    mhost1, mhost2, mhost3 모두 파일 생성해준다.

    ```jsx
    [user@mhost1 ~]$ sudo vi /etc/sudoers.d/user
    [user@mhost1 ~]$ sudo cat /etc/sudoers.d/user
    user        ALL=(ALL)       NOPASSWD: ALL
    ```

- 권한 확인

    ```jsx
    [user@cnode ~]$ ansible all -m yum -a "name=httpd state=" --become
    [user@cnode ~]$ ansible all -m yum -a "name=httpd state=absent" --become

    [user@cnode ~]$ ansible mhost1 -m yum -a "name=httpd state=absent" --become
    mhost1 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": false, 
        "msg": "", 
        "rc": 0, 
        "results": [
            "httpd is not installed"
        ]
    }
    [user@cnode ~]$ ansible mhost2 -m yum -a "name=httpd state=absent" --become
    mhost2 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": false, 
        "msg": "", 
        "rc": 0, 
        "results": [
            "httpd is not installed"
        ]
    }
    [user@cnode ~]$ ansible mhost3 -m yum -a "name=httpd state=absent" --become
    mhost3 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": false, 
        "msg": "", 
        "rc": 0, 
        "results": [
            "httpd is not installed"
        ]
    }
    ```

유저생성

```
student@CCCR03:~/Shell/practice$ sudo useradd user001
[sudo] password for student: 
student@CCCR03:~/Shell/practice$ sudo tail -1 /etc/passwd
user001:x:1001:1001::/home/user001:/bin/sh
student@CCCR03:~/Shell/practice$ sudo tail -1 /etc/shadow
user001:!:18494:0:99999:7:::
student@CCCR03:~/Shell/practice$ ls /home
student

>>유저생성시 단순하게 useradd로만 생성하면 패스워드도 home에 디렉토리도 구성되지 않는
```

```
student@CCCR03:~/Shell/practice$ sudo useradd -m -d /home/user002 user002
student@CCCR03:~/Shell/practice$ ls /home
student  user002
student@CCCR03:~/Shell/practice$ sudo -i
root@CCCR03:~# passwd user001
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
root@CCCR03:~# tail -1 /etc/passwd
user002:x:1002:1002::/home/user002:/bin/sh
root@CCCR03:~# echo "user003:x:1003:1003::/home/user003:/bin/sh" >> /etc/passwd
root@CCCR03:~# tail -1 /etc/passwd
user003:x:1003:1003::/home/user003:/bin/sh
root@CCCR03:~# tail -1 /etc/shadow
user002:!:18494:0:99999:7:::
root@CCCR03:~# passwd user002
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
root@CCCR03:~# tail -1 /etc/shadow
user002:$6$/eNQYZw7$xIDeIgL0q69zsrlmS/0A3pDRGyYrNKTyIygHV6MLynsvmFvD01V0r8MIYDuCSWU/OQivUvIGlTNceV5p74CjH1:18494:0:99999:7:::
root@CCCR03:~# echo "user002:$6$/eNQYZw7$xIDeIgL0q69zsrlmS/0A3pDRGyYrNKTyIygHV6MLynsvmFvD01V0r8MIYDuCSWU/OQivUvIGlTNceV5p74CjH1:18494:0:99999:7:::
> "
user002:$/eNQYZw7/0A3pDRGyYrNKTyIygHV6MLynsvmFvD01V0r8MIYDuCSWU/OQivUvIGlTNceV5p74CjH1:18494:0:99999:7:::

root@CCCR03:~# echo "user003:$6$/eNQYZw7$xIDeIgL0q69zsrlmS/0A3pDRGyYrNKTyIygHV6MLynsvmFvD01V0r8MIYDuCSWU/OQivUvIGlTNceV5p74CjH1:18494:0:99999:7:::" >> /etc/shadow
root@CCCR03:~# tail -1 /etc/shadow
user003:$/eNQYZw7/0A3pDRGyYrNKTyIygHV6MLynsvmFvD01V0r8MIYDuCSWU/OQivUvIGlTNceV5p74CjH1:18494:0:99999:7:::
root@CCCR03:~# chpasswd
user003:P@ssw0rd

chpasswd: line 2: missing new password

chpasswd: line 3: missing new password
^C
root@CCCR03:~# tail -1 /etc/shadow
user003:$6$3o1d6AZz$b8M1auX98GHkdsRhKw66Hu8Jyq40ibXiuE1JGr1t8GRv8p423beEswKlimIOOkcm6lT7G06sREypuJzLeap/r/:18494:0:99999:7:::
root@CCCR03:~# echo "user003:qwer1@34" | chpasswd
root@CCCR03:~# tail -1 /etc/shadow
user003:$6$N6db2Hw5$Syc3XgYC7Ecji1Ez3al0WQZnTmI7Q3j1QqoOrKQp7sosGm0Su/AUjwgDgkrSajJUPcutsYH/1QP2OINeC/XmB.:18494:0:99999:7:::
root@CCCR03:~# vi useradd.sh
root@CCCR03:~# ./useradd.sh
-bash: ./useradd.sh: Permission denied
root@CCCR03:~# chmod +x useradd.sh 
root@CCCR03:~# ./useradd.sh
Enter basename : cccr
Start number : 100
User count : 5
./useradd.sh: line 15: ehco: command not found
./useradd.sh: line 15: ehco: command not found
./useradd.sh: line 15: ehco: command not found
./useradd.sh: line 15: ehco: command not found
./useradd.sh: line 15: ehco: command not found
root@CCCR03:~# cat useradd.sh
#!/bin/bash

echo -n "Enter basename : "
read BASENAME
echo -n "Start number : "
read STARNUM
echo -n "User count : "
read USERCNT

(( USERCNT-- ))

for i in `seq 0 $USERCNT`
do
	USERNAME="$BASENAME`expr $STARTNUM + $i`"
	ehco $USERNAME
done

root@CCCR03:~# vi useradd.sh
root@CCCR03:~# cat useradd.sh 
#!/bin/bash

echo -n "Enter basename : "
read BASENAME
echo -n "Start number : "
read STARNUM
echo -n "User count : "
read USERCNT

(( USERCNT-- ))

for i in `seq 0 $USERCNT`
do
	USERNAME="$BASENAME`expr $STARTNUM + $i`"
	useradd -m -d /home/$USERNAME $USERNAME && echo "$USERNAME user created.."
	echo "$USERNAME:P@ssw0rd:" | chpasswd && echo "$USERNAME password set to P@ssw0rd"
done

root@CCCR03:~# ./useradd.sh 
Enter basename : ^[[A^[[A^[[A^Z
[1]+  Stopped                 ./useradd.sh
root@CCCR03:~# sudo ./useradd.sh 
Enter basename : cccr 
Start number : 100
User count : 5
cccr0 user created..
cccr0 password set to P@ssw0rd
cccr1 user created..
cccr1 password set to P@ssw0rd
cccr2 user created..
cccr2 password set to P@ssw0rd
cccr3 user created..
cccr3 password set to P@ssw0rd
cccr4 user created..
cccr4 password set to P@ssw0rd
root@CCCR03:~# tail -5 /etc/passwd
cccr0:x:1004:1004::/home/cccr0:/bin/sh
cccr1:x:1005:1005::/home/cccr1:/bin/sh
cccr2:x:1006:1006::/home/cccr2:/bin/sh
cccr3:x:1007:1007::/home/cccr3:/bin/sh
cccr4:x:1008:1008::/home/cccr4:/bin/sh
root@CCCR03:~# sudo tail -5 /etc/shadow
cccr0:$6$WovEESnz$6Gh3N9IiWcg3WglhJCL6kOgBWw2FQxvH0nkHZDqfkBtGyAEtdDbDpooFgTI4dbSNGSArfd.Er8mmJeeYwySFj1:18494:0:99999:7:::
cccr1:$6$qLKL8OKB$3G4l.ntXuO0HYL0EsrbDMkrZPkoxsB29wNOjJN8Pcg2MZ/xAIBMHtPsFWq3Giws.faUL2QlK.UH//k6HKI39g0:18494:0:99999:7:::
cccr2:$6$y0tHHoE0$ksT/RRDiXhhyr9NvncGWdcWLN.7qK6pwwvNzz89W8hJtWN/zAPJLx6oT/yRYRFvdfbMHiXvs2JpynsTY05ZXW/:18494:0:99999:7:::
cccr3:$6$PIQvQlKM$ebP27AIu6JALoLcPl163lJhf647OEIlcaDvxMZtsv7LAkRgvFgcRO2ozJMzx.lM1Y1vDA6HsPkKSom9YCyVbv.:18494:0:99999:7:::
cccr4:$6$UwzH7itX$n9593yKU13nHv36JdLP3C0Gfe7XvaWn.zhAn3oX.H8i4GpwW3SP1uaQtQkD0BeATAOTqmqVgMpOrBPQ54tqkR0:18494:0:99999:7:::
root@CCCR03:~# ls -l /home/
total 28
drwxr-xr-x  2 cccr0   cccr0   4096  8월 20 10:15 cccr0
drwxr-xr-x  2 cccr1   cccr1   4096  8월 20 10:15 cccr1
drwxr-xr-x  2 cccr2   cccr2   4096  8월 20 10:15 cccr2
drwxr-xr-x  2 cccr3   cccr3   4096  8월 20 10:15 cccr3
drwxr-xr-x  2 cccr4   cccr4   4096  8월 20 10:15 cccr4
drwxr-xr-x 31 student student 4096  8월 20 09:38 student
drwxr-xr-x  2 user002 user002 4096  8월 20 09:49 user002
```

### 4.

```
root@CCCR03:~# vi testfile.sh
root@CCCR03:~# cat testfile.sh 
#!/bin/bash
# check file : test.txt

# Backup File Check
if [ -f test.txt.backup ] 
then
	# Backup file exist
	echo "Backup file found!"
else
	# Backup file doesn't exist
	cp test.txt test.txt.backup && echo "Backup File Created!"
	exit 0
fi

# File Exist Check

# File Same Check

root@CCCR03:~# vi testfile.sh
root@CCCR03:~# cat > test.txt
HELLO
SOYOON^C
root@CCCR03:~# cp test.txt test2.txt
root@CCCR03:~# diff test.txt test2.txt
root@CCCR03:~# cat > test2.txt
world
hihi^C
root@CCCR03:~# diff test.txt test2.txt
1c1
< HELLO
---
> world
root@CCCR03:~# diff test.txt test.txt 
root@CCCR03:~# echo $?
0
root@CCCR03:~# diff test.txt test2.txt 
1c1
< HELLO
---
> world
root@CCCR03:~# echo $?
1
root@CCCR03:~# md5sum test.txt 
0084467710d2fc9d8a306e14efbe6d0f  test.txt
root@CCCR03:~# md5sum test2.txt 
591785b794601e212b260e25925636fd  test2.txt
root@CCCR03:~# vi testfile.sh 
root@CCCR03:~# chmod +x testfile.sh 
root@CCCR03:~# ./testfile.sh 
Backup File Created!
root@CCCR03:~# ls -l
total 4096024
-rw------- 1 root root 4194304000  7월 15 12:24 fileA
-rw-r--r-- 1 root root          6  8월 20 10:29 test2.txt
-rwxr-xr-x 1 root root        733  8월 20 10:34 testfile.sh
-rw-r--r-- 1 root root          6  8월 20 10:28 test.txt
-rw-r--r-- 1 root root          6  8월 20 10:35 test.txt.backup
-rwxr-xr-x 1 root root        387  8월 20 10:12 useradd.sh
root@CCCR03:~# ./testfile.sh 
Backup file found!
Original file found!
File Check Complete!
root@CCCR03:~# cat >> test.txt
hi
bye^C
root@CCCR03:~# ./testfile.sh 
Backup file found!
Original file found!
File Changed!
File recovered!

root@CCCR03:~# cat testfile.sh 
#!/bin/bash
# check file : test.txt

# Backup File Check
if [ -f test.txt.backup ] 
then
	# Backup file exist
	echo "Backup file found!"
else
	# Backup file doesn't exist
	cp test.txt test.txt.backup && echo "Backup File Created!"
	exit 0
fi

# File Exist Check
if [ -f test.txt ]
then
        # Original file exist 
        echo "Original file found!"
else
        # Original file doesn't exist
	echo "Original file not found!"
        cp test.txt.backup test.txt && echo "File Recovered!"
        exit 0
fi

# File Same Check
if diff test.txt test.txt.backup > /dev/null 2>&1
then
	# File Same
	echo "File Check Complete!"
else
	# File not same
	echo "File Changed!"
	cp -f test.txt.backup test.txt && echo "File recovered!"
fi
```

### 5. 시스템 상태 체크 프로그램

```

```