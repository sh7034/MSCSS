Chapter 15. 가상랜(VLAN) 설정

1. 장비 기본 설정
1.1 Sw-net1 switch >>>
enable
configure terminal
hostname Sw-net1
interface Vlan1
 ip address 192.168.1.2 255.255.255.0
 no shutdown
ip default-gateway 192.168.1.1
end
copy running-config startup-config


1.1 Sw-net2 switch >>>
enable
configure terminal
hostname Sw-net2
interface Vlan1
 ip address 192.168.1.3 255.255.255.0
 no shutdown
ip default-gateway 192.168.1.1
end
copy running-config startup-config


2. 가상랜 생성/ 포트할당 / 트렁크 포트 설정
2.1 Sw-net1 switch >>>
enable
configure terminal
vlan 10
 name Group10
 exit
vlan 20
 name Group20
 exit
interface fastethernet 0/5
 switchport mode access
 switchport access vlan 10
 exit
interface fastethernet 0/10
 switchport mode access
 switchport access vlan 20
 exit
interface fastethernet 0/1
 switchport mode trunk
 end
copy running-config startup-config


2.2 Sw-net2 switch >>>
enable
configure terminal
vlan 10
 name Group10
 exit
vlan 20
 name Group20
 exit
interface fastethernet 0/5
 switchport mode access
 switchport access vlan 10
 exit
interface fastethernet 0/10
 switchport mode access
 switchport access vlan 20
 exit
interface fastethernet 0/1
 switchport mode trunk
 end
copy running-config startup-config

3. vtp 설정
3.1 Sw-net1 switch >>>
enable
configure terminal
vtp mode server
vtp domain net
vtp password netpass
vtp version 2
end
copy running-config startup-config

3.2 Sw-net2 switch >>>
enable
configure terminal
vtp mode server
vtp domain net
vtp password netpass
vtp version 2
end
copy running-config startup-config