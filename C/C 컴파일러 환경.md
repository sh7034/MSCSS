### MSYS2

소프트웨어 배포 및 개발 환경 플랫폼
윈도우에서 MSYS2를 사용하여 리눅스와 유사한 개발 환경을 사용할 수 있다.

- **패키지 관리자:** `pacman` (아치 리눅스에서 사용하는 것과 동일)  
- **셸 환경:** `bash`, `zsh` 등    
- **툴체인:** 컴파일러(GCC), 디버거(GDB), 빌드 도구(make) 등

#### 컴파일러 환경

컴파일러와 필요한 부품들이 묶인 패키지들이 설치되는 저장소
번역기(Compiler), 표준 헤더, 런타임 라이브러리가 저장된다.
- **번역기 (Compiler):** `printf`라는 코드를 기계어로 바꾼다.
- **표준 헤더 (Headers):** `stdio.h`처럼 어떤 기능을 쓸 수 있는지 적힌 명세서.
- **런타임 라이브러리 (Runtime Library):** 실제로 윈도우 화면에 글자를 뿌리는 **진짜 실행 코드**가 들어있는 핵심 부품.

| 환경          | **환경**             | **특징**                                                                              |
| ----------- | ------------------ | ----------------------------------------------------------------------------------- |
| **UCRT64**  | **최신 윈도우 표준**      | MSYS2에서 **현재 가장 권장하는 환경**. Microsoft의 최신 C 라이브러리(UCRT)를 사용하여 윈도우 10/11과 호환성이 가장 좋다. |
| **MINGW64** | **과거 표준 (MSVCRT)** | 오래된 윈도우 프로그램과의 호환성을 위해 남겨두었지만, 새로 시작한다면 UCRT64를 쓰는 게 유리하다.                          |
| **CLANG64** | **Clang/LLVM**     | GCC 대신 `Clang`이라는 다른 컴파일러 엔진을 쓰고 싶을 때 사용한다. 구글이나 애플이 선호하는 최신 기술 방식.                 |

#### 설치 및 사용 예시

- MSYS2 설치
```powershell
winget install MSYS2.MSYS2
```

- 설치를 완료했다면 **'시스템 환경 변수 편집'** ->`환경 변수` -> `System 변수` 중 `Path` 선택 -> `편집` -> `새로 만들기`를 눌러 `msys2.exe`가 있는 경로(`C:\msys64`)를 추가

- 내부의 패키지 관리자 `pacman`을 통해 GCC 세트를 설치
```powershell
# mingw64 사용시
msys2 -c "pacman -S --noconfirm mingw-w64-x86_64-gcc"
# ucrt64 사용시
msys2 -c "pacman -S --noconfirm mingw-w64-ucrt-x86_64-gcc"
```

- 설치를 완료했다면 **'시스템 환경 변수 편집'** ->`환경 변수` -> `System 변수` 중 `Path` 선택 -> `편집` -> `새로 만들기`를 눌러 `gcc.exe`가 있는 경로(`C:\msys64\ucrt64\bin`)를 추가

- 윈도우 환경 변수의 변경사항은 리눅스와 달리 사용하고자 하는 프로그램(powershell, VS Code 등)을 종료 후 다시 실행해야 적용된다. 다만 아래 명령으로 강제로 불러오는 것도 가능하다.
```powershell
# 현재 시스템의 Path를 다시 읽어와서 현재 세션에 강제로 적용
$env:Path = [System.Environment]::GetEnvironmentVariable("Path", "Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path", "User")
```

- 설치확인
```powershell
# 컴파일러 버전이 출력됨
gcc --version
```