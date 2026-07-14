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
##### 참고문서
[[https://docs.ansible.com/projects/ansible/latest/collections/index_module.html]]
[[https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/dnf_module.html#ansible-collections-ansible-builtin-dnf-module]]
[[https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/user_module.html#ansible-collections-ansible-builtin-user-module]]
[[https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/file_module.html#ansible-collections-ansible-builtin-file-module]]

### ad-hoc
```sh
ansible -i /etc/ansible/hosts all -m ping
# 기본 인벤토리(/etc/ansible/hosts)를 사용했다면 인벤토리 옵션을 생략해도 호스트를 지정할 수 있다.
ansible web -m ping -vvvv
# 셸 명령어 실행하는 shell 모듈
# node 노드들에서 shell 모듈을 사용해 ls -al 명령어를 실행하라
ansible node -m shell -a "ls -al"
# dnf로 패키지를 관리하는 dnf 모듈
# was 노드에서 dnf 모듈을 이용해 nginx라는 이름의 패키지의 최신 버전을 설치하라
ansible was -m dnf -a "name=nginx state=latest"
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
##### `dnf`, `copy`, `systemd`, `firewall` 모듈
###### web.yml
```yml
---
- name: install package apache, apache start, firewall open
  hosts: web
  gather_facts: false
  ignore_errors: true
  vars:
    web: httpd
    path: /var/www/html/index.html
    web2: nginx
    path2: /usr/share/nginx/html/index.html
    port: 80/tcp

  tasks:
  - name: install package web
    ansible.builtin.dnf:
      name: "{{ web }}"
      state: latest

  - name: file copy index.html
    ansible.builtin.copy:
      src: index.html
      path: "{{ path }}"

  - name: start web package
    ansible.builtin.systemd_service:
      name: "{{ web }}"
      state: started

  - name: firewalld open
    ansible.posix.firewalld:
      port: "{{ port }}"
      immediate: true
      permanent: true
      state: enabled
```
###### web-delete.yml
```yml
---
- name: install package apache, apache start, firewall open
  hosts: web
  gather_facts: false
  ignore_errors: true
  vars:
    web: httpd
    path: /var/www/html/index.html
    web2: nginx
    path2: /usr/share/nginx/html/index.html
    port: 80/tcp


  tasks:

  - name: firewall closed
    ansible.posix.firewalld:
      port: "{{ port }}"
      state: disabled

  - name: remove package {{ web }}
    ansible.builtin.dnf:
      name: "{{ web }}"
      autoremove: true
      state: absent
```
##### `get_url`, `unarchive`, `replace`, `lineinfile`, `blockinfile` 모듈
###### word.yml
```yml
---
- name: wordpress download & unarchive
  hosts: web
  gather_facts: false
  ignore_errors: true

  tasks:
  - name: wordpress file download
    ansible.builtin.get_url:
      url: https://kor.wordpress.org/wordpress-7.0.1-ko_KR.tar.gz
      dest: /root/

  - name: package install
    ansible.builtin.dnf:
      name:
        - tar
        - php
        - php-cli
        - php-mysqlnd
        - php-gd
        - httpd
      state: latest

  - name: unarchive
    ansible.builtin.unarchive:
      src: /root/wordpress-7.0.1-ko_KR.tar.gz
      dest: /root/
      remote_src: true

  - name: directory copy
    ansible.builtin.copy:
      src: /root/wordpress/
      dest: /var/www/html/
      remote_src: true

  - name: file copy
    ansible.builtin.copy:
      src: /var/www/html/wp-config-sample.php
      dest: /var/www/html/wp-config.php
      remote_src: true

  - name: index.html index.php replace
    ansible.builtin.replace:
      path: /etc/httpd/conf/httpd.conf
      regexp: DirectoryIndex index.html
      replace: DirectoryIndex index.php

  - name: wp-config.php replace
    ansible.builtin.replace:
      path: /var/www/html/wp-config.php
      regexp: "{{ item.src }}"
      replace: "{{ item.dest }}"
    with_items:
      - { src: database_name_here, dest: word }
      - { src: username_here, dest: root }
      - { src: password_here, dest: 'It12345!' }
      - { src: localhost, dest: 10.0.0.14 }

  - name: file create & line
    ansible.builtin.lineinfile:
      path: /var/www/html/health.html
      line: <html><body><h1>Ansible-Health-Line</h1></body></html>
      create: true

  - name: file create & block
    ansible.builtin.blockinfile:
      path: /var/www/html/index.html
      block: |
        <html>
        <body>
        <h1>Ansible-Health-Block</h1>
        </body>
        </html>
      create: true

  - name: httpd start
    ansible.builtin.systemd:
      name: httpd
      state: started

  - name: firewall open
    ansible.posix.firewalld:
      port: 80/tcp
      immediate: true
      permanent: true
      state: enabled
```
###### word-delete.yml
```yml
---
- name: remove wordpress & system init
  hosts: web
  gather_facts: true
  ignore_errors: true

  tasks:
  - name: firewall closed
    ansible.posix.firewalld:
      port: 80/tcp
      state: disabled

  - name: package remove
    ansible.builtin.dnf:
      name:
        - tar
        - httpd
        - php
        - php-cli
        - php-gd
        - php-mysqlnd
      autoremove: true
      state: absent

  - name: file delete
    ansible.builtin.file:
      path: "{{ item }}"
      state: absent
    with_items:
    - wordpress-7.0.1-ko_KR.tar.gz
    - wordpress
```
#### 
