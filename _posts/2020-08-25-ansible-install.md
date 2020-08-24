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
