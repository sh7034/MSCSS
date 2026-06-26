# Docker
- 컨테이너 관리 툴
- 배포와 개발 환경 관리를 쉽게
- 윈도우용 도커를 WSL과 연동하여 사용하며 `docker-desktop`이라는 경량 리눅스 OS 상에 데이터를 저장
```sh
# 구버전 삭제
sudo dnf remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

# 필수 도구 설치
sudo dnf install -y dnf-plugins-core

# Docker 공식 저장소 추가
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Docker 서비스 시작
sudo systemctl start docker

# 부팅 시 자동 시작 설정
sudo systemctl enable docker

sudo systemctl status docker

# 현재 사용자를 docker 그룹에 추가
sudo usermod -aG docker $USER

# 변경된 권한 적용 (로그아웃 후 다시 로그인하거나 아래 명령 실행)
newgrp docker

docker run hello-world

docker login -u MYDOCKER -p <토큰_또는_비밀번호>
docker login -u sh7034 -p dckr_pat_wCAmp_3_hnfRyjEY7mwOFcybjUY
```

### 컨테이너
- 도커에서 실행할 수 있는 기본 단위
```powershell
# 생성-연결-시작
docker run -it --name <컨테이너 이름> <이미지 이름>
docker run -d --name webtest -p 8080:80 nginx
# 이미 실행 중인 컨테이너에 새로운 명령(bash) 실행 (기존 프로세스와 별개 프로세스로 실행)
docker exec -it <컨테이너 이름> bash
# 컨테이너 프로세스 확인
docker ps

```

```powershell
# 생성
docker create <컨테이너 이름>
# 시작
docker start <컨테이너 이름>
# 컨테이너 터미널을 내 터미널에 연결
docker attach <컨테이너 이름>
Ctrl + P + Q
# 정지(종료)
docker stop <컨테이너 이름>
# 재시작
docker restart <컨테이너 이름>
# 삭제
docker rm <컨테이너 이름>
# 모든 컨테이너 목록을 받아 일괄 강제삭제
docker rm -f $(docker ps -aq)
# 호스트의 파일 컨테이너에 복사해 넣기
docker cp <파일> <컨테이너 이름>:<경로>
# 로그확인
docker logs <컨테이너 이름>
```

