# Linux

## 1. 개요

### 1) Linux

- 토발즈가 MINIX 라는 교육용 운영체제를 참조하여 개발

- Linux는 유닉스 계열의 운영체제

- Linux = Linus + UNIX

- **UNIX**:

- 1969년에 AT&T의 벨 연구소에서 어셈블리어로 처음 개발

- 리눅스 계통도

- 데비안 계열: 우분투 리눅스
- 레드햇 계열: 페도라, CentOS(Locky), 레드햇 엔터프라이즈
- 슬랙웨어 계열: SuSE

- 운영체제의 구조

- Kernel
  - 운영체제의 핵심
  - 프로세스/메모리/파일 시스템/장치 관리 등의 기능 수행
  - 컴퓨터의 모든 자원 초기화 및 제어 기능 수행
  - Linux는 커널의 대부분이 C언어로 개발됨

- Shell
  - 명령어 해석기
  - 유저가 전달한 명령을 해석해서 커널에 전달하는 역할

- Application
  - 개발 도구
  - 유틸리티

### 2) Ubuntu Linux

- **개요**:

- 데비안 계열에서 가장 성공한 배포판

- **가상화**:

- 하나의 물리적 컴퓨터에 여러 운영체제를 실행할 수 있게 하는 기술로 하나의 실제 컴퓨팅 자원(CPU, Memory, Storage, Network 등)을 마치 여러 개 인 것 처럼 가상으로 쪼개서 사용하거나 여러 개의 실물 컴퓨팅 자원들을 묶어서 하나의 자원처럼 사용하는 것

- 가상화 방법
VM(Virtual Machine) - 하이퍼바이저를 이용
Container - OS 수준에서 프로세스를 컨테이너로 격리

- 가상 머신

- PC에 설치되어 있는 운영체제(HOST OS)에 가상의 머신을 생성한 후 여기에 다른 운영체제를 설치(Guest OS)할 수 있도록 해주는 소프트웨어
- 종류

| 제품 | Host OS | Guest OS |
| --- | --- | --- |
| VMWare | Windows, Linux, Mac OS | Windows, Linux, Solaris, Mac OS |
| Virtual PC | Windows | Windows, Linux, Solaris |
| Virtual Box | Windows, Linux, Mac OS, Solaris | Windows, Linux, Solaris, Mac OS, Open BSD |
| UTM | Mac OS(Silicon) | Linux |

- Windows의 WSL
Power Shell에서 설치를 하면 Ubuntu Linux의 Kernel이 설치되서 가상화하는 효과를 만들 수 있습니다.

### 3) 가상 머신(Virtual Box)을 이용한 Ubuntu Linux 설치

- 우분투 리눅스 이미지 다운로드

- Server: CLI(Command Line Interface) 환경 - https://ubuntu.com/download/server
나중에 GUI 설치 가능

```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install --no-install-recommends ubuntu-desktop
```

- Desktop: GUI(Graphic User Interface) 환경 - https://ubuntu.com/download/desktop

- Virtual Box 다운로드 및 설치

- 다운로드: https://www.virtualbox.org/wiki/Downloads
- 설치 시 Visual C++ 재배포 패키지 설치를 요구할 수 있음

- Virtual Box에 Ubuntu Linux 설치

- 새로 만들기를 클릭
VM Name 과 저장할 디렉토리 설정

ISO Image에서 다운로드 받은 이미지를 선택

무인 설치를 해제(Proceed with Unattended Installation)

다음을 누르면 메모리와 CPU를 설정: 쿠버네티스 마스터 노드는 CPU 코어 수가 2개 이상이어야 합니다.

디스크 사이즈 설정

다음을 누르면 요약이 나오고 완료를 클릭

VM이 생성됨

- 처음 VM을 만들고 시작을 하면 설치를 진행합니다.
언어 선택: 서버 버전에는 한국어가 없음

기본 NIC의 이름과 IP 설정 방법 확인

Proxy 설정 확인

디스크 설정

파일시스템 설정

컴퓨터 이름 과 유저 설정

Ubuntu Pro 업데이트 여부 확인 - 패스

SSH Server 설치 및 실행 여부

유틸리티 설치 여부

- 재부팅을 하고 로그인

- Windows에 SSH 서버에 접속하기 위한 프로그램 설치

- putty 나 Open SSH 클라이언트를 많이 사용

### 4) Ubuntu Server에 Open SSH를 설치해서 원격 접속

- openssh-server 설치

```bash
sudo apt update

sudo apt install openssh-server
```

- ssh 서비스 실행 및 확인

```bash
sudo systemctl start ssh

sudo systemctl status ssh
```

- 방화벽에서 포트 개방

```bash
sudo ufw allow ssh
```

- Linux의 IP 확인: 10.0.2.15

```bash
hostname -I
```

- 호스트 컴퓨터의 IP 확인: 192.168.201.16

```bash
ipconfig
```
윈도우즈가 아닌 Mac의 경우 설정에서 네트워크를 확인하거나 ifconfig 명령

- Virtual Box에서 NAT 설정

