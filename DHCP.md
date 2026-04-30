### Dynamic Host Configuration Protocol
- IP 주소 자동할당
- IP 자원 효율적 관리

#### 포트
- Protocol: UDP
- Port: 67 (server), 68 (client)

## 동작 구조(DORA)
#### Broadcast
1. Discover: DHCP client가 DHCP server를 찾는 메시지를 Network 전체에 전송
2. Offer: server가 client에게 제안: IP Address, Subnet Mask, Server Address, Lease Time, (DNS, Gateway)
#### Unicast
3. Request: 클라이언트가 서버에게 해당 IP 사용여부를 재확인
4. ACK: 클라이언트가 서버에게 최종 서비스할 IP Address, Subnet Mask, Server Address, Lease Time, (DNS, Gateway)
5. 1/2


## DHCP 서버 구축 예제
- 네트워크: 10.0.0.0 - 10.0.0.255
- server 30대 운영중: 10.0.0.1 - 10.0.0.30
- GW : 10.0.0.254
- DHCP server: 10.0.0.11
- 임대시간: 2시간 - 4시간
- DNS: kornet(168.126.63.1), google(8.8.8.8)

## DHCP 서버 구축 예제
- 네트워크: 10.0.0.0 - 10.0.0.255
- server 40대 운영중: 10.0.0.1 - 10.0.0.41
- GW : 10.0.0.254
- DHCP server: 10.0.0.12
- 임대시간: 1시간 - 1시간
- DNS: google(8.8.8.8), kornet(168.126.63.1)
- domain name: 이니셜.local


## 리눅스 DHCP 서버
```bash
dnf install -y dhcp-server
```
- /etc/dhcp/dhcpd.conf
```
subnet 10.0.0.0 netmask 255.255.255.0 {
  range 10.0.0.31 10.0.0.250;
  option domain-name-servers 168.126.63.1,8.8.8.8;
  option domain-name "shkim.local";
  option routers 10.0.0.254;
#  option broadcast-address 10.5.5.31;
  default-lease-time 3600;
  max-lease-time 3600;
}
host w10 {
  hardware ethernet 00:00:00:00:00:01;
  fixed-address 10.0.0.101;
}
host w11 {
  hardware ethernet 00:00:00:00:00:02;
  fixed-address 10.0.0.201;
}
```

```
systemctl enable --now dhcpd
```
## 윈도우 DHCP 서버
### 서버 관리자
[관리]-[역할 및 기능 추가]-[서버 역할]-[DHCP 서버]
[도구]-[DHCP]-[IPv4]-[새 범위]