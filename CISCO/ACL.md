##### Access Control List
라우터에 입출력되는 패킷을 통과시킬지 폐기할지 정해둔 규칙 목록

##### ACL 정보 확인
```
show ip access-lists
show ip interface [인터페이스 이름]
show access-lists
```
##### 표준 ACL
1 - 99 번호를 사용한다.
```
conf t

# 표준 ACL 문장 정의
access-list 10 deny 192.168.2.0 0.0.0.255
# access-list [ACL번호] [permit/deny] [발신지IP] [발신지WM]
access-list 10 permit any


# 인터페이스에 ACL을 적용
int fa 0/0
ip access-group 10 out
# ip access-group [ACL번호] [in/out]
```
`host 192.168.1.10`은 이 IP주소 하나만 지정한다. `192.168.1.10 0.0.0.0`과 동일하다.
`any`는 `0.0.0.0 255.255.255.255`와 동일하다.
##### 확장 ACL
100 - 199 번호를 사용한다.
발신지 주소, 목적지 주소, 프로토콜, 서비스(포트)를 정할 수 있다.
```
conf t
# 확장 ACL 문장 정의
access-list 110 deny tcp 192.168.1.0 0.0.0.255 192.168.2.0 0.0.0.255 eq 23
# access-list [ACL번호] [permit/deny] [프로토콜] [발신지IP] [발신지WM] [수신지IP] [수신지WM] [eq/gt/lt] [포트] 

# 인터페이스에 ACL을 적용
int s 0/0/1
ip access-group 110 in
# ip access-group [ACL번호] [in/out]
```

icmp 등의 프로토콜을 사용한다면 포트 번호 자리에 type을 입력
##### Named ACL

```
ip access-list extended icmp_deny_1./8net

remark deny ping out
deny icmp 192.0.1.0 0.0.0.255 1.0.0.0 0.255.255.255 echo
permit ip any any

# 인터페이스에 ACL을 적용
int s 0/0/1
ip access-group icmp_deny_1./8net
```

NACL은 각 문장에 자동으로 10 간격으로 순서번호를 부여한다.

```
ip access-list extended icmp_deny_1./8net

# 문장 끼워넣기
15 permit udp any any
# 문장 지우기
no 20
```