- Virtual Box에서 [설정] - [네트워크]
- 포트포워딩을 눌러서 추가 버튼을 누르고 설정
이름은 아무거나 하면 되고 호스트 주소는 192.168.201.16 게스트 주소는 10.0.2.15 게스트 포트는 22 호스트 포트는 정할 수 있는데 저는 22

- 접속

```bash
ssh 계정@호스트IP -p 호스트포트번호
```

### 5) 종료

- GUI 경우는 오른쪽 상단의 아이콘을 클릭해서 컴퓨터끄기/로그아웃 선택

- 터미널에서 종료

```bash
poweroff
shutdown -P now
halt -p
init 0
```

- shutdown을 이용한 종료

```bash
shutdown -P +숫자: 숫자 분 이후에 종료
shutdown -r 시간: 시간에 종료
shutdown -k +숫자: 접속된 모든 사용자에게 숫자 이후에 종료된다는 메시지가 전송되지만 종료되지 않음
shutdown -c: 예약 취소
```

### 6) 재부팅

```bash
reboot
shutdown -r now
init 6
```

### 7) Run Level

- 시스템을 가동하는 방법으로 init 명령 과 함께 사용

| Runlevel | Mode | Description |
| --- | --- | --- |
| 0 | Power Off | 종료 |
| 1 | Rescue | 시스템 복구 모드(단일 사용자) |
| 2 | Multi-User | 사용하지 않음 |
| 3 | Multi-User | 텍스트 모드 |
| 4 | Multi-User | 사용하지 않음 |
| 5 | Graphical | 그래픽 모드 |
| 6 | Reboot | 재부팅 |

### 8) 명령어 입력 및 실행

- **Shell**:

- 사용자가 입력한 명령을 해석해서 커널로 전달하거나 커널의 처리 결과를 사용자에게 전달하는 역할을 수행하는 구성요소
- Server의 텍스트 모드나 X Window(GUI) 의 터미널에서 사용

- 프롬프트

```bash
adam@basic:~$
```

- 슈퍼사용자라면 달러 기호 대신에 #
- 로그인하면 보여지는 셸을 Login Shell 이라고 합니다.
로그인 셸을 확인하는 방법

```bash
echo $SHELL
```
- 명령을 사용하는 방법
직접 입력해서 결과를 바로 확인하는 방식 - 대화식
미리 파일에 기록해두고 그 파일을 Shell에 넘겨서 한꺼번에 수행하는 방식 - 스크립트 방식
~ 은 사용자의 홈 디렉토리(~의 자리는 현재 디렉토리를 나타냄)
adam 은 사용자
basic 은 호스트 이름

- 명령행 편집

- 커서 이동: CTRL +b(뒤로), CTRL + f(앞으로), CTRL + a(가장 앞으로), CTRL + e(가장 뒤로), ESC + b(단어 뒤로), ESC + f(단어 앞으로)
- 단어 지우기: CTRL + w
- 행 지우기(잘라내기): CTRL + u(현재 위치부터 맨 앞 문자까지), CTRL + k(현재 위치부터 끝까지)
- 잘라내기 한 내용 붙이기: CTRL + y

- 명령행 작성 시 발생하는 에러 대처

- 키보드 입력이 안되는 경우: 화면을 잠그는 CTRL + s 를 누른 경우이므로 CTRL + q 로 화면 표시 잠금 해제
- 실행한 명령이 종료되지 않아서 프롬프트가 뜨지 않는 경우: CTRL + c 로 강제 종료
- 프롬프트의 문자가 깨지는 경우: CTRL + l을 눌러서 화면을 클리어

- 명령의 구조

- 형식: 명령어 [옵션] [인자]

- 옵션: - 또는 --로 시작
-는 옵션이 한 문자
--는 옵션이 단어
옵션은 여러 개 사용 가능: ls -a -l
옵션은 대부분 순서가 없음: ls -l -a
옵션이 문자인 경우 결합이 가능: ls -al

- 인자는 명령어를 수행하기 위해서 전달되는 값
- 각각의 요소는 공백으로 구분
- [ ]는 생략 가능
- | 로 구분된 경우는 선택
- 문자열, { }, < > 은 필수

- 실습
디렉토리의 내용을 살펴보는 명령어: ls
a 옵션 과 함께 사용
a 와 l 옵션과 함께 사용
/tmp 라는 인자와 함께 사용
a 옵션과 /tmp 라는 인자와 함께 사용

- 명령어 자동 완성

- 명령어의 시작 부분을 입력하고 Tab을 누르는 경우 명령어가 1개 밖에 없으면 명령어를 자동완성 해줍니다.
eg로 시작하는 명령이 egrep 하나인 경우 eg만 입력하고 tab을 누르면 명령어가 자동완성 됩니다.

- 명령의 시작 부분을 입력하고 Tab을 두번 누르면 시작하는 모든 명령어가 출력됩니다.
e를 입력하고 Tab을 두번 누르면 e로 시작하는 모든 명령어가 출력됩니다.

- **history**:

- 위 화살표(CTRL + p)를 누르면 이전 명령을 아래 화살표(CTRL + n)를 누르면 다음 명령을 호출할 수 있음

