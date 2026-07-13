구성관리도구

멱등성 보장

컨트롤러가 노드들을 제어
##### 설치

```sh
# EPEL 저장소 필요
dnf install -y epel-release
dnf install -y ansible

# ssh 통신 필요
ssh-keygen -m pem -t rsa -q -N ""
scp .ssh/id_rsa.pub root@<노드IP>:/root/.ssh/authorized_keys

# Ansible 호스트 인벤토리 명세
vi /etc/ansible/hosts
```
인벤토리 파일은 대괄호`[]`로 집합을 명시하고, 그 아래에 부분집합이나 호스트의 이름, 또는 IP 주소를 나열한다.
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
### ad-hoc
```sh
ansible -i /etc/ansible/hosts all -m ping
# 기본 인벤토리(/etc/ansible/hosts)를 사용했다면 인벤토리 옵션을 생략해도 호스트를 지정할 수 있다.
ansible web -m ping -vvvv
# 셸 명령어 실행하는 shell 모듈
ansible node -m shell -a "ls -al"
```
### 플레이북
```sh
ansible-playbook -i /etc/ansible/hosts mkdir.yml
# ansible-playbook을 alias 등록
alias ap='ansible-playbook'
# 기본 인벤토리는 생략 가능
ap mkdir.yml
```
##### `shell` 모듈
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
##### `file` 모듈
###### `test.yml`
```yml
---
- name: make file /test
  hosts: node
  gather_facts: false
  ignore_errors: true

  tasks:
  - name: make file test
    file:
      path: /test
      state: touch
      mode: '0777'
      owner: a
      group: b

  - name: make directory /tbabo
    file:
      path: /tbabo
      state: directory
      mode: '0777'
      owner: a
      group: b
```
##### `user`모듈
###### `user.yml`
```yml
---
- name: user add a,b && fix permition && file, directory creation
  hosts: db
  gather_facts: false
  ignore_errors: true

  tasks:
  - name: user add
    user:
      name: "{{ item.user }}"
      update_password: always
      password: "{{ 'It1' | password_hash('sha512') }}"
      uid: "{{ item.uid }}"
      
      # 루프 사용. with_items 보다 loop 권장
      # 각각의 오브젝트가 item이라는 변수에 담겨 실행된다.
    with_items:
      - { user: a, uid: 2000 }
      - { user: b, uid: 3000 }
```
####
