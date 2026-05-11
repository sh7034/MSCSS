

#### Wordpress Server 스크립트
```bash
#! /bin/bash
# 필요 패키지
dnf install -y wget tar httpd php php-cli php-gd php-mysqlnd

# Wordpress 다운로드, 설치
wget https://ko.wordpress.org/wordpress-6.9.4-ko_KR.tar.gz
tar xvzf wordpress-6.9.4-ko_KR.tar.gz
cp -ar wordpress/* /var/www/html/

# 기본 index 파일 경로 변경
sed -i "s/DirectoryIndex index.html/DirectoryIndex index.php/g" /etc/httpd/conf/httpd.conf

# index.php 생성
cp /var/www/html/{wp-config-sample.php,wp-config.php}

# wp-config.php 수정
sed -i "s/database_name_here/wordpress/g" /var/www/html/wp-config.php
sed -i "s/username_here/shkim/g" /var/www/html/wp-config.php
sed -i "s/password_here/It12345@/g" /var/www/html/wp-config.php
sed -i "s/localhost/10.0.0.14/g" /var/www/html/wp-config.php

echo "$HOSTNAME" > /var/www/html/health.html
# httpd 실행 및 방화벽 80 포트 허용
systemctl enable --now httpd
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --reload
```

#### MySQL Server 스크립트
```bash
#! /bin/bash
dnf install -y mysql-server
systemctl enable --now mysqld

firewall-cmd --permanent --add-port=3306/tcp
firewall-cmd --reload

mysql -uroot -e "CREATE USER 'shkim'@'%' IDENTIFIED BY 'It12345@'; GRANT PRIVILEGES ON *.* To 'shkim'@'%';"
```

#### HAProxy 스크립트
- High Availability Proxy: Load Balancing과 Reverse Proxy에 특화된 소프트웨어
```bash
#! /bin/bash
dnf install -y haproxy
sed -i "s/bind *:5000/bind *:80/g" /etc/haproxy/haproxy.cfg
sed -i "s/use_backend static/use_backend app/g" /etc/haproxy/haproxy.cfg
sed -i "s/server  app1 127.0.0.1:5001/server  app1 10.0.0.12:80/g" /etc/haproxy/haproxy.cfg
sed -i "s/server  app2 127.0.0.1:5002/server  app2 10.0.0.13:80/g" /etc/haproxy/haproxy.cfg
sed -i "s/server  app3/# server  app3/g" /etc/haproxy/haproxy.cfg
sed -i "s/server  app4/# server  app4/g" /etc/haproxy/haproxy.cfg

systemctl enable --now haproxy
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --reload
```