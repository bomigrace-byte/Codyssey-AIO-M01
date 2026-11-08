# Codyssey M01 — 개발 워크스테이션 구축

## 1. 프로젝트 개요

**미션명:** 내 컴퓨터에 개발자용 '작업실' 꾸미기

**목적:** Linux CLI, Docker, Git/GitHub를 활용하여 재현 가능한 개발 환경을 구축하고, 실행 결과를 검증한다.

### 주요 수행 내용

- WSL 2 및 Ubuntu 기반 Linux 개발 환경 구성
- 터미널을 활용한 파일·디렉터리 관리 및 권한 설정
- Docker 이미지와 컨테이너 실행·관리
- Dockerfile 기반 커스텀 웹 서버 제작
- 포트 매핑을 통한 브라우저 접속 검증
- 바인드 마운트를 통한 실시간 파일 변경 반영
- Docker 볼륨의 데이터 영속성 검증
- Git 버전 관리 및 GitHub 원격 저장소 연동

## 2. 개발 환경

| 구분 | 환경 |
|---|---|
| 호스트 OS | Windows 11 |
| Linux 환경 | WSL 2 |
| Linux 배포판 | Ubuntu 26.04.1 LTS |
| Shell | Bash |
| Docker Engine | 29.9.3 |
| Docker Compose | v5.7.0 |
| Git (Ubuntu) | 2.53.0 |
| 웹 서버 | NGINX (Alpine 기반) |
| 에디터 | nano, VS Code (WSL 확장) |

### 2.1 Ubuntu 환경 확인

WSL2에서 Ubuntu 26.04.1 LTS가 실행되는 것을 확인했다.

![Ubuntu 버전 및 WSL 커널 확인](docs/screenshots/ubuntu-version-detail.png)

---

## 3. 필수 수행 체크리스트

- [x] Linux CLI 기본 명령어 실습
- [x] 파일 및 디렉터리 권한 변경
- [x] Docker 설치 및 엔진 점검
- [x] Docker 이미지·컨테이너 관리
- [x] hello-world 및 Ubuntu 컨테이너 실행
- [x] Dockerfile 기반 커스텀 이미지 빌드
- [x] 포트 매핑 및 웹 서버 접속
- [x] 바인드 마운트 변경 반영
- [x] Docker 볼륨 영속성 검증
- [x] Git 설정 및 GitHub Push
- [x] VS Code와 GitHub 연동 증거 확보
- [x] 제출용 로그·스크린샷 정리
- [x] 트러블슈팅 기록 및 GitHub 제출 결과 확인

## 4. Linux CLI 실습

### 4.1 파일 및 디렉터리 조작

| 명령어 | 수행 내용 |
|---|---|
| `pwd` | 현재 작업 경로 확인 |
| `ls -la` | 숨김 파일을 포함한 목록 조회 |
| `cd` | 디렉터리 이동 |
| `mkdir` | 디렉터리 생성 |
| `touch` | 빈 파일 생성 |
| `echo` | 문자열 출력 및 파일 기록 |
| `cat` | 파일 내용 확인 |
| `cp` | 파일 복사 |
| `mv` | 파일 이름 변경 |
| `rm` | 파일 삭제 |

주요 실습 명령어:

```bash
mkdir -p ~/codyssey/m01
cd ~/codyssey/m01

touch practice.txt
echo "hello Codyssey" > practice.txt
cat practice.txt

cp practice.txt copy.txt
mv copy.txt renamed.txt
rm renamed.txt
```

**검증 결과:** 파일 생성, 내용 기록, 복사, 이름 변경 및 삭제가 정상적으로 수행되었다.

### 4.1.1 절대경로와 상대경로 선택 기준 

**절대경로**는 파일시스템의 시작점 `/`부터 파일 위치를 지정한다. 예: `/home/vomefall6468/codyssey/m01/practice.txt`. 어디에서 실행해도 동일한 위치를 가리키지만 사용자명이나 설치 위치가 달라지면 경로를 수정해야 한다.

