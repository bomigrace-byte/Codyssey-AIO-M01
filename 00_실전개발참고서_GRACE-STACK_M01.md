# GRACE STACK Vol.01 — 개발 환경 구축 실전 참고서

> **BOMIGRACE · Practical Development Handbook**  
> Codyssey M01 | WSL2 · Ubuntu · Linux CLI · Docker · Docker Compose · Git/GitHub · SSH  
> 버전 1.0 · 2026-11-08

**이 문서는 미션 제출용 `README.md`와 별도로 보관하는, 초보자를 위한 독립형 학습·실무 참고서다.** M01에서 실제 수행한 명령어와 실습 결과를 중심으로 개념·원리·실무 활용법·안전한 복구 방법을 설명한다. 이후 M02·M03에서도 필요할 때 다시 찾아본다.

- **[M01]**: M01 가이드와 제출 README에 근거한 요구사항 또는 실제 실습.
- **[확장]**: 미션의 필수 조건은 아니지만 실무에서 유용한 보충 지식과 예시. 별도 검증 없이 실제 수행했다고 주장하지 않는다.
- **[주의]**: 파일 삭제, 데이터 손실 또는 비밀정보 노출 가능성이 있는 조작.
- **실행 환경 표기**: `[Ubuntu]`는 WSL의 Ubuntu Bash, `[Windows]`는 Windows 터미널/탐색기, `[Container]`는 컨테이너 내부를 뜻한다.

## 빠른 목차

