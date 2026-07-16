Ansible의 공식 콘텐츠 공유 허브이자 패키지 관리 도구(CLI)

단일 야믈(YAML) 파일로 작성된 플레이북(Playbook)은 소규모 단발성 자동화에는 유용하지만, 인프라 규모가 커지고 복잡해질수록 한계에 직면한다.
이미 검증된 전문가의 Role을 가져와 변수만 주입해 실행한다.

- **Playbook:** "어떤 호스트에서 어떤 역할을 실행할 것인가"만 정의하는 껍데기 역할
- **Role/Collection:** 실제 동작을 수행하는 세부 코드 뭉치 (입력 변수, 템플릿, 핸들러 분리

- Infrastructure as Code (IaC)의 패키징 표준화
  Ansible은 자동화 코드가 "문서처럼 읽혀야 한다(Read like documentation)"는 철학을 가집니다. Galaxy는 이 자동화 코드를 독립적이고 유통 가능한 하나의 '패키지 소프트웨어'로 취급하여 배포하는 표준 아키텍처를 제시합니다.
- "선언적 추상화" (Declarative Abstraction)
  사용자는 내부적인 인프라 제어 프로세스(명령어, 절차)를 알 필요 없이, "나는 이 사양의 Nginx를 원한다"라고 선언만 하면 되도록 하위 구현체를 은닉(Encapsulation)하는 컨셉을 지향합니다.
- 느슨한 결합 (Loose Coupling)
  인프라 구성 요소를 상호 독립적인 마이크로서비스처럼 분리하여 관리합니다. 필요에 따라 부품을 조립하듯이 플레이북에 꽂아 넣을 수 있도록 설계되었습니다.

##### `ansible-galaxy role init apache`
###### `defaults/main.yml`
```yml
---
# defaults file for apache
web: nginx
```
###### `files/index.html`
```yml
<html>
        <body>
                <h1>ANSIBLE-GALAXY-FILE</h1>
        </body>
</html>
```
###### `handlers/main.yml`
```yml
---
# handlers file for apache
- name: started http
  service:
    name: httpd
    state: started

- name: restarted http
  service:
    name: httpd
    state: restarted
```
###### `meta/main.yml`
```yml
galaxy_info:
  author: sdkim
  description: rockylinux9 install apache
  company: sdkim (optional)

  # If the issue tracker for your role is not on github, uncomment the
  # next line and provide a value
  # issue_tracker_url: http://example.com/issue/tracker

  # Choose a valid license ID from https://spdx.org - some suggested licenses:
  # - BSD-3-Clause (default)
  # - MIT
  # - GPL-2.0-or-later
  # - GPL-3.0-only
  # - Apache-2.0
  # - CC-BY-4.0
  license: license (GPL-2.0-or-later, MIT, etc)

  min_ansible_version: 2.1

  # If this a Container Enabled role, provide the minimum Ansible Container version.
  # min_ansible_container_version:

  #
  # Provide a list of supported platforms, and for each platform a list of versions.
  # If you don't wish to enumerate all versions for a particular platform, use 'all'.
  # To view available platforms and versions (or releases), visit:
  # https://galaxy.ansible.com/api/v1/platforms/
  #
  # platforms:
  # - name: Fedora
  #   versions:
  #   - all
  #   - 25
  # - name: SomePlatform
  #   versions:
  #   - all
  #   - 1.0
  #   - 7
  #   - 99.99

  galaxy_tags: []
    # List tags for your role here, one per line. A tag is a keyword that describes
    # and categorizes the role. Users find roles by searching for tags. Be sure to
    # remove the '[]' above, if you add tags to this list.
    #
    # NOTE: A tag is limited to a single word comprised of alphanumeric characters.
    #       Maximum 20 tags per role.

dependencies: []
  # List your role dependencies here, one per line. Be sure to remove the '[]' above,
  # if you add dependencies to this list.
```
###### `tasks/main.yml`
```yml
---
# tasks file for apache
- name: print message install apache
  debug:
    msg: "Rockylinux9에 {{ web }} 설치를 시작합니다."

- name: install apache
  dnf:
    name: "{{ web }}"
    state: latest
  notify:
    - started http

- name: index.html file copy
  copy:
    src: index.html
    dest: /var/www/html/
    owner: apache
    group: apache

- name: template copy
  template:
    src: httpd.j2
    dest: /etc/httpd/conf/httpd.conf
    owner: apache
    group: apache
  notify:
    - restarted http

- name: firewall open
  firewalld:
    port: 80/tcp
    immediate: true
    permanent: true
    state: enabled
```
###### `templates/httpd.j2`
```yml
ServerRoot "/etc/httpd"
Listen 80
Include conf.modules.d/*.conf
User apache
Group apache
ServerAdmin root@localhost
#ServerName www.example.com:80
<Directory />
    AllowOverride none
    Require all denied
</Directory>
DocumentRoot "/var/www/html"
<Directory "/var/www">
    AllowOverride None
    # Allow open access:
    Require all granted
</Directory>
<Directory "/var/www/html">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
<Files ".ht*">
    Require all denied
</Files>
ErrorLog "logs/error_log"
LogLevel warn
<IfModule log_config_module>
    LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
    LogFormat "%h %l %u %t \"%r\" %>s %b" common
    <IfModule logio_module>
      LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" %I %O" combinedio
    </IfModule>
    CustomLog "logs/access_log" combined
</IfModule>
<IfModule alias_module>
    ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"
</IfModule>
<Directory "/var/www/cgi-bin">
    AllowOverride None
    Options None
    Require all granted
</Directory>
<IfModule mime_module>
    TypesConfig /etc/mime.types
    #AddType application/x-gzip .tgz
    #AddEncoding x-compress .Z
    #AddEncoding x-gzip .gz .tgz
    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz
    #AddHandler cgi-script .cgi
    #AddHandler type-map var
    AddType text/html .shtml
    AddOutputFilter INCLUDES .shtml
</IfModule>
AddDefaultCharset UTF-8
<IfModule mime_magic_module>
    MIMEMagicFile conf/magic
</IfModule>
ErrorDocument 500 "The server made a boo boo."
ErrorDocument 404 /missing.html
ErrorDocument 404 "/cgi-bin/missing_handler.pl"
ErrorDocument 402 http://www.example.com/subscription_info.html
#EnableMMAP off
EnableSendfile on
IncludeOptional conf.d/*.conf
```
###### `tests/inventory`
```yml
localhost
```
###### `tests/test.yml`
```yml
---
- hosts: localhost
  remote_user: root
  roles:
    - apache
```
###### `vars/main.yml`
```yml
---
# vars file for apache
web: httpd
```