**상대경로**는 현재 작업 디렉터리(`pwd`)를 기준으로 지정한다. 예를 들어 `~/codyssey/m01`에서 `./practice.txt`, `my-web/site/index.html`은 상대경로다. 간결하고 프로젝트 이동 시 재사용하기 쉽지만 현재 위치가 달라지면 파일을 찾지 못할 수 있다.

| 비교 | 절대경로 | 상대경로 |
|---|---|---|
| 기준 | 루트(`/`)부터 | 현재 위치(`pwd`)부터 |
| M01 예시 | `/home/vomefall6468/codyssey/m01/practice.txt` | `./practice.txt` |
| 장점 | 실행 위치와 무관하게 대상이 명확함 | 짧고 프로젝트 이동에 유리함 |
| 단점 | 다른 사용자·컴퓨터에서 경로 변경 필요 | 잘못된 디렉터리에서 실행하면 실패 |
| 권장 사용 | 자동화·설정에서 고정된 위치 지정 시 | 프로젝트 내부 파일을 다룰 때 |

```bash
cd ~/codyssey/m01
pwd
cat ./practice.txt
cat /home/vomefall6468/codyssey/m01/practice.txt
```

위 명령어는 **선택 기준 설명을 위한 재현 예시**이다. `~`는 셸에서 사용자 홈 디렉터리로 확장된다. `.`은 현재 디렉터리, `..`은 상위 디렉터리다.

### 4.2 파일 및 디렉터리 권한 변경

파일 권한 변경:

```bash
chmod 600 practice.txt
ls -l practice.txt
```

결과:

```text
-rw------- ... practice.txt
```

디렉터리 권한 변경:

```bash
chmod 700 permission-test
ls -ld permission-test
```

결과:

```text
drwx------ ... permission-test
```

| 대상 | 변경 전 | 변경 후 |
|---|---|---|
| practice.txt | 644 | 600 |
| permission-test | 755 | 700 |

**검증 결과:** 파일과 디렉터리의 접근 권한 변경에 성공했다.

---

### 4.2.1 rwx 비트와 숫자 권한 매핑

권한 표기는 **소유자(user) · 그룹(group) · 기타 사용자(others)**의 3묶음이다. 각 묶음에서 `r=4`(읽기), `w=2`(쓰기), `x=1`(실행·디렉터리 경로 통과)을 더한 값이 0~7 중 한 자리 숫자가 된다.

| 권한 표시 | 계산 | 숫자 | 의미 |
|---|---|---|---|
| `---` | 0 | 0 | 권한 없음 |
| `r--` | 4 | 4 | 읽기 |
| `r-x` | 4+1 | 5 | 읽기·실행 |
| `rw-` | 4+2 | 6 | 읽기·쓰기 |
| `rwx` | 4+2+1 | 7 | 읽기·쓰기·실행 |

| 실제 명령 | 숫자 해석 | 권한 문자 표시 | M01 결과 |
|---|---|---|---|
| `chmod 600 practice.txt` | 6 / 0 / 0 | `rw- --- ---` | 소유자만 읽기·쓰기 |
| `chmod 700 permission-test` | 7 / 0 / 0 | `rwx --- ---` | 소유자만 디렉터리 접근 |
| `chmod 644 file` | 6 / 4 / 4 | `rw- r-- r--` | 일반적인 읽기 가능한 파일 권한 예시 |
| `chmod 755 dir` | 7 / 5 / 5 | `rwx r-x r-x` | 일반적인 디렉터리 권한 예시 |

파일에서 `x`는 프로그램 실행 권한을 뜻하지만 디렉터리에서는 내부 경로 통과 권한이다. **실제 검증 근거는 4.2의 `chmod 600`, `chmod 700` 실행 및 스크린샷이다.**

### 4.3 Linux CLI 및 권한 실습 증거
![CLI 실습](docs/screenshots/cli-directory.png)

![파일 권한](docs/screenshots/permission-file.png)

![디렉터리 권한](docs/screenshots/permission-directory.png)

---

## 5. Docker 설치 및 기본 운영

### 5.1 Docker 설치 확인

```bash
docker --version
docker info
```

확인 결과:

```text
Docker version 29.9.3
Server Version: 29.9.3
```

Docker CLI와 서버 연결이 정상 작동함을 확인했다.

### 5.2 Docker 공식 저장소 설정 및 설치 과정

