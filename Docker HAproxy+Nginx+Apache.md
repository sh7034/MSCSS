
## 아키텍처
![[Docker-HAproxy.drawio 2.png]]
# Load Balancer

```sh
dnf install -y haproxy
vi /etc.haproxy/haproxy.cfg
```

```sh
# 첫 번째 프론트엔드: 10.0.0.14:80

frontend shkim
    bind *:80
    default_backend             app     

# Nginx 백엔드로 연결: 192.168.0.12:65080, 192.168.0.12:65180

backend app
    balance     roundrobin
    server  app1 192.168.0.12:65080 check
    server  app2 192.168.0.12:65180 check

#---------------------------------------------------------------------
# 두 번째 프론트엔드: 10.0.0.14:8080

frontend shkim1
    bind *:8080
    default_backend             app1

# Apache 백엔드로 연결: 192.168.0.13:65080, 192.168.0.13:65180

backend app1
    balance     roundrobin
    server  app1 192.168.0.13:65080 check
    server  app2 192.168.0.13:65180 check
```

```sh
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --permanent --add-port=8080/tcp
firewall-cmd --relad
systemctl start haproxy
```

## Nginx Server

```sh
docker run -itd -p 65080:80 --name n1 nginx
docker run -itd -p 65180:80 --name n2 nginx
docker cp index1.html n1:/usr/share/nginx/html/index.html
docker cp index2.html n2:/usr/share/nginx/html/index.html
firewall-cmd --permanent --add-port={65080,65180}/tcp
firewall-cmd --reload
```

## Apache Server

```sh
docker run -itd -p 65080:80 --name h1 httpd
docker run -itd -p 65180:80 --name h2 httpd
docker cp index1.html h1:/usr/local/apache2/htdocs/index.html
docker cp index2.html h2:/usr/local/apache2/htdocs/index.html
firewall-cmd --permanent --add-port={65080,65180}/tcp
firewall-cmd --reload
```
