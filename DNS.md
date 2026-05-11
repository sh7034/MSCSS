
Domain Name Service

dnf의 `bind` 패키지, `bind-utils`,`bind-libs`를 활용한다.

#### `/etc/named.conf`

#### `/etc/named.rfc1912.zones`
zone 파일과 역방향 zone 파일을 정의해 준다.
```
zone "shkim.local" IN {
        type master; 
        file "1";
        allow-update { none; };
};
zone "0.0.10. in-addr.arpa" IN {
        type master; 
        file "2";
        allow-update { none; };
};

```

#### zone 파일 `1`

```
$TTL 1D
@       IN SOA  shkim.local. web. (
                                        26050701        ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        NS      ns1.shkim.local.
        NS      ns2.shkim.local.
        MX 10   mx1.shkim.local.
        A       10.0.0.11
        A       10.0.0.12
        A       10.0.0.13
www     A       10.0.0.11
www     A       10.0.0.12
www     A       10.0.0.13
ftp     A       10.0.0.12
ns1     A       10.0.0.11
ns2     A       10.0.0.12
mx1     A       10.0.0.13
blog    A       10.0.0.11
blog    A       10.0.0.12
intra   A       10.0.0.12
intra   A       10.0.0.13


```

#### 역방향 zone 파일 2
```

$TTL 1D
@       IN SOA ns1.shkim.local. web. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        NS      ns1.shkim.local.
        NS      ns2.shkim.local.

11      IN      PTR     ns1.shkim.local.
12      IN      PTR     ns2.shkim.local.


```