Ubuntu 환경에 Docker 공식 APT 저장소를 구성하고 패키지 목록을 갱신한 뒤 Docker Engine과 Compose 플러그인을 설치했다.

![Docker 저장소 설정](docs/screenshots/docker-apt-repository.png)

![Docker 패키지 목록 갱신](docs/screenshots/docker-apt-update.png)

![Docker 및 Compose 설치 완료](docs/screenshots/docker-apt-install.png)

---

### 5.3 hello-world 실행

```bash
docker run hello-world
```

출력 결과:

```text
Hello from Docker!
```

Docker 이미지를 사용하여 컨테이너를 생성하고 실행하는 데 성공했다.

---

### 5.4 Ubuntu 컨테이너 실습

```bash
docker run -it ubuntu bash
```

컨테이너 내부에서 다음 명령어를 실행했다.

```bash
pwd
ls
echo "hello from Ubuntu container"
exit
```

종료한 컨테이너를 재시작하여 기존 컨테이너에 다시 접속하는 과정도 확인했다.

### 5.5 Docker 운영 명령어

| 명령어 | 검증 내용 |
|---|---|
| `docker images` | 로컬 이미지 목록 확인 |
| `docker ps` | 실행 중인 컨테이너 확인 |
| `docker ps -a` | 종료된 컨테이너 포함 목록 확인 |
| `docker start` | 기존 컨테이너 재시작 |
| `docker exec` | 실행 중인 컨테이너 내부 명령 실행 |
| `docker logs` | 컨테이너 로그 확인 |
| `docker stats --no-stream` | CPU·메모리 사용량 확인 |
| `docker stop` | 컨테이너 중지 |

리소스 측정 결과:

```text
CPU: 0.00%
MEM USAGE: 3.094MiB / 7.715GiB
MEM %: 0.04%
```

**검증 결과:** Docker 기본 운영 명령어의 정상 작동을 확인했다.

### 5.6 `docker images`와 `docker ps -a` 실제 출력

다음은 **M01 실습 당시 보관한 터미널 스크린샷의 핵심 열을 발췌한 기록**이다. 이후 보너스 실습으로 이미지와 컨테이너가 추가되었으므로 현재 상태와 다를 수 있다.

```bash
docker images
docker ps -a
```

**이미지 목록 (`docker images`, 당시 화면 중 일부):**

```text
IMAGE                    IMAGE ID
codyssey-01-web:1.0      3b730966ac6b
hello-world:latest       c567b231f656
my-web:latest            f9bb98305d56
ubuntu:latest            6a06111c3b0a
```

**전체 컨테이너 목록 (`docker ps -a`, Ubuntu 컨테이너 실습 당시 일부):**

```text
CONTAINER ID    IMAGE          STATUS
cc4aff576937    ubuntu         Exited (0)
843dba554c98    hello-world    Exited (0)
5b6f9aa0686b    my-web         Exited (255)
```

`docker images`는 실행용 이미지 목록이고 `docker ps -a`는 **종료된 것까지 포함한 컨테이너 목록**이다. `docker ps`는 실행 중인 컨테이너만 표시한다. `Exited (0)`은 정상 종료를 나타내며, `Exited (255)`는 비정상 종료 가능성이 있으므로 로그·실행 이력을 추가 확인해야 한다.

> **증거 구분:** 위 텍스트는 당시 스크린샷에서 옮긴 발췌 기록이다. 원본 전체 출력 화면이 README에 첨부된 것은 아니므로, 평가자가 원본 출력을 요구한다면 `docker images`, `docker ps -a`를 현재 환경에서 다시 실행해 캡처해 추가한다.

## 6. Dockerfile 기반 웹 서버 구축

### 6.1 프로젝트 구조

```text
my-web/
├── Dockerfile
└── site/
    └── index.html
```

### 6.2 베이스 이미지 선정

`nginx:alpine`을 베이스 이미지로 선택했다.

NGINX는 정적 웹 콘텐츠를 제공하는 웹 서버이며, Alpine 기반 이미지를 활용하여 별도의 복잡한 웹 애플리케이션 없이 실습 환경을 구성했다.

### 6.3 Dockerfile 작성

