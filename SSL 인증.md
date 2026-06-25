- 종단 간 신뢰성 확보
- 암호화 통신

#### 공인인증서
`C:\Program Files\NPKI`
Root CA: KISA

사이트 비밀키 A'
사이트 공개키 A

인증기관 비밀키 B'
인증기관 공개키 B

사용자 대칭키 C

1. `사이트 ----- [사이트정보 + A] -----> 인증기관`
2. `인증기관: [사이트정보 + A] ----- [B'로 암호화] -----> 인증서`
3. `인증기관 ----- [사이트정보 + A] -----> 사이트`
4. `인증기관 ----- [B] -----> 사용자`
   
5. `사용자 ----- [접속요청] -----> 사이트`
6. `사이트 ----- [인증서] -----> 사용자`
7. `사용자: 인증서 ----- [B로 복호화] -----> [사이트정보 + A]`
   
8. `사용자: C ----- [A로 암호화] -----> 사이트에 전송`
9. `사이트: 암호화된 C ----- [A'로 복호화] -----> C 획득`
10. `C를 이용해 암호화 통신`

### openssl
```sh
# 인증키
openssl genrsa -out ca.key 2048
# 요청서
openssl req -new -key ca.key -out ca.csr
openssl x509 -req -days 365 -in ca.csr -signkey ca.key -out ca.crt

```

### openssl 사설 인증서 CA등록
```sh
#인증키 생성
openssl genrsa -out rootCA.key 2048
openssl genrsa -out shkim.key 2048
#요청서 생성
openssl req -new -key rootCA.key -config root.cnf -out rootCA.csr
openssl req -new -key shkim.key -config shkim.cnf -out shkim.csr
```
- `root.cnf`
```vim
[req]
default_bits = 2048
prompt = no
default_md = sha256
distinguished_name = dn
req_extensions = v3_req

[dn]
C = KR
ST = gyeonggi-do
L = paju-si
O = shkim
OU = edu
emailAddress = shkim@shkim.shop
CN = root

[v3_req]
subjectAltName = @alt_names

[alt_names]
DNS.1 = *.*.local
```
- `shkim.cnf`
```vim
[req]
default_bits = 2048
prompt = no
default_md = sha256
req_extensions = v3_req
distinguished_name = dn

[dn]
C = KR
ST = seoul
L = gwanak-gu
O = shkim
OU = edu
emailAddress = shkim@shkim.shop
CN = shkim

[ v3_req ]
subjectAltName = @alt_names

[alt_names]
DNS.1 = *.shkim.shop
```

```sh
# 인증서 생성
openssl x509 -req -in rootCA.csr -signkey rootCA.key -out rootCA.crt -extfile root.cnf -extensions v3_req
openssl x509 -req -in shkim.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out shkim.crt -extfile shkim.cnf -extensions v3_req cat rootCA.crt shkim.crt > rootsd.crt
# 체인 파일
cat rootCA.crt shkim.crt > rootsd.crt

cp shkim.crt rootCA.crt rootsd.crt /etc/pki/tls/certs
cp shkim.key rootCA.key /etc/pki/tls/private

vi /etc/httpd/conf.d/ssl.conf

firewall-cmd --add-port {80,443}/tcp
```

```powershell
scp root@10.0.0.11:/root/SSL/rootCA.crt ./
```