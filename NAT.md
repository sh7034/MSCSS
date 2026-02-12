##### Static NAT
* 사설ip를 써서 내부에서 나갈 때 어떤 공인ip를 쓸지 할당한다.
* 인터페이스 설정에서 해당 인터페이스가 내부인지 외부인지를 정의한다.
```
enable
configure terminal

ip nat inside source static 192.168.1.2 200.10.10.2 

interface GigabitEthernet0/0
 ip nat inside
 exit
interface Serial0/0/0
 ip nat outside
 exit
```

```
show ip nat translations
show ip nat statistics
```

##### Dynamic NAT

* access-list 명령어를 이용해 변환을 허용할 사설ip 목록을 ACL로 정의한다.
* ip nat pool 명령어를 이용해 사용할 공인ip pool을 정의한다.
* 사설ip를 지정한 ACL과 공인ip를 지정한 pool을 입력해 NAT를 설정한다.
```
enable
configure terminal
access-list 10 permit 192.168.1.0 0.0.0.127
ip nat pool natPool 200.10.10.11 200.10.10.60 netmask 255.255.255.192
ip nat inside source list 10 pool natPool

interface GigabitEthernet0/0
 ip nat inside
 exit
interface Serial0/0/0
 ip nat outside
 exit
```

##### PAT
```
enable
configure terminal
access-list 20 permit 192.168.1.128 0.0.0.127
ip nat pool natPAT 200.10.10.61 200.10.10.62 netmask 255.255.255.192

# pool 사용한 PAT
ip nat inside source list 20 pool natPAT overload

# interface의 IP 사용한 PAT
ip nat inside source list 20 interface Serial0/0/0 overload

interface GigabitEthernet0/0
 ip nat inside
 exit
interface Serial0/0/0
 ip nat outside
 exit
 end
copy running-config startup-config

```