- [처음 읽기: 5분 시작 가이드](#처음-읽기-5분-시작-가이드)
- [Part 00. 개발 환경 전체 지도](#part-00-개발-환경-전체-지도)
- [Part 01. 터미널 읽기와 경로](#part-01-터미널-읽기와-경로)
- [Part 02. Linux CLI와 파일 권한](#part-02-linux-cli와-파일-권한)
- [Part 03. Docker 실전](#part-03-docker-실전)
- [Part 04. Docker Compose와 환경변수](#part-04-docker-compose와-환경변수)
- [Part 05. Git·GitHub·SSH](#part-05-gitgithubssh)
- [Part 06. 오류 분석과 복구](#part-06-오류-분석과-복구)
- [Part 07. 상황별 명령어 사전](#part-07-상황별-명령어-사전)
- [Part 08. 복습·응용 실습](#part-08-복습응용-실습)
- [용어집·자료 기준](#용어집자료-기준)

---

## 처음 읽기: 5분 시작 가이드

**지금 하고 싶은 일이 무엇인가?**

| 상황 | 먼저 읽을 곳 | 가장 먼저 확인할 것 |
|---|---|---|
| 명령어를 어디에 입력할지 헷갈림 | Part 00~01 | `pwd`, VS Code 좌하단 `WSL: Ubuntu` |
| 파일이 어디 있는지 모름 | Part 01~02 | `pwd`, `ls -la`, `find` |
| Linux 명령어가 무슨 뜻인지 모름 | Part 01~02 | 명령어·옵션·인수 구분 |
| 권한 오류가 발생함 | Part 02, 06 | `ls -l`, 실행 사용자 |
| Docker 웹페이지가 안 열림 | Part 03, 06 | `docker ps -a`, `docker logs`, 포트 |
| 컨테이너를 지웠는데 데이터를 유지하고 싶음 | Part 03 | 볼륨 및 바인드 마운트 |
| Compose 서비스가 안 뜸 | Part 04, 06 | `docker compose config`, `ps`, `logs` |
| `.env`가 GitHub에 올라갈까 걱정됨 | Part 04~05 | `.gitignore`, `git status`, `git check-ignore` |
| GitHub에 최신 파일이 안 보임 | Part 05 | `git status`, `git log`, `git remote -v` |
| Windows에서 복사한 파일에 이상한 이름이 붙음 | Part 06 | `Zone.Identifier` 검색 |

**안전한 기본 진단 순서:** (1) `pwd`로 위치 확인 → (2) `ls -la`로 파일 확인 → (3) 프로그램의 상태·로그 확인 → (4) 오류 문구 검색 → (5) 원인을 확인한 뒤 최소한의 수정만 적용. 오류가 있다고 곧바로 재설치하거나 삭제하지 않는다.

---

## Part 00. 개발 환경 전체 지도

### 00.1 왜 개발 환경부터 배우는가?

**[M01]** 미션 목표는 코드를 작성할 수 있는 환경을 직접 만들고, 다른 사람이 같은 방식으로 실행·검증할 수 있도록 명령어와 설정을 기록하는 것이다. 학습 핵심은 Linux CLI·Docker·Git/GitHub이며, Dockerfile·포트 매핑·바인드 마운트·볼륨으로 재현 가능한 실행을 경험한다.

개발 환경을 배우는 이유는 세 가지다. **어디에서 실행하는가**를 파악하고, **같은 조건으로 다시 실행**할 수 있으며, **문제가 생겼을 때 원인 범위를 좁힐 수 있기 때문**이다.

### 00.2 Windows, WSL2, Ubuntu, VS Code, Docker의 관계

```text
Windows (호스트 운영체제)
├── 브라우저: http://localhost:8084 접속
├── VS Code: 편집기 화면 및 WSL 확장
└── WSL2 (Linux를 Windows에서 실행하는 환경)
    └── Ubuntu (Linux 배포판; Bash 터미널)
        ├── ~/codyssey/m01/ (프로젝트·Git 저장소)
        │   ├── README.md (미션 제출 보고서)
        │   ├── 00_실전개발참고서_GRACE-STACK_M01.md (이 문서)
        │   └── my-web/ (Dockerfile, compose.yaml, site/)
        ├── Git (로컬 이력 관리)
        └── Docker CLI → Docker Engine
            ├── web 컨테이너 (NGINX:80)
            └── tester 컨테이너 (curl 클라이언트)

GitHub (인터넷의 원격 Git 저장소; Ubuntu의 Git에서 연결)
```

**주의:** Docker Engine이 Linux 안에서 동작하는지, Docker Desktop/WSL 통합을 통해 제공되는지는 설치 방식마다 다르다. M01에서는 Ubuntu에서 `docker info`로 CLI와 서버 연결을 확인했다. `VS Code` 자체가 Linux나 Docker는 아니다. VS Code는 WSL로 연결해 그 안의 파일을 편집하는 도구다.

### 00.3 어디서 명령어를 실행해야 하는가?

| 작업 | 권장 위치 | 예시 |
|---|---|---|
| Ubuntu 파일 관리 | `[Ubuntu]` | `ls -la`, `chmod` |
| Docker·Compose 실행 | `[Ubuntu]` | `docker ps`, `docker compose up -d` |
| Git 커밋·Push | `[Ubuntu]`의 Git 저장소 | `git status`, `git push` |
| 웹 브라우저 확인 | `[Windows]` 브라우저 | `http://localhost:8084` |
| Windows 폴더 탐색 | `[Windows]` 탐색기 | `\\wsl$\Ubuntu\home\...` |
| 컨테이너 내부 명령 | `[Ubuntu]`에서 `docker exec` | `docker exec <이름> ls /data` |

**[M01 실제 환경]** Windows 11, WSL2 Ubuntu 26.04.1 LTS, Bash, Docker Engine 29.9.3, Docker Compose v5.7.0, Git 2.53.0, NGINX Alpine 이미지, VS Code WSL 확장을 사용했다. 버전은 당시의 실습 기록이며 다른 컴퓨터에서는 다를 수 있다.

**[확장] 환경 확인 명령**

```bash
# [Ubuntu] 배포판 및 시스템 정보
cat /etc/os-release
uname -a

# [Ubuntu] 도구 버전·연결 확인
docker --version
docker compose version
docker info
git --version
```

### 00.4 세 종류의 상태를 구별하자

- **파일 상태:** 실제 파일이 생성·변경·삭제되었는가? (`ls`, `cat`)
- **실행 상태:** 프로그램이나 컨테이너가 실행되고 있는가? (`docker ps`)
- **버전 관리 상태:** 파일 변경이 Git에 기록되고 GitHub에 올라갔는가? (`git status`, `git log`, `git push`)

파일을 만들었다고 GitHub에 자동 업로드되는 것은 아니다. 컨테이너가 `Exited`라고 이미지·볼륨까지 삭제된 것도 아니다.

---

## Part 01. 터미널 읽기와 경로

### 01.1 CLI, 셸, 터미널은 어떻게 다른가?

- **CLI(Command-Line Interface):** 명령어를 텍스트로 입력해 컴퓨터를 조작하는 방식.
- **터미널(Terminal):** 명령어 입력·출력 화면을 제공하는 프로그램.
- **셸(Shell):** 사용자가 입력한 명령을 해석하고 실행을 지시하는 프로그램. Ubuntu에서 주로 Bash를 사용한다.
- **프롬프트(Prompt):** 명령어 입력을 기다리는 표시. 복사한 예제의 `$`는 보통 명령 자체가 아니다.

### 01.2 명령어 해부 연습

```bash
find docs/screenshots -type f -name '*:Zone.Identifier'
```

| 부분 | 역할 |
|---|---|
| `find` | 실행할 명령어 |
| `docs/screenshots` | 검색 시작 경로(인수) |
| `-type f` | 일반 파일만 대상(옵션 및 값) |
| `-name` | 파일명 조건 옵션 |
| `'*:Zone.Identifier'` | 검색 패턴. 작은따옴표로 셸의 와일드카드 확장을 막음 |

**왜 배울까?** 명령어를 통째로 암기하는 대신, 새로운 명령어가 나와도 *명령어 / 옵션 / 인수 / 경로*로 나누어 이해할 수 있다.

### 01.3 절대 경로와 상대 경로

| 기호·경로 | 뜻 | 예시 |
|---|---|---|
| `/` | Linux 파일 시스템의 루트 | `/home` |
| `~` | 현재 사용자의 홈 디렉터리 | `~/codyssey/m01` |
| `.` | 현재 디렉터리 | `docker build .` |
| `..` | 상위 디렉터리 | `cd ..` |
| `/home/vomefall6468/codyssey/m01` | 절대 경로 | 어디에서나 같은 위치를 가리킴 |
| `my-web/compose.yaml` | 상대 경로 | 현재 위치에 따라 달라짐 |

```bash
pwd                       # 지금 있는 디렉터리
cd ~/codyssey/m01          # 홈 디렉터리 기준 이동
cd my-web                  # 현재 위치 아래로 이동
cd ..                      # 상위 폴더 이동
cd -                       # 직전 작업 디렉터리로 이동
```

**흔한 실수:** `docker build -t ... .`의 끝 `.`은 '현재 디렉터리를 빌드 컨텍스트로 사용한다'는 뜻이다. Dockerfile이 `my-web/`에 있으면 일반적으로 그곳에서 실행해야 한다.

### 01.4 따옴표·공백·와일드카드

```bash
mkdir 'my notes'          # 공백 있는 이름은 따옴표로 묶음
grep -n 'Docker Compose' README.md
find . -name '*.md'       # *.md 파일을 find가 검색하게 전달
```

- 작은따옴표 `'...'`: 안의 글자를 대부분 문자 그대로 취급한다.
- 큰따옴표 `"..."`: 공백을 묶으면서 `$HOME` 같은 변수는 확장할 수 있다.
- `*`: 여러 문자에 대응하는 와일드카드. 따옴표 밖에서는 셸이 먼저 확장할 수 있다.

### 01.5 터미널 작업 효율

| 키·기능 | 무엇을 하는가 |
|---|---|
| `Tab` | 파일명·명령어 자동 완성 |
| `↑` / `↓` | 이전 명령어 다시 불러오기 |
| `Ctrl+C` | 현재 실행 중인 작업에 중단 신호 전달 |
| `Ctrl+L` 또는 `clear` | 화면 정리(명령 이력 삭제 아님) |
| `history` | 셸에 저장된 명령 이력 확인 |
| `man ls` | 매뉴얼 확인 (`q`로 종료) |
| `명령어 --help` | 사용법 간단 확인(지원 여부는 도구별로 다름) |

**[주의]** `Ctrl+C`는 화면을 '지우는' 기능이 아니다. 실행 중인 프로그램을 중단하므로 작업 목적을 확인한다.

### 01.6 파이프·리다이렉션

```bash
echo 'Hello' > note.txt    # 파일을 새로 쓰거나 기존 내용을 덮어씀
echo 'Again' >> note.txt   # 파일 끝에 추가
cat note.txt               # 내용 출력
ls -la | grep '.md'        # 앞 명령어 출력을 grep의 입력으로 연결
```

- `>`: 표준 출력을 파일로 보냄. 기존 파일이 있으면 **덮어쓴다**.
- `>>`: 기존 파일 뒤에 출력 내용을 **추가**한다.
- `|`: 왼쪽 명령의 표준 출력을 오른쪽 명령의 표준 입력으로 연결한다.

**[확장]** `2>`는 표준 오류를 따로 보낼 때 쓴다. 오류가 조용히 사라지지 않게 기록 위치를 확인해야 한다.

---

## Part 02. Linux CLI와 파일 권한

### 02.1 실제 수행한 파일·디렉터리 작업

**[M01]** `~/codyssey/m01`에서 파일 생성, 출력, 복사, 이동·이름 변경, 삭제를 수행했다.

```bash
mkdir -p ~/codyssey/m01
cd ~/codyssey/m01
pwd
ls -la

touch practice.txt
echo 'hello Codyssey' > practice.txt
cat practice.txt
cp practice.txt copy.txt
mv copy.txt renamed.txt
rm renamed.txt
```

| 명령어 | 주요 역할 | 놓치기 쉬운 점 |
|---|---|---|
| `pwd` | 현재 경로 출력 | 작업 위치 확인에 가장 먼저 사용 |
| `ls -la` | 숨김 파일과 권한 등 자세히 조회 | `.gitignore` 같은 숨김 파일도 확인 |
| `cd` | 경로 이동 | 파일 내용을 변경하지 않음 |
| `mkdir -p` | 필요한 부모 디렉터리까지 생성 | 기존 디렉터리에 대해서도 보통 오류 없이 진행 |
| `touch` | 파일이 없으면 만들고 있으면 수정 시각 갱신 | 기존 내용을 지우지 않음 |
| `echo` | 문자열 출력 | `>`와 함께 쓰면 파일 덮어쓰기 |
| `cat` | 파일 내용 출력 | 큰 파일은 `less`가 더 적합할 수 있음 |
| `cp` | 복사 | 원본 유지 |
| `mv` | 이동 또는 이름 변경 | 원본 위치에서 사라짐 |
| `rm` | 삭제 | 휴지통으로 보내지 않음 |

### 02.2 `touch` · `nano` · `echo` 비교

- `touch notes.md`: 빈 파일을 빠르게 준비한다.
- `nano notes.md`: 텍스트 편집기에서 여러 줄을 작성한다. **저장** `Ctrl+O` → Enter, **종료** `Ctrl+X`.
- `echo 'text' > notes.md`: 짧은 내용을 한 줄로 쓰지만 기존 파일을 덮어쓸 수 있다.
- `echo 'text' >> notes.md`: 로그나 메모를 뒤에 추가한다.

### 02.3 파일 권한 `rwx`의 의미

```text
-rw-r--r--  practice.txt
│││ │││ │││
│││ │││ └── 기타 사용자(other)의 권한
│││ └───── 그룹(group)의 권한
│└───────── 소유자(owner)의 권한
└────────── 파일 유형: - 일반 파일, d 디렉터리
```

- `r`(read): 파일 읽기 / 디렉터리 이름 목록 조회와 관련.
- `w`(write): 파일 내용 수정 / 디렉터리 항목 추가·삭제·이름 변경과 관련.
- `x`(execute): 파일 실행 / 디렉터리 내부 경로 탐색.

디렉터리에서는 단독 `r`만으로 내부 파일에 접근할 수 있다고 단정할 수 없다. 경로 탐색에 `x`도 중요하다.

### 02.4 숫자 권한을 읽는 법

숫자는 각 권한의 합이다: `r=4`, `w=2`, `x=1`.

| 숫자 | 해당 권한 | 계산 |
|---|---|---|
| 7 | `rwx` | 4+2+1 |
| 6 | `rw-` | 4+2 |
| 5 | `r-x` | 4+1 |
| 4 | `r--` | 4 |
| 0 | `---` | 0 |

`755` = 소유자 `rwx`, 그룹 `r-x`, 기타 `r-x`. `644` = 소유자 `rw-`, 그룹 `r--`, 기타 `r--`.

**[M01 실제 권한 실습]**

```bash
chmod 600 practice.txt
ls -l practice.txt
# -rw------- ... practice.txt

chmod 700 permission-test
ls -ld permission-test
# drwx------ ... permission-test
```

| 대상 | 변경 전 | 변경 후 | 목적 |
|---|---|---|---|
| `practice.txt` | 644 | 600 | 소유자만 읽고 쓰도록 제한 |
| `permission-test/` | 755 | 700 | 소유자만 내부 탐색·수정하도록 제한 |

### 02.5 `sudo`와 권한 오류

`sudo`는 허가된 사용자에게 관리자 권한으로 명령을 실행하게 하는 기능이다. **권한 오류가 날 때마다 `sudo`를 붙이는 방식은 바람직하지 않다.** 먼저 현재 사용자, 파일 소유권, 도구 연결 방식을 확인한다.

```bash
whoami
ls -l practice.txt
ls -ld permission-test
```

**[확장]** `chmod 777`은 모두에게 읽기·쓰기·실행 권한을 부여하므로 일반적인 문제 해결책으로 쓰지 않는다. `chown`은 파일 소유권 변경 명령이지만, 현재 파일의 소유관계를 확인한 뒤 필요한 범위에서만 사용한다.

### 02.6 `find` — 파일·디렉터리 찾기

```bash
find . -name 'README.md'                  # 현재 위치 이하에서 파일명 검색
find docs/screenshots -type f -name '*.png' # PNG 파일 검색
find . -type f -name '*:Zone.Identifier'   # Windows 출처 메타데이터 검색
```

**명령어를 읽는 순서:** 어디서 찾는가(`.`) → 어떤 대상인가(`-type f`) → 이름이 무엇인가(`-name`) → 어떤 작업인가(기본 출력).

### 02.7 `grep` — 파일 속 내용 찾기

**[M01 실제 사용]** README에 보너스 과제 섹션이 들어갔는지 확인했다.

```bash
grep -n '보너스 과제' README.md
```

| 옵션 | 의미 | 예시 |
|---|---|---|
| `-n` | 줄 번호 표시 | `grep -n 'Docker' README.md` |
| `-i` | 대소문자 구별 없이 검색 | `grep -i 'error' app.log` |
| `-r` | 하위 디렉터리까지 검색 | `grep -r 'TODO' .` |
| `-v` | 해당 패턴이 없는 줄만 표시 | `grep -v 'INFO' app.log` |
| `-F` | 정규식이 아닌 고정 문자열 검색 | `grep -F '8084->80' file.txt` |

**차이:** `find`는 *파일의 위치·이름*을 찾고, `grep`은 *파일의 내용*을 찾는다. `grep` 결과가 없다고 오류가 난 것은 아니다. 검색 일치가 없으면 일반적으로 출력이 없고 종료 상태가 1이다.

### 02.8 파일 권한·검색 미니 실습

```bash
mkdir -p ~/codyssey/m01/cli-lab
cd ~/codyssey/m01/cli-lab
printf 'Docker Compose\nGitHub SSH\n' > keywords.txt
ls -l keywords.txt
chmod 600 keywords.txt
grep -n 'GitHub' keywords.txt
find . -name '*.txt'
```

**[확장 실습]** 기존 프로젝트 핵심 파일을 건드리지 않도록 별도 `cli-lab`에서 수행한다. 종료 후 폴더를 삭제할 때는 위치와 대상부터 확인한다.

---

## Part 03. Docker 실전

### 03.1 Docker가 필요한 이유

Docker는 **이미지(image)**에 정해진 실행 환경을 기반으로 **컨테이너(container)**를 실행한다. 개발자마다 설치된 패키지와 실행 설정이 다른 문제를 줄인다.

- **이미지:** 컨테이너를 생성하는 읽기 전용 템플릿과 레이어들의 집합. 한 이미지에서 여러 컨테이너를 만들 수 있다.
- **컨테이너:** 이미지를 기반으로 실제 실행되는 격리된 프로세스 환경. 별도의 쓰기 가능 계층을 갖는다.
- **Dockerfile:** 이미지를 만드는 절차를 텍스트로 기록한 파일.
- **Docker Engine:** 이미지 빌드와 컨테이너 실행·관리 담당.
- **Docker CLI:** `docker ...` 명령으로 엔진에 작업을 요청하는 도구.

Docker 컨테이너는 **가상머신과 동일하지 않다.** 일반적으로 호스트 커널의 기능을 활용하는 프로세스 격리 기술이다.

### 03.2 설치 점검과 상태 확인

**[M01]** Docker 공식 APT 저장소를 구성하고 엔진·Compose 설치 후 다음을 확인했다.

```bash
docker --version    # 클라이언트 버전
docker info         # 서버 연결·엔진 동작 확인
docker compose version
```

`docker --version`이 작동해도 서버 연결이 실패할 수 있다. `docker info`가 클라이언트와 서버 정보를 모두 보여주는지 확인한다. 권한 또는 데몬 연결 오류가 발생하면 Part 06을 참고한다.

### 03.3 이미지 관련 명령어

```bash
docker pull ubuntu     # 원격 레지스트리에서 이미지 다운로드
docker images          # 로컬 이미지 목록
docker image inspect ubuntu
```

`pull`은 이미지 다운로드, `run`은 이미지에서 **컨테이너 생성 후 실행**이다. 처음 실행할 때 이미지가 없으면 `run`이 이미지를 자동으로 가져올 수 있다.

### 03.4 컨테이너의 생애주기

```text
이미지 선택 → docker run → 생성·실행(Up)
                              │
                        docker stop
                              ↓
                         중지(Exited)
                              │
                        docker start
                              ↓
                           다시 Up

docker rm: 컨테이너 객체 제거(이미지·볼륨까지 자동 삭제한다는 뜻 아님)
```

**[M01]**

```bash
docker run hello-world
docker run -it ubuntu bash
# [Container] pwd, ls, echo 등의 명령 실행 후 exit

docker ps                    # 실행 중 컨테이너
docker ps -a                 # 종료된 컨테이너 포함
docker images
docker logs <컨테이너이름>
docker stats --no-stream
docker stop <컨테이너이름>
```

`<컨테이너이름>`은 설명용 자리표시자다. 실제 실행 시 꺾쇠괄호까지 입력하지 말고 이름으로 교체한다.

### 03.5 `run` · `start` · `exec` · `attach` 비교

| 명령어 | 목적 | 대표 상황 |
|---|---|---|
| `docker run` | 이미지에서 새 컨테이너 생성·실행 | 처음 테스트하거나 새 복제본 생성 |
| `docker start` | 기존 중지된 컨테이너 다시 실행 | `Exited` 상태에서 재시작 |
| `docker exec` | 실행 중인 컨테이너 안에서 새 명령 수행 | 서버를 중단하지 않고 내부 점검 |
| `docker attach` | 실행 중인 컨테이너의 주 프로세스 입출력에 연결 | 기존 프로세스의 입출력 관찰 |
| `docker logs` | 컨테이너 로그 조회 | 웹서버 오류 분석 |

**중요:** `docker exec`는 기본적으로 **실행 중인** 컨테이너에서만 가능하다. `attach`에서 입력한 종료 동작이 컨테이너 주 프로세스에 영향을 줄 수 있으므로 초보자 점검에는 `exec`가 편하다.

### 03.6 백그라운드·대화형·이름 지정

```bash
docker run -d --name sample nginx:alpine
```

- `-d`(detached): 백그라운드 실행.
- `--name sample`: 컨테이너 이름 지정. 같은 이름의 컨테이너를 중복 생성할 수 없다.
- `-it`: 대화형 터미널(`-i` 입력 연결, `-t` 가상 터미널).

**[M01]** Ubuntu 테스트 컨테이너에 `sleep infinity`를 사용한 것은 *메인 프로세스를 계속 실행 상태로 두어* `docker exec`로 들어갈 수 있게 하기 위해서다. 이것만으로 서비스가 만들어지는 것은 아니다.

### 03.7 Dockerfile — NGINX 기반 웹 서버 만들기

**[M01 실습 파일 구조]**

```text
my-web/
├── Dockerfile
├── site/
│   └── index.html
├── compose.yaml     # 보너스 과제에서 추가
└── .env.example     # 보너스 과제에서 추가
```

**[M01 Dockerfile]**

```dockerfile
FROM nginx:alpine
COPY site/ /usr/share/nginx/html/
```

- `FROM`: 베이스 이미지 결정.
- `COPY`: 현재 빌드 컨텍스트의 파일을 이미지 안으로 복사.
- NGINX의 `/usr/share/nginx/html/`: 정적 웹 콘텐츠를 제공하는 디렉터리.

```bash
cd ~/codyssey/m01/my-web
docker build -t codyssey-01-web:1.0 .
docker images
```

`-t`는 이미지의 이름과 태그를 설정한다. `.`은 **빌드 컨텍스트**이다. M01에서는 `codyssey-01-web:1.0` 이미지 빌드에 성공했다.

### 03.8 포트 매핑: 왜 `8081:80`인가?

컨테이너 안의 NGINX는 80번 포트에서 대기한다. 하지만 호스트 브라우저는 컨테이너 내부 네트워크에 무조건 직접 접근할 수 있는 것이 아니다. 호스트 포트를 연결하는 **포트 게시(publishing)**가 필요하다.

```text
[Windows 브라우저] localhost:8081
                ↓ 호스트 8081 → 컨테이너 80
[NGINX 컨테이너] :80
```

**[M01]**

```bash
docker run -d -p 8081:80 --name codyssey-m01-web codyssey-01-web:1.0
docker ps
```

브라우저 `http://localhost:8081`에서 `Hello Codyssey!` 웹페이지가 표시되었다. `-p 8081:80`의 왼쪽은 호스트 포트, 오른쪽은 컨테이너 포트다.

**[확장·보안]** `-p 8081:80`은 기본적으로 모든 호스트 인터페이스에 바인딩될 수 있다. 오직 자신의 컴퓨터에서만 테스트하려면 `-p 127.0.0.1:8081:80` 형식으로 호스트 바인딩 주소를 제한하는 방식을 고려한다(환경별 차이 확인).

### 03.9 바인드 마운트: 파일을 즉시 반영하고 싶을 때

**이미지에 COPY한 파일**은 이미지를 다시 빌드하지 않는 이상 변경 전 내용이 남아 있을 수 있다. **바인드 마운트**는 호스트의 경로를 컨테이너에 연결하여 호스트에서 파일을 편집하면 컨테이너에서도 변경이 보이게 한다.

```bash
cd ~/codyssey/m01/my-web
docker run -d -p 8082:80 \
  --name codyssey-m01-bind \
  --mount type=bind,source="$(pwd)/site",target=/usr/share/nginx/html,readonly \
  codyssey-01-web:1.0
```

`$(pwd)`는 **현재 작업 디렉터리의 절대 경로**로 치환된다. 따라서 이 명령어는 `my-web` 폴더에서 실행해야 한다. `readonly`는 컨테이너에서 마운트된 경로를 쓰기 금지로 접근하게 한다.

**[M01 검증]** `site/index.html`을 수정하고 `http://localhost:8082`에서 `Hello Docker Bind Mount!` 문구가 **이미지 재빌드 없이** 반영된 것을 확인했다.

### 03.10 이름 있는 볼륨: 컨테이너가 삭제되어도 데이터 유지

바인드 마운트는 **호스트의 특정 경로**를 연결하고, named volume(이름 있는 볼륨)은 **Docker가 관리하는 영속 저장 공간**을 사용한다.

```bash
docker volume create codyssey-m01-data

docker run -d --name codyssey-m01-vol1 \
  -v codyssey-m01-data:/data ubuntu sleep infinity

docker exec codyssey-m01-vol1 \
  sh -c 'echo "M01 volume test" > /data/test.txt'

docker rm -f codyssey-m01-vol1

docker run -d --name codyssey-m01-vol2 \
  -v codyssey-m01-data:/data ubuntu sleep infinity

docker exec codyssey-m01-vol2 cat /data/test.txt
# M01 volume test
```

**[M01 검증]** 첫 컨테이너를 삭제한 뒤 두 번째 컨테이너에서 같은 데이터가 출력되었다. 볼륨은 컨테이너 수명과 분리되어 관리된다. 단, `docker volume rm`, `docker compose down -v` 등의 조작은 데이터 손실을 유발할 수 있다.

### 03.11 세 가지 저장 위치 비교

| 구분 | 이미지 내부 파일 | 바인드 마운트 | Named volume |
|---|---|---|---|
| 위치 | 이미지의 레이어 | 호스트의 지정 폴더 | Docker가 관리하는 저장 공간 |
| 주용도 | 초기 소스·설정 배포 | 개발 중 즉시 편집 | 데이터 보존 |
| 수정 반영 | 보통 이미지 다시 빌드 | 호스트 파일 수정 즉시 보임 | 동일 볼륨에 연결한 컨테이너에서 공유 가능 |
| 주의 | 이미지·컨테이너 혼동 | 경로가 틀리면 의도한 파일이 안 보임 | 삭제 시 데이터 손실 위험 |

### 03.12 Docker 점검 루틴

```bash
docker ps -a                  # 1. 대상 존재·상태
docker images                # 2. 이미지 존재
docker logs <컨테이너이름>    # 3. 실행 로그
docker port <컨테이너이름>    # 4. 포트 연결
docker inspect <컨테이너이름> # 5. 상세 구성
docker stats --no-stream     # 6. 리소스 정보
```

**[확장]** `docker system prune`은 불필요한 리소스를 정리하지만 예상보다 넓은 범위를 제거할 수 있다. 학습 실습에서도 실행 대상과 보존할 데이터를 먼저 점검한다.

---

## Part 04. Docker Compose와 환경변수

### 04.1 Compose는 왜 필요한가?

**[M01 보너스]** `docker run`에서 여러 옵션을 길게 입력하던 방식을 **`compose.yaml`이라는 설정 파일**로 바꾸는 도구다. 여러 컨테이너를 한 프로젝트로 정의하고 시작·종료·상태·로그를 함께 관리할 수 있다.

- **Dockerfile:** 하나의 이미지를 어떻게 만드는가?
- **Compose 파일:** 어떤 서비스를 어떤 이미지·포트·네트워크·환경설정으로 함께 실행하는가?
- **`.env`:** Compose 파일의 변수 치환에 사용할 값 등을 어디서 가져오는가?

### 04.2 YAML 읽는 법

```yaml
services:
  web:
    build: .
    ports:
      - "${WEB_PORT}:80"
  tester:
    image: curlimages/curl:latest
    command: ["sleep", "infinity"]
    depends_on:
      - web
```

- 들여쓰기는 보통 스페이스 2칸으로 일관되게 작성한다. **Tab 대신 공백 사용**.
- `services`는 서비스 정의 집합, `web`과 `tester`는 서비스 이름이다.
- `build: .`는 현재 위치의 Dockerfile을 기반으로 이미지를 빌드하도록 설정한다.
- `ports`는 포트 게시, `image`는 사용 이미지, `command`는 실행 명령이다.
- `depends_on`은 시작 순서에 관여한다. 기본 형식만으로 웹 서버의 **준비 완료**까지 보장하지는 않는다.

Compose 파일 이름은 `compose.yaml`이 권장되는 형태이며, 미션 가이드의 `docker-compose.yml`도 지원되는 파일명이다.

### 04.3 단일 서비스 실행 [M01 보너스 ①]

최초 웹 서비스에서 `8083:80` 포트를 사용해 실행했다.

```bash
cd ~/codyssey/m01/my-web
docker compose config
docker compose up -d --build
docker compose ps
```

**검증:** `web` 서비스 `Up`, `http://localhost:8083` 정상 접속. 이후 환경변수 실습에서 포트를 **8084로 변경**했다. 따라서 현재 `8083`을 그대로 접속해도 반드시 작동하는 것은 아니다.

### 04.4 두 서비스와 내부 통신 [M01 보너스 ②]

NGINX 웹 서버 `web`과 `curlimages/curl` 기반 `tester`를 같은 Compose 구성에 추가했다.

```bash
docker compose up -d
docker compose ps
docker compose exec tester curl -I http://web
# HTTP/1.1 200 OK
```

**왜 `http://web`인가?** 같은 Compose 기본 네트워크에 참여하는 컨테이너는 서비스 이름으로 상대 서비스에 접근할 수 있다. 이를 **서비스 디스커버리(service discovery)**라고 한다. 호스트 브라우저의 `localhost:8084`와 컨테이너 내부의 `http://web`은 사용 위치가 다르다.

### 04.5 Compose 운영 [M01 보너스 ③]

```bash
docker compose ps
# 실행 중인 서비스 조회

docker compose logs --tail=10
# 각 서비스 최근 로그 확인

docker compose down
# 이 Compose 프로젝트의 컨테이너·기본 네트워크 제거

docker compose up -d
# 다시 생성해 백그라운드 실행

docker compose ps
# web, tester의 Up 확인
```

| 명령어 | 의미 | 주의 |
|---|---|---|
| `config` | 변수 치환과 최종 설정 확인 | 실제 서비스 실행은 아님 |
| `up -d` | 서비스 생성·시작 | 변경 설정에 따라 재생성될 수 있음 |
| `up -d --build` | 필요 이미지 빌드 후 실행 | 소스 변경 반영에 유용 |
| `ps` | Compose 서비스 상태 확인 | 이 Compose 프로젝트 기준 |
| `logs --tail=10` | 로그 확인 | `404 /favicon.ico` 같은 부수 로그도 존재 |
| `down` | 서비스 컨테이너와 기본 네트워크 제거 | 기본적으로 named volume은 지우지 않지만 `-v` 주의 |

### 04.6 환경변수: 코드를 수정하지 않고 설정 바꾸기 [M01 보너스 ④]

```dotenv
# my-web/.env — 로컬 실습용 값
WEB_PORT=8084
```

```yaml
# compose.yaml 중 일부
services:
  web:
    build: .
    ports:
      - "${WEB_PORT}:80"
```

```bash
docker compose config
# published: "8084" 확인

docker compose up -d
docker compose ps
# 8084->80/tcp 확인
```

**[M01 검증]** `http://localhost:8084` 접속 성공. 환경변수를 사용하면 같은 구성 파일을 환경별로 다르게 설정할 수 있다.

**중요한 구분:** Compose의 `.env`를 이용한 `${WEB_PORT}` **변수 치환**과, 컨테이너 프로세스에 `environment:`로 변수를 **주입**하는 것은 동일하지 않다. 위 실습은 **호스트 공개 포트의 치환**을 검증했으며, NGINX 프로세스 자체에 `WEB_PORT`가 전달되는 것을 검증한 것은 아니다.

### 04.7 `.env`와 `.env.example`은 왜 둘 다 필요한가?

| 파일 | 목적 | Git 업로드 권장 |
|---|---|---|
| `.env` | 내가 실제로 사용하는 로컬 설정과 민감할 수 있는 값 | 일반적으로 제외 |
| `.env.example` | 다른 개발자가 필요한 변수 이름과 예시값을 알도록 안내 | 포함 |
| `.gitignore` | Git이 무시할 경로 패턴 정의 | 포함 |

```dotenv
# my-web/.env.example
WEB_PORT=8084
```

**재현 절차 [확장]**

```bash
cd ~/codyssey/m01/my-web
cp .env.example .env
# 필요 시 로컬 값만 수정

docker compose config
docker compose up -d --build
```

실제 비밀번호·토큰·API 키는 `.env.example`에 넣지 않는다. `.env` 역시 파일 자체만으로 '암호화 보관'을 제공하지 않는다. 이미 Git이 추적하는 파일은 나중에 `.gitignore`를 수정해도 자동으로 추적 해제되지 않는다.

### 04.8 `.gitignore`와 Git 추적의 관계

```bash
cd ~/codyssey/m01
git status --short
git check-ignore -v my-web/.env
git check-ignore -v my-web/.env.example
```

**[M01]** `.env`는 제외되고 `.env.example`은 버전 관리 대상이 되는 것을 확인했다. `!.env.example`은 제외 규칙의 예외 패턴이다. `git check-ignore`가 예외 규칙을 출력하더라도 최종적으로 `git status`에서 새 파일로 등장하는지 확인하는 것이 직관적이다.

**[확장·주의]** 비밀정보가 이미 `git add`/커밋되었다면 `.gitignore` 추가만으로 Git 이력에서 사라지지 않는다. 민감정보는 우선 폐기·재발급하고 이력 정리를 별도로 검토한다.

### 04.9 Compose 실습 결과 종합

| 보너스 실습 | 실제 결과 |
|---|---|
| 단일 웹 서버 | `8083:80` 접속 성공 |
| 멀티 컨테이너 | `web`, `tester` 동시 `Up` |
| 내부 통신 | `tester → http://web` : HTTP 200 |
| 운영 | `ps`, `logs`, `down`, `up` 검증 |
| 환경변수 | `WEB_PORT=8084`, 브라우저 재접속 성공 |

---

## Part 05. Git·GitHub·SSH

### 05.1 Git과 GitHub는 왜 다르게 배워야 하는가?

- **Git:** 로컬 파일의 변경 이력과 커밋을 관리하는 분산 버전 관리 시스템.
- **GitHub:** Git 저장소를 인터넷에 호스팅하고 협업·검토 기능을 제공하는 플랫폼.
- **Git 저장소(repository):** 파일 상태와 커밋 이력을 기록하는 영역. `.git/`이 핵심이다.
- **커밋(commit):** 특정 시점의 변경사항을 이력으로 남긴 기록.

**핵심 흐름:** 작업 폴더(파일 수정) → 스테이징 영역(`git add`) → 로컬 커밋(`git commit`) → 원격 GitHub(`git push`).

### 05.2 M01 실제 Git 설정과 첫 Push

```bash
cd ~/codyssey/m01
git config --global user.name '작성자명'
git config --global user.email 'GitHub 등록 이메일'
git config --global init.defaultBranch main

git init
git status
git add .
git commit -m 'Initial commit: M01 development environment'
git log --oneline
```

**[M01]** 첫 커밋은 `431c074`였으며, GitHub CLI로 HTTPS 인증을 구성해 원격 저장소에 Push했다. 현재 저장소는 `main` 브랜치를 사용한다.

```bash
git remote add origin https://github.com/bomigrace-byte/Codyssey-AIO-M01.git
git push -u origin main
```

**주의:** 위의 `remote add`는 최초 설정 예시다. 이미 `origin`이 있으면 다시 실행할 필요가 없고 오류가 날 수 있다. `git remote -v`로 먼저 확인한다.

### 05.3 Git 상태 표시 읽기

```bash
git status
git status --short
git diff                    # 아직 스테이징하지 않은 변경 비교
git diff --cached           # 커밋 예정 변경 비교
git diff --cached --name-only
git log --oneline -5
```

| 표시 예시 | 일반적인 의미 |
|---|---|
| `?? file` | 아직 추적하지 않는 새 파일 |
| ` M file` | 작업 폴더에서 수정되었으나 스테이징 전 |
| `M  file` | 스테이징에 수정 반영 |
| ` D file` | 추적 파일이 작업 폴더에서 삭제됨 |
| `D  file` | 삭제가 스테이징됨 |
| `nothing to commit, working tree clean` | 현재 작업 폴더의 변경사항이 없음 |

**[M01 실습]** Windows에서 복사해 생긴 `:Zone.Identifier` 메타데이터 파일의 삭제 이력이 Git에 `D`로 나타났고, `git add -A`로 삭제 기록도 커밋 대상에 포함시켰다.

### 05.4 가장 안전한 커밋·Push 루틴

```bash
cd ~/codyssey/m01
git status --short
# 변경 내용 확인

git add README.md my-web/compose.yaml my-web/.env.example
# 원하는 파일만 추가(필요한 스크린샷은 개별 추가)

git diff --cached --stat
git diff --cached --name-only
# 비밀정보나 의도하지 않은 파일이 없는지 확인

git commit -m 'Docs: Update M01 learning materials'
git push origin main
git status
```

**[주의]** `git add -A`는 삭제 파일과 신규 파일을 포함한 **전체 변경**을 스테이징한다. 편리하지만 의도하지 않은 민감정보가 있는지 반드시 확인한다.

### 05.5 Git 사고 복구의 기초 [확장]

| 실수 | 검토할 명령 | 의미 |
|---|---|---|
| 실수로 `git add`함 | `git restore --staged <파일>` | 스테이징만 취소, 작업 파일은 유지 |
| 아직 커밋하지 않은 수정 확인 | `git diff <파일>` | 원본 대비 수정 범위 확인 |
| 스테이징된 수정 확인 | `git diff --cached <파일>` | 커밋 예정 내용 비교 |
| 최근 커밋 확인 | `git log --oneline` | 복구 기준점 파악 |
| 이미 원격에 반영된 변경 취소 | `git revert <커밋>` | 기존 이력을 보존하며 되돌리는 새 커밋 생성 |

**[주의]** `git restore <파일>`, `git reset --hard`, 강제 Push(`--force`)는 미커밋 변경이나 협업 이력을 잃게 만들 수 있다. 본 참고서에서는 기본 복구 절차로 권장하지 않는다. `git revert`도 충돌이 발생할 수 있으므로 현재 상태를 확인한 뒤 사용한다.

### 05.6 SSH 공개키·개인키는 무엇인가?

**SSH(Secure Shell)**는 안전하게 원격 시스템과 통신하기 위한 프로토콜이다. GitHub는 SSH 공개키 인증을 이용해 사용자의 `git push` 권한을 확인할 수 있다.

```text
Ubuntu 컴퓨터                        GitHub 계정
~/.ssh/id_ed25519 (개인키)          등록된 공개키
       │                                  │
       └── 공개키 인증 절차로 소유 증명 ────┘
             개인키 내용 자체는 전송하지 않음
```

- **개인키(private key):** 자신의 장치에만 보관한다. 절대 GitHub 저장소·README·채팅·스크린샷에 올리지 않는다.
- **공개키(public key):** `*.pub` 파일. GitHub의 SSH keys 설정에 등록한다.
- **암호문구(passphrase):** 개인키 파일을 추가로 보호하는 암호. GitHub 비밀번호와는 별개다.
- **서버 지문(fingerprint):** 처음 SSH 연결할 때 상대 서버가 진짜 GitHub인지 검증하는 정보. 공식 GitHub 문서와 대조한다.

### 05.7 SSH 생성부터 GitHub Push까지 [M01 보너스 ⑤]

```bash
# [Ubuntu] 기존 키 파일명 확인
ls -la ~/.ssh

# 기존 키가 없을 때 생성
ssh-keygen -t ed25519 -C 'codyssey-m01'

# 생성된 공개키만 확인하여 GitHub에 등록
cat ~/.ssh/id_ed25519.pub

# 서버 지문을 공식 문서와 대조한 뒤 인증 시험
ssh -T git@github.com

# [Ubuntu] 저장소 안에서 원격 URL 변경
cd ~/codyssey/m01
git remote -v
git remote set-url origin git@github.com:bomigrace-byte/Codyssey-AIO-M01.git
git remote -v
git push origin main
```

**[M01 실제 결과]** `Hi bomigrace-byte! You've successfully authenticated, but GitHub does not provide shell access.` 메시지와 SSH 원격 주소를 확인했다. `git push origin main`은 `Everything up-to-date`를 출력했다. 이 메시지는 **원격에 올릴 새 커밋이 없는 상태**이며, SSH 연결 자체가 성공한 결과였다.

**[주의]** 키 생성 명령은 이미 같은 이름의 키가 있으면 덮어쓰기 여부를 물을 수 있다. 기존 키를 확인한 후 생성하고 덮어쓰지 않는다. SSH 접속이 성공한 것과 새 커밋을 실제 Push한 것은 구분한다.

### 05.8 HTTPS와 SSH 비교

| 구분 | HTTPS | SSH |
|---|---|---|
| 주소 예시 | `https://github.com/계정/저장소.git` | `git@github.com:계정/저장소.git` |
| 인증 방식 | 토큰·자격 증명 도우미 등 | SSH 키 쌍 |
| 초기 설정 | 인증 도구 로그인 | 키 생성·공개키 등록·지문 확인 |
| Git 사용 명령 | `git push` | `git push` |

### 05.9 VS Code와 Git 연동

**[M01]** VS Code WSL 확장으로 Ubuntu 프로젝트를 열었고, Source Control 패널에서 파일 변경·커밋·Push를 확인했다. `code .` 명령이 즉시 작동하지 않았던 적이 있었지만 VS Code의 WSL 연결 기능으로 프로젝트를 열어 작업했다.

**중요:** VS Code의 Source Control은 Git 작업을 시각화한 것이다. 실제 저장소와 원격 상태를 확인하는 기준으로 `git status`, `git remote -v`, GitHub 웹 화면을 함께 사용한다.

---

## Part 06. 오류 분석과 복구

### 06.1 오류를 해결하는 공통 절차

1. **증상:** 언제, 어떤 명령에서 오류가 났는가? 메시지를 그대로 읽는다.
2. **가설:** 경로·권한·프로세스·포트·파일명·인증 중 어느 범주인가?
3. **확인:** 상태 조회나 읽기 전용 명령으로 가설을 검증한다.
4. **최소 수정:** 필요 조건만 바꾸고 기존 환경을 가능한 보존한다.
5. **재검증:** 성공 메시지 외에 실제 기능까지 확인한다.
6. **기록:** 사용한 명령·출력·해결 방법을 남긴다.

### 06.2 Docker 포트·명령어 형식 오류 [M01]

- **증상:** `docker: invalid reference format` 등 실행 명령 형식 오류.
- **가설:** 옵션의 위치·공백·이미지 이름·줄바꿈 등의 입력 문제. M01 기록에서는 포트 옵션 입력 누락을 우선 점검했다.
- **확인:** 실제 입력한 전체 명령과 오류 문구를 비교했다.
- **해결:** 다음처럼 포트를 명시한 명령을 사용했다.

```bash
docker run -d -p 8081:80 --name codyssey-m01-web codyssey-01-web:1.0
```

**주의:** `invalid reference format`은 여러 입력 오류에서 발생할 수 있다. *언제나 `-p` 누락 때문*이라고 일반화하지 않는다.

### 06.3 바인드 마운트 접속 거부 [M01]

- **증상:** 브라우저 `ERR_CONNECTION_REFUSED`.
- **가설:** 포트 8082에 연결할 컨테이너가 아직 생성되지 않았거나 실행되지 않음.
- **확인:**

```bash
docker ps -a
docker logs codyssey-m01-bind
docker inspect codyssey-m01-bind
```

- **결과:** `No such container` 오류로 대상이 존재하지 않는 것을 확인했다.
- **해결:** Part 03의 바인드 마운트 `docker run` 명령으로 컨테이너를 생성했다.
- **검증:** `localhost:8082`에 접속했고 HTML 변경이 재빌드 없이 반영되었다.

### 06.4 `docker status`와 `docker stats` 혼동 [M01]

**오류 입력:** `docker status --no-stream`.

**올바른 명령:**

```bash
docker stats --no-stream
```

**핵심:** Docker의 CPU·메모리 사용량을 조회하는 것은 `stats`이다. 처음 보는 명령어는 `docker --help`와 하위 명령 도움말로 존재 여부를 확인한다.

### 06.5 Docker 데몬 접근 권한 오류 [M01 별도 실습]

**증상:** `permission denied` 또는 Docker 소켓 접근 오류.

**확인 순서:**

```bash
whoami
docker info
ls -l /var/run/docker.sock
```

실제 운영 방식이 Docker Desktop/WSL 통합인지, Ubuntu에 별도 엔진을 설치했는지 먼저 파악한다. 임의로 소켓 권한을 `777`로 바꾸는 것은 보안 위험이 있다. 관리자 권한이나 Docker 그룹 설정 변경 역시 사용 권한과 보안 정책을 확인한 뒤 수행한다.

### 06.6 이름 충돌·포트 충돌 [M01 별도 실습]

- **컨테이너 이름 충돌:** 이미 같은 이름의 컨테이너가 있다. `docker ps -a`로 확인한 다음 기존 컨테이너를 재사용하거나 새 이름을 정한다.
- **포트 충돌:** 이미 다른 서비스가 호스트 포트를 사용한다. `docker ps --format 'table {{.Names}}\t{{.Ports}}'`로 컨테이너 측 포트를 확인한다. 다른 프로그램의 점유 여부는 아래를 참고한다.

```bash
# [Ubuntu] 추가 학습: LISTEN 중인 포트 조회
ss -lntp
```

**주의:** 기존 컨테이너 이름을 비우겠다고 무조건 `docker rm -f`를 실행하지 않는다. 데이터와 서비스 중단 영향부터 확인한다.

### 06.7 `Zone.Identifier`는 왜 생기는가? [M01 실제 경험]

Windows는 인터넷 등에서 받은 파일에 **Mark of the Web** 출처 표시를 붙일 수 있다. 일반적으로 NTFS의 대체 데이터 스트림(ADS) `Zone.Identifier` 형태지만, Windows에서 WSL/Linux 경로로 복사·압축 해제하는 과정에서는 `파일명:Zone.Identifier`가 별도 파일처럼 나타날 수 있다.

```text
bonus-compose-config.png                 # 실제 스크린샷
bonus-compose-config.png:Zone.Identifier # Windows 출처 정보가 드러난 파일
```

**검색 → 삭제 → 재확인 [M01]**

```bash
cd ~/codyssey/m01

# 1. 먼저 대상만 출력
find . -type f -name '*:Zone.Identifier'

# 2. 결과를 확인한 뒤 해당 패턴만 삭제
find . -type f -name '*:Zone.Identifier' -delete

# 3. 삭제 여부 및 Git 상태 확인
find . -type f -name '*:Zone.Identifier'
git status --short
```

- `-type f`: 일반 파일만.
- `-name '*:Zone.Identifier'`: 해당 이름으로 끝나는 파일만.
- `-delete`: 실제 삭제. **검색 결과를 확인하기 전에는 실행하지 않는다.**
- 실제 PNG 자체는 삭제하지 않는다.

**[M01]** 기존 커밋에 포함됐던 메타데이터 파일 4개의 삭제 내역이 Git에 `D`로 표시되었으며, `git add -A`는 그 삭제 상태까지 스테이징했다.

### 06.8 README의 이미지가 깨질 때 [M01]

**원인 후보:** 파일명이 다르거나, 상대 경로가 틀리거나, 대소문자가 다르거나, 파일 자체가 없다.

```bash
cd ~/codyssey/m01
ls -1 docs/screenshots

grep -oE 'docs/screenshots/[^)]+' README.md | sort -u |
while read -r file; do
    [ -f "$file" ] || echo "누락: $file"
done
```

**[M01]** README에 `github-vscode.png`로 연결했지만 실제 파일은 `vscode-wsl.png`와 `github-readme.png`였던 사례가 있었다. 링크를 실제 파일명으로 수정해 해결했다. 위 검사는 해당 패턴에 해당하는 **경로 존재 여부**만 확인하므로 모든 이미지 표시·보안 검사까지 보장하지 않는다.

### 06.9 Git 오류 빠른 분류

| 증상 | 먼저 확인 | 다음 조치 |
|---|---|---|
| `nothing to commit` | `git status` | 변경이 저장되었는지 확인 |
| `Everything up-to-date` | `git log`, `git status` | 새 커밋이 있는지 확인 |
| `remote origin already exists` | `git remote -v` | 중복 추가 대신 기존 URL 확인 |
| `Permission denied (publickey)` | `ssh -T git@github.com`, 키 등록 | 공개키 등록·키 선택·접속 설정 검토 |
| 실수로 민감 파일 추가 | `git diff --cached --name-only` | 커밋 전 스테이징 취소·비밀값 관리 |

### 06.10 보안 사고를 막는 체크리스트

- [ ] `.env`와 `.env.example` 구별
- [ ] 개인키, 액세스 토큰, 인증 코드, 비밀번호가 README·스크린샷·커밋에 없는지 확인
- [ ] `git diff --cached --name-only`로 제출 파일 목록 검토
- [ ] GitHub 웹에서도 이미지와 노출 정보 확인
- [ ] 이미 공개된 비밀값은 삭제만 하지 말고 **폐기·재발급** 검토
- [ ] 파일 삭제 전 `pwd`, 경로, `find` 검색 결과 확인

---

## Part 07. 상황별 명령어 사전

### 07.1 바로 찾는 CLI 치트시트

| 상황 | 명령어 | 실행 위치 / 주의 |
|---|---|---|
| 지금 위치 | `pwd` | Ubuntu |
| 숨김 파일까지 확인 | `ls -la` | Ubuntu |
| 상위 디렉터리 이동 | `cd ..` | Ubuntu |
| 폴더 만들기 | `mkdir -p 경로` | Ubuntu |
| 파일 생성 | `touch 파일` | Ubuntu |
| 파일 편집 | `nano 파일` | Ubuntu |
| 파일 내용 | `cat 파일` | Ubuntu |
| 파일 복사 | `cp 원본 대상` | Ubuntu |
| 파일 이동·개명 | `mv 원본 대상` | Ubuntu |
| 파일 삭제 | `rm 파일` | 주의: 휴지통 없음 |
| 파일 권한 | `ls -l 파일` | Ubuntu |
| 권한 변경 | `chmod 600 파일` | 권한 의미 이해 후 |
| 파일 찾기 | `find . -name '*.md'` | Ubuntu |
| 텍스트 찾기 | `grep -n '문구' 파일` | Ubuntu |
| Docker 이미지 | `docker images` | Ubuntu |
| 모든 컨테이너 | `docker ps -a` | Ubuntu |
| Docker 로그 | `docker logs 이름` | Ubuntu |
| Docker 리소스 | `docker stats --no-stream` | Ubuntu |
| Compose 설정 | `docker compose config` | compose.yaml 폴더 |
| Compose 시작 | `docker compose up -d` | compose.yaml 폴더 |
| Compose 상태 | `docker compose ps` | compose.yaml 폴더 |
| Compose 로그 | `docker compose logs --tail=10` | compose.yaml 폴더 |
| Compose 종료 | `docker compose down` | 기본 네트워크·컨테이너 제거 |
| Git 상태 | `git status --short` | 저장소 내부 |
| 변경 비교 | `git diff` | 저장소 내부 |
| 커밋 이력 | `git log --oneline -5` | 저장소 내부 |
| 원격 주소 | `git remote -v` | 저장소 내부 |
| SSH 인증 | `ssh -T git@github.com` | Ubuntu |

### 07.2 명령어에서 자주 만나는 옵션

| 옵션·표현 | 도구 | 의미 |
|---|---|---|
| `-a` | `ls`, `docker ps` 등 | **도구마다 의미 다름**: 숨김 파일 포함 / 종료 컨테이너 포함 |
| `-l` | `ls` | 긴 형식으로 출력 |
| `-p` | `mkdir`, `docker run` | **도구마다 의미 다름**: 부모 폴더 생성 / 포트 매핑 |
| `-d` | `docker run`, `docker compose up` | 백그라운드 실행 |
| `-it` | `docker run` | 대화형 입력 + 터미널 |
| `-f` | `find -type`, `grep -F` 등 | 옵션 이름·위치마다 의미 다름 |
| `--name` | `docker run` | 컨테이너 이름 지정 |
| `--build` | `docker compose up` | 실행 전 이미지 빌드 |
| `--no-stream` | `docker stats` | 실시간 연속 갱신 대신 한 번 출력 |
| `-n` | `grep` | 줄 번호 출력 |

**중요:** `-p`처럼 이름이 같은 옵션도 명령어에 따라 의미가 달라진다. `ls -a`와 `docker ps -a`를 같은 동작으로 외우면 안 된다.

### 07.3 위험도별 주의 명령

| 위험도 | 명령어 예시 | 위험 이유 / 대안 |
|---|---|---|
| 낮음(조회) | `pwd`, `ls`, `docker ps`, `git status` | 상태만 조회 |
| 중간(상태 변경) | `chmod`, `docker compose up`, `git add` | 실행·권한·스테이징 상태 변경 |
| 높음(파괴적) | `rm -rf`, `docker rm -f`, `docker compose down -v` | 파일·서비스·볼륨 데이터 삭제 가능 |
| 매우 높음(이력 손실) | `git reset --hard`, `git push --force` | 작업 내용·협업 이력 손실 가능 |

### 07.4 자주 묻는 Q&A

**Q. `docker ps`에 `NAMES PORTS`만 보이면 오류인가?**  
A. 아니다. 실행 중인 컨테이너가 없다는 뜻이다. `docker ps -a`로 중지된 컨테이너까지 확인한다.

**Q. `docker compose config`가 성공하면 서버도 실행 중인가?**  
A. 아니다. 설정이 정상 해석된 것뿐이다. `docker compose up -d` 후 `ps`와 실제 접속을 확인해야 한다.

**Q. `docker compose down`을 하면 Dockerfile이 삭제되는가?**  
A. 아니다. Compose가 관리하는 컨테이너와 기본 네트워크를 제거하지만 로컬 소스 파일은 유지된다. `-v` 옵션은 영속 볼륨 삭제와 관련되므로 주의한다.

**Q. `.gitignore`에 `.env`가 있으면 완전히 안전한가?**  
A. 아니다. 이미 추적·커밋된 파일에는 소급 적용되지 않으며, 화면·로그에 표시되는 비밀값도 별도로 보호해야 한다.

**Q. SSH 인증 성공 후 Push가 `Everything up-to-date`라고 나오는 이유는?**  
A. 로컬에 새로운 커밋이 없기 때문이다. SSH 인증 성공과 파일 업로드 발생은 다른 사실이다.

**Q. `Zone.Identifier`를 지우면 원본 이미지도 지워지나?**  
A. `*:Zone.Identifier`라는 이름의 일반 파일만 선택해 삭제한 경우 원본 PNG 자체는 삭제하지 않는다. 반드시 `find` 결과를 먼저 확인한다.

---

## Part 08. 복습·응용 실습

### 08.1 개념 점검 질문

정답을 보지 않고 자신의 말로 설명해 보자.

1. Windows, WSL2, Ubuntu, VS Code, Docker의 역할을 각각 설명할 수 있는가?
2. `cd my-web`과 `cd ~/codyssey/m01/my-web`의 차이는 무엇인가?
3. `chmod 600`과 `chmod 700`은 각각 어떤 권한을 의미하는가?
4. `docker run`과 `docker exec`의 차이는 무엇인가?
5. `-p 8084:80`의 왼쪽과 오른쪽은 각각 무엇인가?
6. Dockerfile의 `COPY`와 바인드 마운트의 동작 차이는 무엇인가?
7. 컨테이너 삭제 후 볼륨 데이터가 남는 이유는 무엇인가?
8. `docker compose config`와 `docker compose ps`는 무엇을 각각 확인하는가?
9. `web` 서비스 이름으로 다른 컨테이너가 접근할 수 있는 이유는 무엇인가?
10. `.env`, `.env.example`, `.gitignore` 각각의 역할은 무엇인가?
11. Git과 GitHub는 어떻게 다르며 `git add`, `commit`, `push` 순서는 왜 필요한가?
12. SSH 공개키와 개인키는 어디에 보관하는가?
13. `find`와 `grep`의 차이는 무엇인가?
14. `Zone.Identifier`가 파일처럼 나타났을 때 안전한 삭제 순서는 무엇인가?

### 08.2 결과 예측 문제

**문제 A**

```bash
printf 'one\n' > note.txt
printf 'two\n' >> note.txt
cat note.txt
```

`cat` 출력은 무엇일까? 두 연산자의 차이를 설명해 보자.

**문제 B**

```bash
docker ps --format 'table {{.Names}}\t{{.Ports}}'
```

표 머리글만 보인다면 무엇을 의미할까? 기존 컨테이너가 제거되었다고 결론 내려도 될까?

**문제 C**

```bash
git add README.md
git status --short
```

README가 수정된 상태라면 왜 `git add` 이후에도 GitHub 첫 화면이 자동 갱신되지 않을까?

### 08.3 오류 찾기 문제

**문제 D:** `docker status --no-stream`에서 잘못된 단어를 찾아 수정해 보자.

**문제 E:** `.env`에 `WEB_PORT=8084`가 있는데 `compose.yaml`에는 `"8083:80"`을 직접 작성했다. 왜 값 변경이 적용되지 않을까?

**문제 F:** `README.md`는 `docs/screenshots/github-vscode.png`를 가리키고 실제 파일은 `vscode-wsl.png`다. 어느 부분을 고쳐야 할까?

### 08.4 안전한 재실습 [확장]

실제 제출물을 훼손하지 않도록 **새 컨테이너 이름과 사용하지 않는 포트**로 재현해 본다. 아래 예시에서 `8085` 사용 가능 여부를 먼저 확인한다.

```bash
cd ~/codyssey/m01/my-web
docker ps --format 'table {{.Names}}\t{{.Ports}}'

docker build -t codyssey-review-web:1.0 .
docker run -d --name codyssey-review-web -p 127.0.0.1:8085:80 codyssey-review-web:1.0

docker ps
docker logs codyssey-review-web
```

브라우저에서 `http://localhost:8085`를 확인한 뒤, 테스트용 컨테이너만 정리한다.

```bash
docker stop codyssey-review-web
docker rm codyssey-review-web
```

**주의:** 이 재실습은 '기존 환경에서의 재실행'이다. Ubuntu·Docker를 처음부터 설치하는 **완전한 신규 환경 재현성 검증**과는 다르다.

### 08.5 복습 평가표

| 역량 | 내가 할 수 있어야 하는 일 | 확인 |
|---|---|---|
| 이해력 | WSL·Docker·Git의 역할을 말로 설명 | [ ] |
| 구현력 | Dockerfile로 이미지 빌드·웹 접속 구현 | [ ] |
| 운영력 | Compose 두 서비스 실행·로그·종료 관리 | [ ] |
| 문제 해결력 | 오류 메시지와 상태를 근거로 원인 분석 | [ ] |
| 보안 역량 | 비밀정보 제외·SSH 키 안전 보관 | [ ] |
| 응용력 | 새 프로젝트에 환경변수와 Git 루틴 적용 | [ ] |

**권장 복습:** 당일 요약 → 다음 날 개념 질문 → 3일 뒤 직접 명령 실행 → 7일 뒤 작은 환경 재현.

---

## 용어집·자료 기준

### 핵심 용어

| 용어 | 한 문장 설명 |
|---|---|
| 호스트(Host) | 컨테이너 등을 실행하는 바깥쪽 컴퓨터·환경 |
| WSL2 | Windows에서 Linux 환경을 활용할 수 있게 해주는 실행 계층 |
| 배포판(Distribution) | Linux 커널과 사용자 프로그램을 묶어 제공하는 운영 환경(예: Ubuntu) |
| CLI | 텍스트 명령으로 조작하는 인터페이스 |
| Bash | 명령어를 해석하고 실행하는 셸 |
| Docker 이미지 | 컨테이너 실행의 기반이 되는 템플릿 |
| 컨테이너 | 이미지 기반으로 실행되는 격리된 프로세스 환경 |
| 포트 매핑 | 호스트의 특정 포트를 컨테이너 포트로 전달하는 설정 |
| 바인드 마운트 | 호스트의 지정 파일·폴더를 컨테이너 경로에 연결 |
| 볼륨 | Docker가 관리하는 영속 데이터 저장 공간 |
| Compose 서비스 | Compose 설정에 정의하는 실행 구성 단위 |
| 환경변수 | 프로그램·도구가 외부에서 읽는 설정값 |
| Git 커밋 | 특정 시점의 변경을 이력으로 기록한 단위 |
| GitHub 원격 저장소 | Git 이력을 서버에서 보관·공유하는 저장소 |
| SSH 키 쌍 | 공개키 인증에 사용하는 개인키와 공개키 |
| Mark of the Web | Windows에서 다운로드 출처를 표시하는 보안 메타데이터 |

### 자료와 정확성 기준

1. **미션 원본:** `M01-내-컴퓨터에-개발자용-'작업실'-꾸미기.txt` 및 「M01 - 올인원 1기 입학 연수.pdf」의 미션 소개·기능 요구사항·보너스 5개 항목을 기준으로 범위를 정의했다. 서울캠퍼스의 OrbStack 예외 사항은 M01 원본의 별도 안내이며, 이 참고서의 실제 실습은 WSL2 Ubuntu + Docker 기반이다.
2. **직접 수행한 기록:** Codyssey-AIO-M01 저장소에 제출한 `README.md` 및 보너스 섹션, 이 대화에서 확인한 터미널 출력·스크린샷을 기준으로 `[M01]`을 표시했다. 저장소: https://github.com/bomigrace-byte/Codyssey-AIO-M01
3. **추가 지식:** `[확장]` 표시는 원본의 필수 실습이 아닌 참고·응용 정보다. 예제 출력은 실행 환경에 따라 달라질 수 있다.
4. **검증 범위:** M01 필수 및 보너스 항목은 실습 기록상 수행되었지만, 이 문서를 제작하며 새로운 Ubuntu 환경을 설치해 전체 절차를 독립적으로 재현한 것은 아니다.
5. **보안:** 실습 화면의 인증 코드나 비밀정보는 참고서에 옮기지 않는다. 경로의 사용자명, 이미지 태그, 포트 등은 실제 사례이므로 다른 컴퓨터에서는 자신의 값으로 변경한다.

---

**GRACE STACK — Learn it. Build it. Debug it.**  
*Vol.02 이후에도 각 미션은 독립된 한 권의 Markdown 실전 참고서로 이어진다.*
