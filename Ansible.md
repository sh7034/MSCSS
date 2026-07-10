구성관리도구
#### 설치

```sh
# EPEL 저장소 필요
dnf install -y epel-release
dnf install -y ansible

# ssh 통신 필요
ssh-keygen -m pem -t rsa -q -N ""
scp .ssh/id_rsa.pub root@<노드IP>:/root/.ssh/authorized_keys

# Ansible 호스트 명세
vi /etc/ansible/hosts
```
###### `etc/ansible/hosts` 예시
```
[all]
10.0.0.[11:14]

[node]
10.0.0.12
10.0.0.13
10.0.0.14

[web]
10.0.0.12

[was]
10.0.0.13

[db]
10.0.0.14

[httpd:children]
web
was

```
####
```sh
ansible -i /etc/ansible/hosts all -m ping
ansible web -m ping -vvvv

ansible node -m shell -a "ls -al"
```

#### 
##### 셸 명령을 실행하는 플레이북
###### `mkdir.yml`
```yml
---
- name: make directory
  hosts: node
  gather_facts: false
  ignore_errors: true

  tasks:
  - name: make directory /test
    shell: 'mkdir /test'

  - name: view directory
    shell: 'ls -al /'
    register: result

  - name: print result
    debug:
      var: result.stdout_lines

  - name: remove directory /test
    shell: 'rmdir /test'
```

```sh
ansible-playbook -i /etc/ansible/hosts mkdir.yml
```