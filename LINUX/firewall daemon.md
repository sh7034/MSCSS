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