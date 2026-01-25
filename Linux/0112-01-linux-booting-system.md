# Linux Booting System

### 1) systemd service

- **개요**:

- 리눅스 시스템의 서비스 관리자로서 유닉스의 init 프로세스가 하던 작업을 대신 수행
- 다양한 서비스 데몬을 시작하고 프로세스 들의 상태를 유지하고 시스템의 상태를 관리
-init 과 관련된 스크립트 파일은 /etc/init.d 디렉토리에 존재: 아직 남아있음
-init 스크립트 방식과 systemd 서비스가 공존

- systemd 가 init 에 비해서 좋은 점

- 소켓 기반으로동작하여 inetd 와 호환성을 유지
- 쉘과 독립적으로 부팅이 가능
- 마운트 제어가 가능
- fsck(File System ChecK) 제어 가능
- 시스템 상태에 대한 스냅샷을 유지
- 서비스에 시그널 전송 가능
- 셧 다운 전에 사용자 세션의 안전한 종료가 가능

- systemd unit

- 유닛 이라는 구성 요소를 사용
- 관리 대상을 서비스이름.유닛종류 형태로 관리
- 종류
service: 시스템 서비스 유닛으로 데몬을 시작, 종료, 재시작 및 로드

target: 유닛을 그룹화
automount: 디렉토리 계층 구조에서 자동 마운트 포인트를 관리
device: 리눅스 장치 트리에 있는 장치를 관리

```bash
mount: 디렉토리 계층 구조에서 마운트 포인트를 관리
```

path: 파일 시스템의 파일이나 디렉토리 경로를 관리
scope: 외부에서 생성된 프로세스를 관리
slice: 시스템의 프로세스를 계층적으로 관리
socket: 소켓을 관리하는 유닛
swap: 스왑 장치 관리
timer: 타이머 와 관련된 기능을 관리

- systemctl 명령

- 형식: systemctl [옵션] [명령] [유닛이름]

- 옵션
a: 상태와 관계없이 유닛 전체를 출력
t 유닛종류: 유닛 종류만 출력

- 명령
start: 유닛 시작 - systemctl start mariadb
stop: 유닛 중지
reload: 유닛의 설정 파일을 다시 읽어옵니다.
restart: 재시작
status: 유닛 상태를 확인
enable: 부팅 시 시작하는 유닛으로 등록
disable: 부팅 시 시작하지 않도록 등록
is-active: 유닛이 동작하고 있는지 확인
is-enabled: 유닛이 시작했는지 확인
isolate: 이 유닛만 시작하고 나머지는 정지
kill: 유닛에 시그널을 전송

- 서비스 등록

- ubuntu에서 서비스를 등록하는 것은 systemd 서비스를 이용하는 것이 일반적
- /etc/systemd/system에 .service 파일을 생성하고 systemctl daemon-reload 그리고 systemctl enable 명령어를 수행해서 서비스를 등록하고 활성화 시킴 
- 파일의 구성

