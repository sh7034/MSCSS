```powershell
#host

ssh-keygen -b 2048 -m PEM -t rsa -q -N ""
scp .ssh\id_rsa.pub root@10.0.0.11:/root/.ssh/authorized_keys

scp .ssh\id_rsa root@10.0.0.11:/root/.ssh/
```

```sh
# bastion

scp .ssh/authorized_keys root@172.16.0.21:/root/.ssh/
scp .ssh/authorized_keys root@172.16.0.22:/root/.ssh/
chmod 600 /root/.ssh/id_rsa
echo "net.ipv4.ip_forward = 1" > /etc/sysctl.d/ip_forward.conf

firewall-cmd --permanent --direct --add-rule ipv4 nat POSTROUTING 0 -o ens160 -j MASQUERADE
firewall-cmd --permanent --direct --add-rule ipv4 filter FORWARD 0 -i ens192 -o ens160 -j ACCEPT
```

```sh
# web

dnf install -y httpd
echo $HOSTNAME > /var/www/html/index.html

sed -i "s/Listen 80/Listen 65080/g" /etc/httpd/conf/httpd.conf
systemctl enable --now httpd
firewall-cmd --permanent --add-port=65080/tcp
firewall-cmd --reload

```

```sh
# haproxy

dnf install -y haproxy
vi /etc/haproxy/haproxy.cfg

systemctl enable haproxy
firewall-cmd --reload
```