```dockerfile
FROM nginx:alpine
COPY site/ /usr/share/nginx/html/
```

**커스텀 내용:** 직접 작성한 `index.html`을 NGINX의 정적 콘텐츠 디렉터리에 복사했다.

### 6.4 커스텀 이미지 빌드

```bash
cd ~/codyssey/m01/my-web
docker build -t codyssey-01-web:1.0 .
docker images
```

검증 결과:

- 커스텀 이미지 빌드 성공
- 생성된 이미지: `codyssey-01-web:1.0`
- 이미지 ID: `3b730966ac6b`

![Docker 이미지 빌드 성공](docs/screenshots/docker-build.png)

### 6.5 이미지 빌드·실행·변경의 차이와 흐름

**이미지(image)는 실행 환경을 담은 배포 단위**, **컨테이너(container)는 그 이미지를 기반으로 만들어 실행하는 인스턴스**다. M01에서는 `Dockerfile`에 `FROM nginx:alpine`과 `COPY site/ /usr/share/nginx/html/`을 작성하고 `docker build -t codyssey-01-web:1.0 .`으로 이미지를 빌드했다. `docker run -d -p 8081:80 --name codyssey-m01-web codyssey-01-web:1.0`을 실행하자 그 이미지로 NGINX 컨테이너가 만들어졌다. 이후 원본 `site/index.html`을 수정해도 **기존 이미지에 `COPY`된 파일은 자동으로 바뀌지 않는다.** 이미지를 다시 빌드해 새 컨테이너에 적용하거나, 8장의 **바인드 마운트**를 사용하면 이미지 재빌드 없이 호스트 파일의 변경을 반영할 수 있다.

```text
Dockerfile + site/index.html
        │ docker build
        ▼
Docker 이미지: codyssey-01-web:1.0
        │ docker run -p 8081:80
        ▼
실행 중인 NGINX 컨테이너 → 브라우저 접속
        │
        ├─ COPY된 파일 수정: 이미지 재빌드 및 새 컨테이너 적용
        └─ 바인드 마운트된 파일 수정: 이미지 재빌드 없이 반영
```

검증 근거는 6.4의 이미지 빌드, 7장의 브라우저 접속, 8장의 바인드 마운트 전후 결과다.

## 7. 포트 매핑 검증

### 7.1 컨테이너 실행

```bash
docker run -d -p 8081:80 --name codyssey-m01-web codyssey-01-web:1.0
```

실행 상태 확인:

```bash
docker ps
```

포트 매핑:

```text
0.0.0.0:8081->80/tcp
```

### 7.1.1 네트워크 네임스페이스와 포트 포워딩의 필요성

**네트워크 네임스페이스(network namespace)**는 IP 주소·네트워크 인터페이스·라우팅 등 네트워크 자원을 격리하는 Linux 기능이다. Docker의 일반적인 브리지 네트워크에서는 컨테이너가 호스트와 분리된 네트워크 공간을 사용하므로, 컨테이너의 NGINX가 **80번 포트**를 열었다고 해서 호스트 브라우저가 그 포트에 직접 접속하는 것은 아니다. `-p 8081:80`은 **호스트 8081번 포트로 들어오는 연결을 컨테이너 내부 80번 포트로 전달**하도록 게시하는 설정이다. 환경에 따라 NAT 또는 프록시 방식을 사용할 수 있다.

```text
Windows 브라우저: http://localhost:8081
                  │
           호스트 포트 8081
                  │ Docker 포트 전달
                  ▼
   컨테이너 네트워크 네임스페이스
                  │
            NGINX 포트 80
```

**M01 검증:** `docker ps`에 `0.0.0.0:8081->80/tcp`가 보였고, 브라우저에서 `Hello Codyssey!`가 출력되었다. 이 구조를 이해하면 컨테이너가 정상 실행 중이어도 외부에서 접속할 수 없는 이유를 분석할 수 있다.

### 7.2 브라우저 접속

접속 주소: `http://localhost:8081`

확인 결과:

```text
Hello Codyssey!
My first Docker web server.
```

**검증 결과: PASS**

Docker 컨테이너의 80 포트와 호스트의 8081 포트를 연결하여 웹페이지 접속에 성공했다.