- CTRL + r를 이용하면 이전에 실행한 명령어를 검색할 수 있는데 증분 검색 모드이므로 입력을 할 때 마다 현재 입력된 내용을 기반으로 검색 

- history라고 직접 입력하면 명령어 수행 내용 출력
!!: 직전 명령어
!번호: 번호에 해당하는 명령어
history -d 라인번호: 명령어 목록에서 삭제
history -c: 명령어 목록 전부 삭제
실제 내용은 홈 디렉토리의 .bash_history 파일에 저장(nano .bash_history)

- --help 옵션

- 명령어의 도움말을 출력해주는 옵션
- 출력 내용
사용 방법
명령어에 대한 개요
지정할 수 있는 옵션 목록 과 그 의미

- cat 이라는 명령의 사용법과 의미

- Shell이 명령어를 찾는 위치 확인:  환경변수 PATH를 확인

```bash
echo $PATH
```

- 명령어를 이름만 입력한 경우 PATH에서 찾습니다.
- 현재 디렉토리의 명령어를 실행하고자 하는 경우는 ./명령어 형태로 입력해야 합니다.
- 압축을 풀어서 프로그램을 설치하는 경우 명령어 나 실행파일 속한 디렉토리를 PATH에 추가해주거나 PATH에 설정된 디렉토리로 옮겨 주어야만 명령어 입력만으로 실행을 시킬 수 있습니다.

- 명령어 위치 확인

- whereis
명령어의 실행 파일 위치, 소스 위치, man 페이지 파일의 위치를 찾아주는 명령어

기본 형식

```bash
    whereis [옵션] [명령어]
```

옵션
    -b: 바이너리 파일만 검색
    -s: 소스 파일만 검색
    -m: 메뉴얼 파일만 검색

- which
PATH 환경변수에 저장된 디렉토리에서 명령어의 위치를 검색
```bash
which [옵션] [명령어]
```

옵션
-a --all: 모든 내용을 출력
-i --read-alias: 별명 설정 환경을 출력
-v -V, --version: 버전 정보 출력

### 9) 기본 명령어

- passwd: 비밀번호 변경 명령어

```bash
passwd [계정]
```

- passwd만 입력하면 현재 접속 중인 계정의 비밀번호를 변경
- passwd 계정을 입력하면 계정의 비밀번호를 변경

- 터미널 종료: exit

- 현재 보이는 화면 삭제: clear

- 별명 관련 명령

- 현재 설정된 별명을 확인: alias

- 설정: alias 별명='설정값'

```bash
ls -F 라는 명령을 ls 라는 별명으로 설정: alias ls='ls -F'
```

- type 명령을 이용하면 별명인지 확인 가능
type ls: 별명이라서 누구의 별명인지 출력
type cp: 원본 파일의 경로가 출력

- 별명 삭제: unalias

- 별명이 있는 경우 원본 명령어 실행
전체 경로로 실행
command 명령어 로 실행
\ 명령어로 실행

```bash
ls 명령어의 경로를 확인: which ls - /usr/bin/ls

ls 명령어 실행: ls /    (bin 뒤에 @이 붙지 않음)

ls 가 ls /F를 수행하도록 별명 설정: alias ls='ls -F'

ls 명령어 실행: ls /    (bin 뒤에 @이 붙음)
```

원래 명령어 수행
/usr/bin/ls /
command ls /
\ls /

- date: 현재 시각 과 날짜를 출력

- 설치하고 시스템 설정을 확인할 때 사용

- timedatectl: 모든 시간 관련 설정을 전부 출력

- 시스템 사용자 정보 확인

- logname: 사용 중 인 로그인 네임 확인
- users: 접속한 모든 사용자의 아이디
- who: 로그인 한 모든 사용자 계정 과 접속 시간, 접속 도구 , 접속 위치가 같이 출력됩니다.
- whoami: 현재 Ubuntu 사용자 확인

- 시스템 정보 확인

- uname [옵션]: 시스템 정보 확인
옵션
a: 모든 정보 확인
m: 하드웨어 정보 확인
n: 호스트 네임 확인
r: 운영체제 릴리즈 번호
s: 운영체제 이름
v: 버전 출시 일자

- hostname

- arch: CPU 정보

- env: 환경변수

- sudo 와 su

- 명령어 앞에 sudo를 붙여서 실행하는 것은 관리자의 권한을 빌려서 명령어를 실행하는 것으로 처음 실행할 때는 비밀번호를 입력해야 합니다.
여기서 사용하는 비밀번호는 현재 사용자의 비밀번호

- su 계정: 현재 계정의 환경 변수들을 유지하면서 다른 계정으로 전환
전환하고자 하는 계정의 비밀번호를 입력해야 합니다.

- su - 계정: 현재 계정의 환경 변수를 유지하지 않고 새로운 계정의 환경 변수를 가지고 다른 계정으로 전환

- su 명령 다음에 계정을 입력하지 않으면 root 계정으로 전환됨

### 10) ubuntu server에 GUI 설치

- sudo apt update
- sudo apt install ubuntu-desktop
