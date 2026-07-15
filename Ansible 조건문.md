
##### `stat`, `debug` 모듈
- `stat`: 파일/디렉터리/링크의 메타데이터를 조회해서 팩트로 반환
- `debug`: 
###### `test.yml`
```yml
---
- name: test command module
  hosts: db
  gather_facts: true
  ignore_errors: true


  tasks:
    - name: command test
      command: cat /etc/passwd
      register: shkim_db_passwd

    - name: print/etc/passwd
      debug:
        msg: "{{ shkim_db_passwd }}"

    - name: print ansible_facts1
      debug:
        var: ansible_facts['distribution']

    - name: print ansible_facts2
      debug:
        var: ansible_facts['all_ipv4_addresses']
```
###### `whenfile.yml`
```yml
---
- name: get_url file download, when exist file download
  hosts: web
  gather_facts: true
  ignore_errors: true

  tasks:
    - name: path exist file
      stat:
        path: /root/wordpress-7.0.1-ko_KR.tar.gz
      register: shkim_check

    - name: print message file not exists
      debug:
        msg: "파일이 존재하지 않습니다. 다운로드를 시작합니다."
      when: not shkim_check.stat.exists

    - name: print message file exists
      debug:
        msg: "파일이 존재합니다. 다운로드하지 않습니다."
      when: shkim_check.stat.exists

    - name: wordpress file download
      get_url:
        url: https://ko.wordpress.org/wordpress-7.0.1-ko_KR.tar.gz
        dest: /root/
      when: not shkim_check.stat.exists

```
###### `word.yml`
```yml
---
- name: test command module
  hosts: db
  gather_facts: true
  ignore_errors: true


  tasks:
    - name: command test
      command: cat /etc/passwd
      register: shkim_db_passwd

    - name: print/etc/passwd
      debug:
        msg: "{{ shkim_db_passwd }}"

    - name: print ansible_facts1
      debug:
        var: ansible_facts['distribution']

    - name: print ansible_facts2
      debug:
        var: ansible_facts['all_ipv4_addresses']
```
##### `mysql` 모듈
###### `mysql.yml`
```yml
---
- name: mysql
  hosts: db
  gather_facts: true
  ignore_errors: true
  vars:
    m: mysql

  tasks:
  - name: "install {{ m }}"
    dnf:
      name: "{{ item }}"
      state: latest
    loop:
      - mysql-server
      - python3-PyMySQL

  - name: "start {{ m }}"
    systemd:
      name: mysqld
      state: started

  - name: user create
    mysql_user:
      login_user: root
      user:
        - root
        - shkim
      password: It12345!
      host: '%'
      priv: '*.*:ALL'
      state: present

  - name: db create word
    mysql_db:
      login_user: root
      name: word
      state: present

  - name: firewall open
    firewalld:
      port: 3306/tcp
      immediate: true
      permanent: true
      state: enabled
```