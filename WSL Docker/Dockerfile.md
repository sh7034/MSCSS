도커 이미지를 빌드하기 위한 스크립트

### 명령어
`{Dockerfile}FROM` 기본 이미지
`{Dockerfile}COPY` 이미지에 포함시킬 추가 파일
`{Dockerfile}ENV` 환경변수
`{Dockerfile}WORKDIR` 홈 디렉터리
`{Dockerfile}USER` 기본 사용자
`{Dockerfile}EXPOSE` 개방할 포트
`{Dockerfile}RUN` 빌드 과정 중 실행할 명령
`{Dockerfile}CMD` 빌드 완료 후, 컨테이너 시작 시 실행할 명령 (`docker run` 에 명령어 사용시 덮어씌워짐)
`{Dockerfile}ENTRYPOINT` 컨테이너 시작 시 실행할 고정 실행 명령
### 예시
```Dockerfile
FROM ubuntu:24.04
ENV DEBIAN_FRONTEND=noninteractive
RUN apt update && apt install -y \
    gcc \
    make \
    libc6-dev \
    vim \
&& rm -rf /var/lib/apt/lists/*
WORKDIR /src
COPY hello.c .
RUN gcc hello.c -o hello
CMD ["./hello"]
```

```Dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py ./
EXPOSE 5000
CMD ["python", "app.py"]
```

```Dockerfile
FROM python:3.12-slim
WORKDIR /app
RUN useradd -m appuser
COPY --chown=appuser:appuser app.py .
RUN pip install flask
USER appuser
EXPOSE 5000
CMD ["python", "app.py"]
```

```Dockerfile
FROM ubuntu:24.04 AS builder
RUN apt update && apt install -y gcc libc6-dev
WORKDIR /build
COPY hello.c .
RUN gcc -o hello hello.c

FROM ubuntu:24.04
WORKDIR /app
COPY --from=builder /build/hello .
CMD ["./hello"]
```