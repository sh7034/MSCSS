Network File System
##### NFS 서버에서 Export
```sh
dnf install -y nfs-utils
chmod 777 <서버의 공유 디렉터리>
systemctl enable --now nfs-server
```
###### /etc/exports
허용할 IP 네트워크 대역에 적어 준다.
```
<서버의 공유 디렉터리>     10.0.0.0/24(rw,sync,no_root_squash)
```
- `rw`: 읽기/쓰기
- `ro`: 읽기 전용
- `sync`: 클라이언트 요청 작업을 디스크에 즉시 저장 (기본값)
- `async`: 데이터를 메모리에 먼저 쓰고 비동기적으로 디스크에 저장
- `root_squash`: 클라이언트의 root 사용자를 일반 사용자 권한으로 강하
- `no_root_squash`: 클라이언트의 root 사용자가 서버에서도 root 권한을 그대로 행사
- `all_squash`: 접속하는 모든 사용자를 일반 사용자 권한으로 통일
##### NFS 클라이언트에서 Mount
```sh
dnf install -y nfs-utils
mount -t nfs <서버의IP>:<서버의 공유 디렉터리> <클라이언트의 공유 디렉터리>
```
