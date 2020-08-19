---
layout: post
title: "지옥같았던 쿠버네티스 설치하기"
author: "한결"
categories: blog
---

### 쿠버네티스 설치하기

#### kuberspray와 vagrant를 이용해 쿠버네티스를 설치한다.

메모리가 작아서 설치가 쉽지 않았다. 심지어 사실상 실패함. 이틀이나 설치에 목맨게 아쉬워서 정리해둔다.

* Vagrant : 베이그런트는 포터블 가상화 소프트웨어 개발 환경의 생성 및 유지보수를 위한 오픈 소스 소프트웨어 제품의 하나이다. 베이그런트는 루비 언어로 작성되어 있지만 생태계는 몇 가지 언어로 개발을 지원한다.
* kuberspray : Kubespray is a composition of [Ansible](https://docs.ansible.com/) playbooks, [inventory](https://github.com/kubernetes-sigs/kubespray/blob/master/docs/ansible.md), provisioning tools, and domain knowledge for generic OS/Kubernetes clusters configuration management tasks.
  
  
 1. vagrant 설치하기

    vagrant 홈페이지에서 ubuntu - debian 버전을 다운받는다.

    ```
    ls -l *.deb
    sudo dpkg --install vagrant_2.2.9~~~.deb
    vagrant status
    vagrant plugin list
    vagrant box list
    vagrant box add ubuntu/bionic64
    vagrant plugin vagrant-hostmanager
    vagrant plugin vagrant-disksize
    vagrant up 
    ```

    설치 과정에서 사용할 명령어 목록이다.

    전날 이미 vagrant와 ubuntu/bionic64를 받아두었기에 나머지 과정을 진행했다.

    vagrant 폴더를 생성해주고 만들 가상머신의 설정값이 정의된 vagrantfile을 만들어준다.

    ```
    student@CCCR01:~$ mkdir vagrant
    student@CCCR01:~/vagrant$ sudo vi vagrantfile
    student@CCCR01:~/vagrant$ sudo cat vagrantfile
    # -*- mode: ruby -*-
    # vi: set ft=ruby :
    
    Vagrant.configure("2") do |config|
      config.vm.define "kube-master1" do |config|
        config.vm.box = "ubuntu/bionic64"
        config.vm.provider "virtualbox" do |vb|
          vb.name = "kube-master1"
          vb.cpus = 2
          vb.memory = 3072
        end
        config.vm.hostname = "kube-master1"
        config.vm.network "private_network", ip: "192.168.56.11"
        config.disksize.size = "30GB"
      end
      config.vm.define "kube-node1" do |config|
        config.vm.box = "ubuntu/bionic64"
        config.vm.provider "virtualbox" do |vb|
          vb.name = "kube-node1"
          vb.cpus = 2
          vb.memory = 3072
        end
        config.vm.hostname = "kube-node1"
        config.vm.network "private_network", ip: "192.168.56.21"
        config.disksize.size = "30GB"
      end
      config.vm.define "kube-node2" do |config|
        config.vm.box = "ubuntu/bionic64"
        config.vm.provider "virtualbox" do |vb|
          vb.name = "kube-node2"
          vb.cpus = 2
          vb.memory = 3072
        end
        config.vm.hostname = "kube-node2"
        config.vm.network "private_network", ip: "192.168.56.22"
        config.disksize.size = "30GB"
      end
      config.vm.define "kube-node3" do |config|
        config.vm.box = "ubuntu/bionic64"
        config.vm.provider "virtualbox" do |vb|
         vb.name = "kube-node3"
          vb.cpus = 2
          vb.memory = 3072
        end
        config.vm.hostname = "kube-node3"
        config.vm.network "private_network", ip: "192.168.56.23"
        config.disksize.size = "30GB"
      end
    
      # Hostmanager plugin
      config.hostmanager.enabled = true
      config.hostmanager.manage_guest = true
    
      # Enable SSH Password Authentication
      config.vm.provision "shell", inline: <<-SHELL
        sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
        sed -i 's/archive.ubuntu.com/ftp.daum.net/g' /etc/apt/sources.list
        sed -i 's/security.ubuntu.com/ftp.daum.net/g' /etc/apt/sources.list
        systemctl restart ssh
      SHELL
    end
    ```

    vagrant 파일과 상태를 확인 후 hostmanager, disksize 플러그인을 다운받는다.

    ```
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
    ```

    vagrant up을 하면 vagrantfile 에 설정한대로 가상머신이 생성된다.

    설정한대로 4개의 가상머신이 생성되었고 vagrant up 명령어로 실행시킬 수 있다.
    vagrant suspend / vagrant halt / vagrant resume 으로 중지, 종료, 재실행시킬 수 있다.

    ```
    student@CCCR01:~/vagrant$ vagrant up
    
    Bringing machine 'kube-master1' up with 'virtualbox' provider...
    Bringing machine 'kube-node1' up with 'virtualbox' provider...
    Bringing machine 'kube-node2' up with 'virtualbox' provider...
    Bringing machine 'kube-node3' up with 'virtualbox' provider...
    ==> kube-master1: Importing base box 'ubuntu/bionic64'...
    ==> kube-master1: Matching MAC address for NAT networking...
    ........
    
    ```

    vagrant status를 한번 더 확인해본다. 동작중인 것을 확인할 수 있다.

    ```
    student@CCCR01:~/vagrant$ vagrant status
    
    Current machine states:
    
    kube-master1              running (virtualbox)
    kube-node1                running (virtualbox)
    kube-node2                running (virtualbox)
    kube-node3                running (virtualbox)
    
    This environment represents multiple VMs. The VMs are all listed
    above with their current state. For more information about a specific
    VM, run `vagrant status NAME`.
    
    ```

    vagrant ssh 가상머신명으로 master 노드에 접속이 가능하다.

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
    
    ```

    ssh vagrant@아이피주소 로도 가상머신 원격접속이 가능하다.

    192.168.56.11 은 마스터 노드의 아이피 주소이다.

    ```
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

    /etc/hosts 파일을 확인하면 자동으로 vagrant hostmanager가 아이피 주소를 등록해놓은 것을 확인할 수 있다.

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
    ```

	2. 노드에 keygen 생성하여 등록하기

    간편한 ssh 접속을 위하여 keygen을 생성하여 노드마다 등록해준다. 이 작업을 진행하면 노드도 vagrant ssh 가상머신명으로 쉽게 접속이 가능하다. 

    키젠 생성후 ssh-copy-id vagrant@kube-node1 명령어를 통해 모든 노드에 로그인해주면 된다. 비밀번호는 vagrant으로 설정해뒀다.

    ```
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
    
    vagrant@kube-master1:~$ ssh-copy-id vagrant@kube-node3
    
    vagrant@kube-master1:~$ ssh-copy-id vagrant@localhost
    ```

3. 쿠버스프레이 설치하기

   vagrant로 만든 가상머신에 쿠버스프레이를 이용하여 쿠버네티스를 설치할 것이다. 관리의 주체가 될 마스터 노드에 로그인하여 진행한다. 쿠버스프레이는 쿠버네티스 설치를 돕는 자동화도구다. 편리하게 설치가 가능하다. https://github.com/kubernetes-sigs/kubespray.git 에서 다운이 가능하다. 

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
   .........
   
   vagrant@kube-master1:~$ git clone --single-branch --branch v2.12.3 https://github.com/kubernetes-sigs/kubespray.git
   
   vagrant@kube-master1:~$ ls
   kubespray
   vagrant@kube-master1:~$ ls -l
   total 4
   drwxrwxr-x 15 vagrant vagrant 4096 Aug 11 07:35 kubespray
   ```

   쿠버스프레이가 설치되었으면 쿠버스프레이 폴더 내로 이동해야한다.

   ```
   vagrant@kube-master1:~$ cd kubespray/
   vagrant@kube-master1:~/kubespray$ ls
   CNAME            Makefile        RELEASE.md         ansible.cfg         docs             library                    remove-node.yml   scale.yml  test-infra
   CONTRIBUTING.md  OWNERS          SECURITY_CONTACTS  cluster.yml         extra_playbooks  logo                       requirements.txt  scripts    tests
   Dockerfile       OWNERS_ALIASES  Vagrantfile        code-of-conduct.md  index.html       mitogen.yaml               reset.yml         setup.cfg  upgrade-cluster.yml
   LICENSE          README.md       _config.yml        contrib             inventory        recover-control-plane.yml  roles             setup.py
   ```

   쿠버스프레이 폴더 내에는 여러 파일이 존재하며 파이썬3 으로 requirements.txt 를 설치해 줄 것이다.

   ```
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
   ```

   이런식으로 표시되면 잘 받아진 것이다.

   쿠버스프레이 내의 inventory 폴더 안의 sample 폴더를 mycluster 폴더로 복사한다.

   mycluster 내에 inventory.ini 파일을 생성한다.

   ansible 으로 제대로 ping이 되는지 확인해준다.

   ```
   vagrant@kube-master1:~/kubespray$ cp -rfp inventory/sample inventory/mycluster
   vagrant@kube-master1:~/kubespray$ vi inventory/mycluster/inventory.ini 
    [all]  
    kube-master1	ansible_host=192.168.56.11 ip=192.168.56.11 ansible_connection=local
    kube-node1      ansible_host=192.168.56.21 ip=192.168.56.21
    kube-node2 	    ansible_host=192.168.56.22 ip=192.168.56.22
    kube-node3 	    ansible_host=192.168.56.23 ip=192.168.56.23
    
    [all:vars]  
    ansible_python_interpreter=/usr/bin/python3

    [kube-master]  
    kube-master1 
    
    [etcd]  
    kube-master1  
    
    [kube-node]  
    kube-node1  
    kube-node2
    kube-node3  
    
    [calico-rr]  
    
    [k8s-cluster:children]  
    kube-master  
    kube-node  
    calico-rr
    
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

   이렇게 출력되면 통신이 잘 되고 있는 것이다.

   inventory/mycluster/group_vars/k8s-cluster/addons.yml 파일에 metrics_server_enabled와 ingress_nginx_enabled를 true로 변경해준다.

   ```
   vagrant@kube-master1:~/kubespray$ ls 
   CNAME            Makefile        RELEASE.md         ansible.cfg         docs             library                    remove-node.yml   scale.yml  test-infra
   CONTRIBUTING.md  OWNERS          SECURITY_CONTACTS  cluster.yml         extra_playbooks  logo                       requirements.txt  scripts    tests
   Dockerfile       OWNERS_ALIASES  Vagrantfile        code-of-conduct.md  index.html       mitogen.yaml               reset.yml         setup.cfg  upgrade-cluster.yml
   LICENSE          README.md       _config.yml        contrib             inventory        recover-control-plane.yml  roles             setup.py
        
   vagrant@kube-master1:~/kubespray$ sudo vi inventory/mycluster/group_vars/k8s-cluster/addons.yml 
   
   > metrics_server_enabled: true
   > ingress_nginx_enabled: true
   
   ```

   변경했으면 다시 ansible 명령어를 실행해준다. 모든 노드의 캐시 업데이트를 허용한다.

   ```
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
   ```

   이후 최종으로 ansible-playbook 명령어로 yml 파일대로 설치를 진행할 것이다.

   이 과정에서 에러가 나서 실패하면 같은 명령어를 반복적으로 실행해 설치가 될때까지 도전(?) 하는 수밖에 없다.(고 한다...)

   ```
   vagrant@kube-master1:~/kubespray$ ansible-playbook -i inventory/mycluster/inventory.ini cluster.yml --become
   ```

   굉장히 오래걸리는 작업이다. 어떤 노드도 죽지 않고 다운로드가 완료되면 된다.

   
  
  
