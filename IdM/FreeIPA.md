IdM 서버는 주로 인증관리와 DNS의 기능을 겸한다.
IdM 서버가 관리하는 컴퓨터들은 호스트로, 사용자 계정은 사용자로 관리된다.
IdM 서버는 어떤 사용자가 어떤 호스트에 접속하여 어떤 작업을 할 수 있는지를 통제하기 위해 sudo 룰과 HBAC 룰을 사용한다.

##### dnf 패키지 설치
```bash
dnf install -y ipa-server # IdM 서버
dnf install -y ipa-server-dns # DNS 서버

dnf install -y ipa-client # IdM 클라이언트
```
##### 사용자 계정
```bash
ipa user-add \
  --first=[이름] \ # 필수
  --last=[성] \ # 필수
  --passsword='[비밀번호]' # 필수
ipa group-add [그룹]
ipa group-add-member [사용자 그룹] --users={[사용자]}
```
##### 호스트
```bash
ipa host-add [호스트]
# 호스트 이름은 ipa-client-install 과정에서 등록했으므로 일반적으로는 필요없음
ipa hostgroup-add [호스트 그룹]
ipa hostgroup-add-member [호스트 그룹] --sudocmds={[호스트 경로]}
```
###### 클라이언트 등록(호스트 장치에서)
```bash
sudo ipa-client-install \
  --domain=kcci.edu \
  --realm=KCCI.EDU \
  --server=idm.kcci.edu \
  --principal=admin \
  --password='Tj0tlf.0505' \
  --hostname=web.kcci.edu \
  --mkhomedir \
  --unattended
```
##### sudo 룰 명령어
```bash
ipa sudocmd-add [명령어 경로]
ipa sudocmdgroup-add [명령어 그룹]
ipa sudocmdgroup-add-member [명령어 그룹] --sudocmds={[명령어 경로]}
```
##### sudo 룰
```bash
ipa sudorule-add [룰]
ipa sudorule-add-user [룰] --groups={[사용자 그룹]}
ipa sudorule-add-host [룰] --hostgroups={[호스트 그룹]}
ipa sudorule-add-allow-command [룰] --groups={[명령어 그룹]}
```
##### NOPASSWD 옵션
```bash
ipa sudorule-add-option sr-all-helpdesk-log-nopw --sudooption='!authenticate'
```

##### HBAC 룰
```bash
ipa hbacrule-add [룰]
ipa hbacrule-add-user [룰] --users={[사용자]} --groups={[사용자 그룹]}
ipa hbacrule-add-host [룰] --hosts={[호스트]} --hostgroup={[호스트 그룹]}
ipa hbacrule-add-service [룰] --hbacsvcs={[서비스]} --hbacsvcgroup={[서비스 그룹]}

```