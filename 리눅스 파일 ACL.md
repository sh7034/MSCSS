rwx 권한을 소유주/그룹/기타사용자 기준으로 부여하는 대신 개별 사용자에 대해 권한 부여
```sh
getfacl a.txt
# usera라는 사용자에게 a.txt 파일에 대한 r-- 권한을 부여
setfacl -m u:usera:r a.txt
```