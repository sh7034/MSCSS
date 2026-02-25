IDM 서버
##### 사용자
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
###### 클라이언트 등록
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
##### 명령어
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