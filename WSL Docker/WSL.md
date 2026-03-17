### WSL
- Windows Subsytem Linux
- 가상머신이 호스트 컴퓨터 속 격리된 환경에서 구동되는 것과 달리, WSL은 윈도우와 리눅스가 파일을 공유하면서 각자의 커널이 모두 구동되고 있다.
- 두 OS가 각자의 셸을 실행할 수 있으며 독자적인 파일 시스템을 통해 마운트를 한다.

|            | Windows         | Linux     |
| ---------- | --------------- | --------- |
| Shell      | cmd, powershell | bash      |
| 파일 시스템     | NTFS            | ext 4     |
| 경로         | `\\wsl$`        | `/mnt/c/` |
| 사용자 계정 식별자 | SID             | UID       |

##### 리눅스 배포판 이미지

WSL을 통해 리눅스 OS를 설치해야 사용할 수 있다.
설치를 위해 이미지 파일을 온라인으로 다운로드받거나 가지고 있는 이미지 파일을 WSL에 임포트하여 사용한다.
가상머신 방식과 달리 별도의 Install Wizard가 필요하지 않다.
```powershell
# 온라인에서 배포판 검색
wsl --list --online
# 온라인에서 배포판 실행
wsl --install Ubuntu

# 배포판 .tar 파일 임포트
wsl --import <Distro> <InstallLocation> <FileName> [Options]
# 지정된 tar 파일을 새 배포로 가져옵니다. 파일 이름은 - for stdin 일 수 있습니다.
# 옵션:
  --version <Version>
# 새 배포에 사용할 버전을 지정합니다.
  --vhd
# 제공된 파일이 tar 파일이 아닌 .vhdx 파일임을 지정합니다. 이 작업은 지정된 설치 위치에 .vhdx 파일의 복사본을 만듭니다.
```
##### 배포판 관리
```powershell
# 배포판 목록 및 WSL 버전 정보
wsl -l -v
# 기본 실행할 배포판 설정
wsl --set-default Ubuntu
# WSL 버전 변경
wsl --set-version Ubuntu 2 
```

##### 배포판 제어
```powershell
# 특정 배포판 실행
wsl -d Ubuntu
# 특정 배포판 종료
wsl --terminate Ubuntu
# WSL 전체 종료
wsl --shutdown

```