---

### 7.3 실행 결과 스크린샷

![Docker 웹 서버 포트 매핑 결과](docs/screenshots/port-8081.png)

---

## 8. 바인드 마운트 검증

### 8.1 바인드 마운트 적용

```bash
docker run -d -p 8082:80 \
  --name codyssey-m01-bind \
  --mount type=bind,source="$(pwd)/site",target=/usr/share/nginx/html,readonly \
  codyssey-01-web:1.0
```

### 8.2 변경사항 반영

호스트의 `site/index.html`을 수정했다.

수정 후 브라우저에서 확인한 결과:

```text
Hello Docker Bind Mount!
This page was updated without rebuilding the image.
```

접속 주소: `http://localhost:8082`

**검증 결과: PASS**

Docker 이미지를 다시 빌드하지 않고 호스트 파일의 변경사항이 웹페이지에 반영되는 것을 확인했다.

### 8.3 바인드 마운트 변경 전후 증거
![변경 전](docs/screenshots/bind-before.png)

![변경 후](docs/screenshots/bind-after.png)

---

## 9. Docker 볼륨 영속성 검증

### 9.1 볼륨 생성

```bash
docker volume create codyssey-m01-data
docker volume ls
```

### 9.2 데이터 저장

```bash
docker run -d --name codyssey-m01-vol1 \
  -v codyssey-m01-data:/data \
  ubuntu sleep infinity
```

```bash
docker exec codyssey-m01-vol1 \
  sh -c 'echo "M01 volume test" > /data/test.txt'
```

### 9.3 컨테이너 교체

```bash
docker rm -f codyssey-m01-vol1
```

동일한 볼륨을 연결하여 새로운 컨테이너를 생성했다.

```bash
docker run -d --name codyssey-m01-vol2 \
  -v codyssey-m01-data:/data \
  ubuntu sleep infinity
```

### 9.4 데이터 유지 확인

```bash
docker exec codyssey-m01-vol2 cat /data/test.txt
```

출력 결과:

```text
M01 volume test
```

**검증 결과: PASS**

기존 컨테이너를 제거하고 새로운 컨테이너에서 같은 볼륨을 연결한 뒤 저장된 데이터를 확인했다.

이를 통해 Docker 볼륨을 사용하면 컨테이너와 별도로 데이터를 유지할 수 있음을 확인했다.

---

### 9.5 Docker 볼륨 영속성 증거

![데이터 저장 전 컨테이너 확인](docs/screenshots/volume-before.png)

![컨테이너 교체 후 데이터 유지 확인](docs/screenshots/volume-after.png)

---

## 10. Git 및 GitHub 연동

### 10.1 Git 초기 설정

```bash
git config --global user.name "작성자명"
git config --global user.email "GitHub 등록 이메일"
git config --global init.defaultBranch main
git config --global --list
```

### 10.2 Git 저장소 초기화

```bash
cd ~/codyssey/m01
git init
git status
```

### 10.3 첫 커밋 생성

```bash
git add .
git status
git commit -m "Initial commit: M01 development environment"
git log --oneline
git status
```

확인된 첫 번째 커밋:

```text
431c074 Initial commit: M01 development environment
```

### 10.4 GitHub 원격 저장소 연결

```bash
git remote add origin https://github.com/bomigrace-byte/Codyssey-AIO-M01.git
git branch -M main
git remote -v
```

### 10.5 GitHub Push

GitHub CLI를 통해 HTTPS 인증을 구성한 후 첫 번째 Push를 완료했다.

```bash
gh auth setup-git
git push -u origin main
```

결과:

```text
[new branch] main -> main
branch 'main' set up to track 'origin/main'.
```

**검증 결과: PASS**

GitHub 저장소:
https://github.com/bomigrace-byte/Codyssey-AIO-M01


### 10.6 VS Code 연동

**진행 상태: 완료**

VS Code의 WSL 확장을 이용하여 Ubuntu 개발 환경에 연결하고, M01 프로젝트 폴더를 열어 작업하였다.

VS Code의 Source Control에서 Git 변경 사항을 확인하고, README.md를 수정한 뒤 GitHub에 커밋 및 Push하였다.

