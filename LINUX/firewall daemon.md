방화벽을 관리하는 프로세스
명령어를 이용해 조작할 수 있다.

##### ```firewall-cmd``` 명령어

```firewall-cmd --list-all```
```firewall-cmd --permanent --add-service=[서비스 이름]```
```firewall-cmd --permanent --add-port=[port id]```
```firewall-cmd --reload```
```firewall-cmd --list-port```

```
firewall-cmd --list-services

# 만약 mysql이 보이면 제거
firewall-cmd --permanent --remove-service=mysql

sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.200.21" port protocol="tcp" port="3306" accept'

sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.200.0/24" port protocol="tcp" port="3306" accept'
```


#### 허용 서비스 및 포트 파일 보기
`vi /etc/firewalld/zones/public.xml`
```
<?xml version="1.0" encoding="utf-8"?>
<zone>
  <short>Public</short>
  <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
  <service name="ssh"/>
  <service name="dhcpv6-client"/>
  <service name="cockpit"/>
  <port port="21" protocol="tcp"/>
  <port port="65000-65010" protocol="tcp"/>
  <forward/>
</zone>

```