```ini
[Unit]
Description=My custom service
After=network.target

[Service]
ExecStart=/path/to/your/script/myservice.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

[Unit]: 서비스의 메타 데이터와의 의존성을 정의하는데 After=network.target 은 서비스가 시작되기 전에 네트워크 서비스가 먼저 실행되어야 함
[Service]: 실행할 스크립트 파일의 경로 와 실패했을 때 재시작하는 정책을 설정
[Install]: 서비스가 부팅 시 자동으로 시작하도록 하는 분을 정의하는데 WantedBy에 런레벨 설정 가능

- 서비스 등록 및 활성화
서비스 등록

```bash
systemctl daemon-reload # 새로운 서비스 파일을 읽어서 서비스 데몬을 다시 시작
```

서비스 관리 명령을 사용해서 활성화하거나 비활성화

### 2) 종료

- **개요**:

- 리눅스는 대부분 서버 운영체제로 사용되기 때문에 비정상적으로 시스템을 종료해서 문제가 발생하면 서비스를 제공하지 못할 수 있음

- 종료 방법

- shutdown 명령
- halt 명령
- poweroff 명령
- Runlevel을 0이나 6으로 전환
- reboot 명령
- 전원 차단

- **shutdown**:

- 형식: shutdown [옵션] [시간][메시지]
- 옵션
k: 사용자들에게 메시지만 전달
r: 재시작
h: 종료하고 halt 상태로 이동
f: 빠른 재시작  fsck를 수행하지 않음
c: shutdown 명령 취소
- 시간
(hh:mm, now, +분)

- now를 사용하면 즉시 종료
- k 나 h 옵션을 사용하면 메시지만 전송하고 종료되지 않음

- Run Level을 변경해서 종료하는 것도 가능

```bash
sudo init 0 또는 sudo init 6
```

- 종료
```bash
sudo systemctl isolate poweroff.target
sudo systemctl isolate runlevel0.target
```

- 재시작
```bash
sudo systemctl isolate reboot.target
sudo systemctl isolate runlevel6.target
```

- 종료의 다른 명령: systemctl 명령의 심볼릭 링크

- halt: /sbin/halt
- poweroff: /sbin/poweroff
- reboot: /sbin/reboot

### 3) Daemon Process

- **개요**:

- 리눅스의 백그라운드에서 동작하며 특정한 서비스를 제공하는 프로세스
- 서버로 사용되는 프로세스들이 대부분 데몬
- 데몬 혼자서 스스로 동작하는 독자형 과 데몬을 관리하는 슈퍼 데몬에 의해 동작하는 방식이 있음
- 독자형의 경우 시스템의 백그라운드에서 항상 동작하고 있는데 자주 호출되는 데몬이 아니라면 시스템의 자원을 낭비할 우려가 있음
- 슈퍼 데몬에 의한 동작 방식은 평소에는 슈퍼 데몬만 동작하다가 서비스 요청이 오면 슈퍼 데몬이 해당 데몬을 동작시키는 것으로 독자형보다는 서비스에 응답하는데 시간이 좀 더 걸릴 수 있지만 자원을 효율적으로 사용하는 것이 가능

- 슈퍼 데몬

- 데몬의 종류가 늘어나자 이를 관리하기 위해서 등장
- 유닉스의 슈퍼 데몬이 inetd 였는데 우분투에서는 보안 기능이 포함된 xinetd를 사용
- 슈퍼 데몬은 네트워크 서비스를 제공하는 데몬만 관리
- 사용자가 네트워크 서비스를 요청하면 슈퍼 데몬이 이를 받아서 해당하는 서비스 데몬을 동작시키는 것

- systemd 데몬

- init을 대체한 데몬
- 대다수 프로세스의 조상 프로세스
- pstree 명령으로 확인 가능

- kernel thread 데몬

- 커널 일부분을 프로세스 처럼 관리하는 데몬
- ps 명령으로 확인할 때 [ ] 에 들어있는 프로세스들
- 커널 데몬은 대부분 입출력이나 메모리 관리 그리고 디스크 동기화등을 수행하고 낮은 번호의 PID를 가짐

- 주요 데몬

- atd: 특정 시간에 실행되도록 예약한 명령을 실행하도록 해주는 데몬
- cron: 주기적으로 실행되도록 예약한 명령을 실행하도록 해주는 데몬
- dhcpd: 동적 IP 주소 할당
- httpd: 웹 서버
- nfs: 네트워크 파일 시스템 서비스 제공
- named: DNS
- sendmail: 메일 전송 데몬
- smtpd: 메일 전송 데몬
- popd: 기본 메일 서비스
- routed: 자동 IP 라우터 테이블 제공
- smb: 삼바 서비스를 제공
- syslogd: 로그 기록 서비스를 제공
- sshd: 원격 보안 서비스를 제공
- ftpd: 파일 송수신 서비스
- ntpd: 시간 동기화 서비스

### 4) Boot Loader

- **개요**:

- 커널을 메모리에 로딩하는 역할을 수행
- 리눅스에서는 LILO 와 GRUB 라는 두 가지 부트 로더가 있는데 우분투에서는 GRUB를 사용
- GRUB(GRand Unified Bootloader)의 약자로 리눅스의 전통적인 부트 로더인 LILO의 단점을 보완한 것임
- GRUB가 LILO에 비해 가진 장점
LILO는 리눅스에서만 사용할 수 있지만 GRUB는 윈도우에서도 사용할 수 있음
LILO에 비해 설정과 사용이 편리
부팅할 때 명령을 사용하여 수정할 수 있음
멀티 부팅 기능을 제공
- 최신 버전은 GRUB2 입니다.

- 관련 디렉토리 와 파일

- /boot/grub/grub.cfg
직접 수정 불가
이 파일이 /etc/defaul/grub 파일 과 /etc/grub.d 디렉토리 아래에 있는 스크립트를 읽어서 생성
파일의 내용을 수정하고자 하는 경우는 위의 파일 과 디렉토리 안에 있는 파일을 수정

- /etc/grub.d 디렉토리
GRUB 관련 스크립트를 가지고 있음

- /etc/defaul/grub 파일
GRUB 메뉴 설정 내용이 저장되어 있음

- 루트 계정의 암호를 잃어버린 경우

- GRUB 메뉴 초기 화면을 출력
- 메뉴 초기 화면에서 E를 눌러서 편집 모드로 전환한 후 커서를 아래로 내려서 ro splach vt_handoff 부분을 rw init=/bin/bash로 수정
- 재부팅하면 비밀번호 없이 root 계정으로 로그인

### 5) Namespace & Cgroup

- Container 기술

- 애플리케이션을 효율적이고 독립적으로 실행할 수 있는 경량화된 환경을 제공
- 컨테이너 기술의 근간이 되는 리눅스 기술
Control Group(Cgroup)
Namespace
Union Mount Filesystem

- Control Group

- 프로세스들이 사용하는 시스템 자원의 사용 정보를 수집 및 제한시키는 커널의 기늘
- 사용 가능한 서브 시스템
CPU
Memory
Freezer: cgroup의 작업을 일시 중지하거나 다시 시작
blkio: 블록 장치에 대한 I/O를 제한
net_cls: 트래픽 컨트롤러가 특정 cgroup 작업에서 발생하는 패킷을 식별하게 하는 네트워크 패킷 태그를 지정
cpuset: 개별 CPU 메모리 노드를 cgroup에 할당
cpuacct: CPU 자원 보고서 생성
devices: cgroup의 작업 단위
ns: namespace 서브 시스템

- **Namespace**:

- 프로세스를 격리하기 위해 사용하는 커널의 기능
- Docker 와 같은 컨테이너 기술의 핵심 기반