GitHub 저장소에서도 README와 실습 스크린샷이 정상적으로 표시되는 것을 확인하였다.

---

**검증 증거**

![VS Code와 WSL 연결](docs/screenshots/vscode-wsl.png)

![GitHub README 확인](docs/screenshots/github-readme.png)

---


## 11. 트러블슈팅

### 사례 1. Docker 포트 매핑 실행 오류

**문제**

```text
docker: invalid reference format
```

**원인 가설**

컨테이너 실행 시 포트 매핑 옵션 `-p`를 누락하여 명령어 형식이 잘못되었을 가능성을 확인했다.

**확인**

실제로 입력한 명령어와 Docker의 오류 메시지를 비교했다.

**해결**

올바른 포트 매핑 옵션을 적용했다.

```bash
docker run -d -p 8081:80 --name codyssey-m01-web codyssey-01-web:1.0
```

**검증**

`docker ps`에서 컨테이너가 실행 중임을 확인했고, 브라우저에서 `localhost:8081` 접속에 성공했다.

![포트 매핑 오류 및 확인 과정](docs/screenshots/troubleshoot-port.png)

### 사례 2. 바인드 마운트 웹 서버 접속 실패

**문제**

```text
ERR_CONNECTION_REFUSED
```

**원인 가설**

8082 포트에 연결되는 컨테이너가 실행되지 않았을 가능성을 검토했다.

**확인**

```bash
docker ps -a
docker logs codyssey-m01-bind
docker inspect codyssey-m01-bind
```

확인 결과 `codyssey-m01-bind` 컨테이너가 존재하지 않았다.

**해결**

바인드 마운트 설정을 포함한 `docker run` 명령어로 컨테이너를 다시 생성했다.

**검증**

`localhost:8082` 접속에 성공했으며, HTML 수정 후 변경사항이 정상 반영되었다.

![바인드 마운트 접속 오류 확인](docs/screenshots/troubleshoot-bind.png)

### 사례 3. Docker 리소스 확인 명령어 오류

**문제**

`docker status --no-stream` 실행 시 옵션 오류 발생

**원인**

`status`와 `stats` 명령어 혼동

**해결**

```bash
docker stats --no-stream cc4aff576937
```

**검증**

CPU·메모리 등 컨테이너 리소스 사용량이 정상 출력되었다.

### 사례 4. 포트 충돌 진단 및 안전한 해제 방법 — 

**상황 예시:** 다른 서비스가 이미 호스트 포트 8084를 사용하면 Docker가 `port is already allocated` 또는 `address already in use` 등의 오류를 출력할 수 있다. 아래는 **추가 학습용 진단 절차**이며 M01에서 포트 충돌을 새로 재현해 해결했다는 기록은 아니다.

```bash
# ① Docker에서 어떤 컨테이너가 포트를 사용하는지 확인
docker ps --format "table {{.Names}}\t{{.Ports}}"

# ② Ubuntu에서 8084를 사용 중인 프로세스 확인
sudo ss -lntp '( sport = :8084 )'

# ③ lsof가 설치되어 있을 때의 대안
sudo lsof -nP -iTCP:8084 -sTCP:LISTEN

# ④ 본인 Compose 프로젝트가 점유한 경우에만 종료
cd ~/codyssey/m01/my-web
docker compose down

# ⑤ 개별 Docker 컨테이너가 점유했다면 이름을 확인하고 중지
# docker stop <컨테이너_이름>
```

| 진단 결과 | 해결 방법 |
|---|---|
| `docker ps`에서 해당 포트를 사용 중 | 해당 컨테이너 중지 또는 Compose 설정의 호스트 포트 변경 |
| `ss`/`lsof`에서 일반 프로세스 확인 | 프로세스 이름·PID가 본인 실행 작업인지 확인한 후 정상 종료 |
| 점유 프로세스를 찾지 못함 | Windows·WSL 측 리스너, 포트 바인딩 및 일시적 상태를 추가 점검 |

