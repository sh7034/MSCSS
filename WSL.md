### WSL
- Windows Subsytem Linux
- 가상머신이 호스트 컴퓨터 속 격리된 환경에서 구동되는 것과 달리, WSL은 윈도우와 리눅스가 파일을 공유하면서 각자의 커널이 모두 구동되고 있음
- 두 OS가 각자의 셸을 실행할 수 있으며 독자적인 파일 시스템을 통해 마운트를 함.

|            | Windows         | Linux     |
| ---------- | --------------- | --------- |
| Shell      | cmd, powershell | bash      |
| 파일 시스템     | NTFS            | ext 4     |
| 경로         | `\\wsl$`        | `/mnt/c/` |
| 사용자 계정 식별자 | SID             | UID       |

