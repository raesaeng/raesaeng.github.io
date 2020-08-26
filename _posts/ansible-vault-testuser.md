### ansible-vault 실습

사용자를 자동으로 생성해주는 플레이북을 생성해 볼 것이다.

1. 사용자 생성 후 비번 설정

```
[user@cnode roletest]$ cd ..
[user@cnode ansible]$ mkdir vault
[user@cnode ansible]$ cp ansible.cfg inventory vault/
[user@cnode ansible]$ cd vault/
[user@cnode vault]$ pwd
/home/user/ansible/vault
[user@cnode vault]$ sudo useradd testuser
[sudo] password for user: 
[user@cnode vault]$ sudo passwd testuser 
Changing password for user testuser.
New password: 
Retype new password: 
passwd: all authentication tokens updated successfully.
```



2. password 파일 암호화 진행

   새로 생성한 testuser의 암호 해시값을 복사해 입력해 yaml 파일을 만들어준다.

```
[user@cnode vault]$ sudo tail -1 /etc/shadow
testuser:$6$LSGLNFlg$7iDPO4xw5MHEKru498EKruQqLjmYA7loBpiv.HD6/mZcFqLCgLdMi4wxlvwFhcYJm2h280bfEy67qXIrRM/Jf.:18500:0:99999:7:::

[user@cnode vault]$ vi secret_password.yml
[user@cnode vault]$ cat secret_password.yml
password: $7$LSGLNFlg$7iDPO4xw5MHEKru498EKruQqLjmYA7loBpiv.HD6/mZcFqLCgLdMi4wxlvwFhcYJm2h280bfEy67qXIrRM/Jf.
username: testuser
[user@cnode vault]$ ansible-vault encrypt secret_password.yml 
New Vault password: 
Confirm New Vault password: 
Encryption successful
```



3. 플레이북 파일 생성하여 실행한다.

```
[user@cnode vault]$ vi useradd.yml
[user@cnode vault]$ cat useradd.yml
---
- name: user add play
  hosts: mhost1
  become: true
  vars_files:
    - secret_password.yml
  tasks:
    - name: user add
      user:
        name: "{{ username }}"
        password: "{{ password }}"
```

4. * 플레이북으로 실행할 때 암호가 필요하다.

     —ask-vault-pass 으로 실행 가능하다.

```
[user@cnode vault]$ ansible-playbook useradd.yml --syntax-check 
ERROR! Attempting to decrypt but no vault secrets found

[user@cnode vault]$ ansible-playbook useradd.yml --ask-vault-pass 
Vault password: 

PLAY [user add play] ***************************************************************

TASK [Gathering Facts] *************************************************************
ok: [mhost1]

TASK [user add] ********************************************************************
changed: [mhost1]

PLAY RECAP *************************************************************************
mhost1                     : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```





* 또다른 플레이북 실행방법은

  암호파일을 생성하여 지정해주는 것이다.

  암호파일에 암호를 적어 권한을 주고

  —vault-password-file=.password 으로 지정해줘야한다.

```
[user@cnode vault]$ cat > .password
dkagh1.
[user@cnode vault]$ chmod 600 .password

[user@cnode vault]$ ansible-playbook useradd.yml --vault-password-file=.password --syntax-check 

playbook: useradd.yml

[user@cnode vault]$ ansible-playbook useradd.yml --vault-password-file=.password

PLAY [user add play] ***************************************************************

TASK [Gathering Facts] *************************************************************
ok: [mhost1]

TASK [user add] ********************************************************************
ok: [mhost1]

PLAY RECAP *************************************************************************
mhost1                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[user@cnode vault]$ ansible-playbook useradd.yml --vault-password-file=.password 

PLAY [user add play] ***************************************************************

TASK [Gathering Facts] *************************************************************
ok: [mhost1]

TASK [user add] ********************************************************************
changed: [mhost1]

PLAY RECAP *************************************************************************
mhost1                     : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```





5. 실행 후 플레이북으로 mhost1에 testuser가 잘 생성되었는지 확인해본다.

   ssh testuser 접속해보기.

```
[user@cnode vault]$ ssh testuser@mhost1
testuser@mhost1's password: 
Last failed login: Wed Aug 26 01:25:44 EDT 2020 from 192.168.56.111 on ssh:notty
There were 6 failed login attempts since the last successful login.
System name: mhost1
Date: 2020-08-24
Only use this system who has permission.
System Adminstrator : admin@cccr.local
```