`ss`는 일반적으로 `iproute2`에 포함되어 있고 `lsof`는 별도 설치가 필요할 수 있다. `netstat -tulpn`도 대안이지만 환경에 따라 `net-tools` 설치가 필요하다. **PID를 확인하지 않은 `kill -9`나 데이터를 지우는 `docker compose down -v`는 사용하지 않는다.** 포트 변경으로 해결하려면 `.env`의 `WEB_PORT`를 비어 있는 다른 포트로 바꿀 수 있다.

## 12. 보너스 과제 실습 및 검증 (2026-11-08)

필수 미션에서 만든 `my-web/Dockerfile`과 `site/index.html`을 재사용하여 Docker Compose와 GitHub SSH 인증을 실습하였다. 아래 보너스 과제 5개는 **로컬 실습 검증 완료** 상태이며, 관련 설정 파일과 증거 이미지는 GitHub 저장소에 반영하였다.

### 12.1 Docker Compose 기본 구성 — PASS

`my-web/compose.yaml`에 `web` 서비스를 정의하고, 기존 NGINX 이미지를 빌드하여 호스트의 8083 포트로 실행했다.

```yaml
services:
  web:
    build: .
    ports:
      - "8083:80"
```

```bash
cd ~/codyssey/m01/my-web
docker compose config
docker compose up -d --build
docker compose ps
```

**검증 결과:** 서비스 `web`이 `Up`으로 실행되었으며, `http://localhost:8083`에서 웹페이지를 확인했다. 표시된 `Hello Docker Bind Mount!` 문구는 기존 HTML 내용을 이미지에 복사한 결과로, 이 Compose 설정 자체에 바인드 마운트가 포함된 것은 아니다.

![Compose 기본 설정 확인](docs/screenshots/bonus-compose-config.png)

![Compose 웹 서버 8083 접속](docs/screenshots/bonus-compose-browser-8083.png)

### 12.2 멀티 컨테이너와 내부 네트워크 — PASS

기존 `web` 외에 HTTP 요청 테스트용 `tester` 서비스를 추가했다.

```yaml
  tester:
    image: curlimages/curl:latest
    command: ["sleep", "infinity"]
    depends_on:
      - web
```

위 코드는 `services:` 아래 `web`과 같은 깊이에 추가한 부분이다. `depends_on`은 시작 순서를 지정하며 서비스 준비 완료까지 보장하지는 않는다.

```bash
docker compose config
docker compose up -d
docker compose ps
docker compose exec tester curl -I http://web
```

**검증 결과:** `web`, `tester` 모두 `Up` 상태였고, `tester`에서 서비스 이름 `web`으로 HTTP 요청을 보내 `HTTP/1.1 200 OK`를 받았다. Compose 기본 네트워크의 서비스 이름 기반 통신을 확인했다.

![두 서비스 동시 실행](docs/screenshots/bonus-multicontainer-up.png)

![컨테이너 간 HTTP 200 응답](docs/screenshots/bonus-internal-network.png)

### 12.3 Docker Compose 운영 — PASS

```bash
docker compose ps
docker compose logs --tail=10
docker compose down
docker compose ps
docker compose up -d
docker compose ps
```

**검증 결과:** 상태 조회와 로그 확인, Compose 컨테이너 및 기본 네트워크 제거, 서비스 재생성 후 두 컨테이너의 `Up` 상태를 확인했다. 로그 중 `/favicon.ico`의 404는 아이콘 파일 부재로 발생한 부수 요청이며, 웹페이지 요청은 200 응답이었다.

![Compose 로그 및 down 검증](docs/screenshots/bonus-compose-operations.png)

![Compose 재실행 후 두 서비스 Up](docs/screenshots/bonus-compose-restart.png)

### 12.4 환경변수로 웹 서버 포트 변경 — PASS

`my-web/.env`에 `WEB_PORT=8084`를 정의하고, `compose.yaml`의 포트 설정을 환경변수 참조로 변경했다.

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

```dotenv
# my-web/.env.example (공개 가능한 예시값)
WEB_PORT=8084
```

```bash
docker compose config
docker compose up -d
docker compose ps
```

**검증 결과:** `docker compose config`에서 `published: "8084"` 확인, 컨테이너 재생성 후 `8084->80/tcp` 확인, `http://localhost:8084` 접속 성공.