- `docker run -t`: tty, 컨테이너에 터미널을 할당해 줌
- `docker run -i`: interactive, 상호작용 (stdin)
- `docker run -d`: detach, 컨테이너의 터미널을 내 터미널에서 분리(백그라운드화)
- `docker run -it` 또는 `docker run -itd`로 실행
###### 도커 컨테이너의 데이터 구성
- **[[Docker#이미지|이미지]] (Read-Only / 수정 불가):**
    - **역할:** 컨테이너의 '뿌리'이자 '설계도'.
    - **특징:** 운영체제 환경, 라이브러리, 실행 파일 등 변하지 않는 데이터들. 컨테이너가 아무리 많아도 이 원본 데이터는 하나만 존재하며 공유된다.
- **[[Docker#볼륨|볼륨]] & 바인드 마운트 (Persistent / 외부 연결):**
     - **역할:** 컨테이너 '외부'에 있는 데이터 창고.
     - **특징:** DB의 데이터나 로그, 내가 편집 중인 소스 코드. 실시간 미러링 방식이라 호스트나 컨테이너 어느 쪽에서 수정해도 즉시 반영되며, **컨테이너가 사라져도 유일하게 살아남는 데이터**이다.
- **컨테이너 레이어 (Writable / 증분 기록):**
    - **역할:** 컨테이너가 켜져 있는 동안 발생하는 '임시 낙서장'.
    - **특징:** 이미지(1번)와 다른 점만 기록하는 **CoW(Copy-on-Write)** 계층. "이미지에 있는 파일을 삭제함", "새로운 임시 파일을 생성함" 같은 정보만 담기며, 컨테이너 삭제 시 함께 파기된다.
###### Copy-on-Write
- 컨테이너는 반드시 이미지를 참조함
- 컨테이너만의 수정 사항(이미지와 차이나는 데이터)은 **컨테이너 레이어**에 증분 백업 방식처럼 저장함

### 이미지
- 도커에서 고정된 데이터(수정불가)
- 수정 대신 증분 기록 형식으로 버전 관리만 가능
- 컨테이너는 이미지에 의존적. 컨테이너가 이미지를 사용 중이라면 해당 이미지는 삭제 불가
- OS의 모든 것을 포함하지는 않고, 작업에 필요한 소수 데이터만 저장함

| **포함되는 것 (응용프로그램 실행용)**              | **포함되지 않는 것 (OS 핵심)**           |
| ------------------------------------ | ------------------------------- |
| **코드:** 내가 작성한 프로그램 파일               | **커널:** 하드웨어를 직접 제어하는 OS의 심장    |
| **런타임:** Python, Node.js, Java 설치 환경 | **드라이버:** 그래픽 카드, 네트워크 카드용 드라이버 |
| **라이브러리:** 실행에 필요한 `.so`나 `.dll` 파일  | **부트 로더:** 컴퓨터를 켤 때 필요한 시스템 파일  |
| **환경 변수:** 설정값, 경로(Path) 정보          |                                 |
```powershell
# 도커 허브 등 온라인 저장소 이미지 로컬로 다운로드받기
docker pull <이미지>:<버전>
# 내 온라인 저장소에 로컬 이미지를 업로드
docker push <저장소>/<이미지>:<버전>

# 도커 이미지 ↔️ .tar 암축파일
docker load -i my_image.tar
docker save -o my_image.tar ubuntu:22.04

# 도커 컨테이너(스냅샷) ↔️ .tar 암축파일
# 파일들만 가지고 이미지 생성. 환경변수, 부팅 명령어 등이 누락되므로 백업용으로 사용
docker import -o my_container.tar my_ubuntu_container
docker export my_container.tar my_new_image:1.0
docker import --change "CMD ["/bin/bash"]" my_snapshot.tar my_new_image:1.0

# 이미지 생성(빌드)
docker build -t <이미지 이름> <경로>
# 컨테이너를 로컬 이미지로 내보내기
docker commit h1 sh7034/web:h1.0
# 커밋한 이미지를 원격 저장소로 푸시
docker push sh7034/web:h1.0
# 이미지 목록
docker images
# 이미지 삭제
docker rmi --force <이미지 이름>
# 도커 이미지나 컨테이너의 모든 세부 정보(설정값)를 JSON 형식으로 출력
docker inspect

```
###### 도커 이미지 생성 방법
- `docker bake` 파일을 직접 이미지로 굽기
- [[Dockerfile]]: 생성하려는 이미지의 기본 정보를 담은 스크립트
- [[Docker Compose]]: `compose.yaml` 파일 형식으로 다중 컨테이너 애플리케이션을 한 번에 정의하고 실행
### 볼륨 & 바인드 마운트
컨테이너 외부(WSL 또는 Windows)에 마운트된 별개 데이터(수정가능)
```powershell
# 볼륨 생성
docker volume create <볼륨 이름>

# 컨테이너 생성 시 볼륨을 마운트
# 권장, <key>=<value> 형식
# 존재하지 않는 볼륨 지정 시 에러
docker run --name <컨테이너 이름> -it --mount source=<볼륨>,target=<컨테이너 속 마운트할 경로> <이미지 이름> bash
# 구식, <소스>:<타겟>:<옵션> 형식
# 존재하지 않는 볼륨을 지정하면 자동으로 빈 볼륨 생성
docker run -itd -v <볼륨 이름>:<컨테이너 속 마운트할 경로> --name <컨테이너 이름> <이미지 이름>
# 볼륨 소스는 볼륨 이름(www-vol)

# 바인드 마운트
# 호스트PC 절대경로(/www-vol로 입력하면
docker run -itd -v <호스트 경로>:<컨테이너 속 마운트할 경로> --name <컨테이너 이름> <이미지 이름>

# 볼륨 확인
docker volume ls
docker volume inspect <볼륨 이름>

# 볼륨 삭제
docker volume rm <볼륨 이름>
```

