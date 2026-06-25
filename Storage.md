### On-premise Storage
##### **D**irect **A**ttached **S**torage
물리적 전송매체(IDE, SATA, SCASI)를 이용해서 Storage를 직접 연결
속도 빠름, 저렴, 안정적
원거리 구성 불가, 확장이 제한적
##### **N**etwork **A**ttached **S**torage
기존 네트워크 환경(100Mbps, 1Gbps)을 이용해서 Storage 연결
구성 용이, 저렴, 원거리 구성 가능
속도 느림, 네트워크 병목 현상 영향을 받음
##### **S**torage **A**rea **N**etwork
Storage영역에 별도 Network 구성, Fiber Channel 사용
속도 빠름(16Gbps), 안정적, 원거리 구성 가능
고비용, 구성 난해, 별도의 SAN switch card, HBA card 필요

#### Cloud Storage
##### Block Storage
HDD 형태의 가상 Storage
실제 On-premise 환경의 Block Storage와 동일하게 동작
##### Object Storage
Rest API (객체별로 URL 제공)
거의 무제한의 용량제공
접근제어 가능
버전관리 가능
S3(AWS), Blob(Azure)