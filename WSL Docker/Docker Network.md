## 기본 인터페이스: `docker0`
호스트에 생성되는 도커 전용 마스터 브릿지 인터페이스
IP 주소는 172.17.0.1/16

```sh
# 컨테이너의 인터페이스들
docker exec a2 ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0@if43: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP 
    link/ether 3a:3d:c9:8d:45:66 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.3/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```

```sh
# 호스트의 인터페이스들
ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:49:f4:c3 brd ff:ff:ff:ff:ff:ff
    altname enp3s0
    inet 10.0.0.11/24 brd 10.0.0.255 scope global noprefixroute ens160
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fe49:f4c3/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 32:ed:3d:71:e0:18 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::30ed:3dff:fe71:e018/64 scope link 
       valid_lft forever preferred_lft forever
42: vethb5a3281@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether 8a:e1:45:da:a6:24 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::88e1:45ff:feda:a624/64 scope link 
       valid_lft forever preferred_lft forever
43: vethd3269fa@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether 22:08:2b:63:f3:89 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::2008:2bff:fe63:f389/64 scope link 
       valid_lft forever preferred_lft forever
```

컨테이너 인터페이스 이름 = 호스트 인터페이스 번호
호스트 인터페이스(마스터는 `docker0` 또는 지정한 인터페이스) 이름 = 컨테이너 인터페이스 번호

## bridge 인터페이스
NAT처럼 동작
```sh
docker network ls
docker network create --subnet 192.168.0.0/24 --gateway 192.168.0.254 <네트워크이름>
```
`docker run --net <네트워크이름>` 옵션을 이용해 컨테이너 생성 시 브릿지를 지정 가능

## host 인터페이스
네트워크 격리되지 않음
호스트 컴퓨터의 네트워크와 IP주소를 그대로 공유
포트 공유 불가능
NAT와 같은 포트포워딩 없음
방화벽 자동 허용이 되지 않았으므로 firewalld에서 수동으로 허용 필요
`docker run --network host`
`-p` 옵션 없이 외부에 배포됨
## none 인터페이스
NIC를 생성하지 않음
null 드라이버 사용
`lo`만 존재

### 링크와 hosts 추가
```sh
# 링크 방식
docker run --link <컨테이너 이름>
# add-host 방식
docker run --add-host <host 이름>:<IP주소>
```
링크 형식은 