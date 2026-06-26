### Azure Cloud

- Enta ID: On-Premise AD와 Entra Connect
- AppGW + WAF
- Bastion + Azure Key Vault: 비밀번호 저장하여 보호
- NSG (서브넷 보호)
- Azure Storage - 웹 서버에 필요한 데이터 저장
### On-Premise

- VMware Workstation + Vagrant-vmware-plugin 스택으로 On-premise PC에 다음 VM들 구현:
	- DC
	- 동기화 서버
	- 클라이언트
### 탐지 및 대응
- Sentinel 이용, 로그검토 및 정책 생성

###
아래는 그 환경을 관통하는 공격 시나리오를 MITRE ATT&CK 전술 흐름에 맞춰 구성한 것입니다. 발표용 위협 모델 수준(개념적 공격 경로)이며, 무기화된 실행 절차가 아니라 "어디를 노리고 왜 통하는가"에 초점을 둡니다.

초기 침투: Entra ID 로그인에 대한 패스워드 스프레이, 또는 AiTM 피싱으로 세션 토큰 탈취. 한 사원의 클라우드 ID 또는 워크스테이션(client01)에 대한 침투.

자격 증명 확보: 워크스테이션에서 캐시된 AD 자격 증명 수집, 리프레시 토큰 확보. 동기화·페더레이션된 계정은 온프레미스와 클라우드 양쪽에 영향을 줄 수 있으므로 양방향으로 확장 가능.

탐색·횡적 이동: AD를 열람해 브리지 계층, 즉 Entra Connect 서버(sync01)를 찾아 이동. 하이브리드 환경의 핵심 표적.

권한 상승: sync01의 디렉터리 동기화 서비스 계정은 디렉터리 복제 권한을 가진 고특권 계정. 이를 악용하면 도메인 전체 해시를 복제할 수 있어, 브리지 한 대 장악 -> 도메인 전체 장악으로 확산.

5단계 — 하이브리드 인증 백도어·지속성(Persistence). 공격자는 하이브리드 ID에 연결된 인증 과정을 변조(T1556.007)할 수 있습니다. 예컨대 PTA 에이전트가 도는 온프렘 서버를 장악해 인증 에이전트 프로세스에 악성 DLL을 주입하면 모든 인증 시도를 승인하고 자격 증명을 기록할 수 있고, 클라우드에서 전역 관리자 계정을 탈취한 경우엔 웹 콘솔에서 새 PTA 에이전트를 등록해 누구로든 로그인할 수 있습니다. 여기에 앱 등록에 추가 자격 증명을 심어(T1098.001) MFA를 우회하는 지속성을 확보합니다. [Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/what-is-cloud-sync)

6단계 — 목표 달성·영향(Impact). 위조·상승된 클라우드 ID로 보호 대상인 Azure 데이터와 앱에 접근합니다. 스토리지·DB를 클라우드 API로 읽어 유출(Data from Cloud Storage, T1530)하거나, 하이브리드 클라우드 환경으로 확장된 랜섬웨어(예: Storm-0501 패턴)로 전개합니다. 이 단계가 프로젝트 주제인 "데이터·앱 보안"과 직접 맞물립니다.