보안 및 협업을 위해 실제 `.env`는 `.gitignore`로 제외하고, `.env.example`만 버전 관리 대상으로 유지한다. `.env.example`에는 실제 비밀번호나 API 키를 기록하지 않는다.

![환경변수 치환 결과 8084](docs/screenshots/bonus-env-config.png)

![컨테이너 포트 변경 결과](docs/screenshots/bonus-env-port-mapping.png)

![8084 브라우저 접속](docs/screenshots/bonus-env-browser-8084.png)

![환경 파일 Git 추적 제외 검증](docs/screenshots/bonus-gitignore-verification.png)

### 12.5 GitHub SSH 인증 및 Push — PASS

Ubuntu에서 Ed25519 SSH 키 쌍을 생성하고 공개키를 GitHub 계정에 등록했다. GitHub SSH 인증 테스트에서 성공 메시지를 확인한 뒤, M01 원격 저장소의 인증 방식을 HTTPS에서 SSH로 변경했다.

```bash
# 공개키를 GitHub에 등록한 후 인증 시험
ssh -T git@github.com

cd ~/codyssey/m01
git remote set-url origin git@github.com:bomigrace-byte/Codyssey-AIO-M01.git
git remote -v
git push origin main
```

**검증 결과:** GitHub SSH 인증 성공, `origin`의 fetch/push 주소가 SSH 형식으로 변경됨, `git push origin main`이 `Everything up-to-date`를 반환함. 이는 기존 커밋 기준으로 SSH를 통한 원격 연결이 성공했다는 뜻이며, 보너스 산출물이 새로 업로드되었다는 뜻은 아니다.

개인키(`~/.ssh/id_ed25519`)와 암호는 저장소·README·스크린샷에 포함하지 않는다.

![SSH 원격 주소 및 Push 성공](docs/screenshots/bonus-ssh-push.png)

### 12.6 보너스 과제 검증 요약

| 보너스 과제 | 실습 검증 |
|---|---|
| Docker Compose 단일 웹 서비스 | PASS |
| 두 컨테이너 및 내부 HTTP 통신 | PASS |
| Compose 운영(`ps`, `logs`, `down`, `up`) | PASS |
| `.env` 환경변수 포트 변경 | PASS |
| GitHub SSH 인증 및 Push | PASS |

**실습 산출물:** `my-web/compose.yaml`, `my-web/.env.example`, `docs/screenshots/bonus-*.png`. 실제 `.env`와 SSH 개인키는 GitHub 업로드 대상이 아니다.

---

## 13. 최종 검증 결과 및 제출 현황

| 검증 항목 | 결과 |
|---|---|
| Linux CLI | PASS |
| 파일·디렉터리 권한 | PASS |
| Docker 설치 및 기본 운영 | PASS |
| Ubuntu 컨테이너 실행 | PASS |
| Dockerfile 이미지 빌드 | PASS |
| 웹 서버 포트 매핑 | PASS |
| 바인드 마운트 변경 반영 | PASS |
| Docker 볼륨 영속성 | PASS |
| GitHub 첫 Push | PASS |
| VS Code와 WSL 연결 및 GitHub 연동 | PASS |
| README 스크린샷 링크 및 GitHub 표시 확인 | PASS |
| M01 보너스 과제 5개 실습 검증 | PASS |
| 보너스 산출물 GitHub 최종 반영 | PASS |

### 제출 전 확인사항

- [x] 필수 수행 로그와 스크린샷 연결
- [x] VS Code와 GitHub 연동 확인
- [x] README 이미지 링크 및 GitHub 표시 확인
- [x] 필수 미션 보고서 Git commit 및 push
- [x] 개인정보·인증 코드·토큰 노출 여부 최종 점검
- [x] 보너스 README·Compose 설정·스크린샷을 GitHub에 반영

---

> **최종 정리:** M01 필수 과제와 보너스 5개 실습 결과 및 관련 설정·증거 파일은 GitHub에 반영했다. 본 보고서는 실제 수행·검증한 내용 중심으로 관리한다.

**학습 과정:** 2026 경남이노베이션아카데미 AI 올인원 제1기 입학연수과정

**미션:** M01 — 내 컴퓨터에 개발자용 '작업실' 꾸미기

