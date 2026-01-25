# Hosts and DNS Resolution

- 운영하는 서버세엇 hosts 사용을 피해야 하는 이유

- DNS 변경이 반영되지 않을 수 있음
상황
    DNS에서 서버 IP
    일부 서버만 계속 예전 IP로 접속
원인
    /etc/hosts
    10.0.0.5 api.company.com
    DNS는 바뀌었지만 hosts가 우선
    서버마다 서로 다른 목적지로 접속
결과
    서버 간 통신 장애
    특정 서버에서만 장애 발생

- 서버마다 설정이 달라짐(일관성 붕괴)
    서버 A에는 hosts가 존재하고 서버 B에는 hosts 가 없는 경우

- 장애 발생 시 원인 추적이 극도로 어려움
    SSH/API/DB 의 간헐적 실패
    로그 상 네트워크 문제처럼 보임

- 서버 교체 또는 오토스케일링 과 충돌
클라우드 환경
    Auto Scaling
    Immutable Infrastructure
    Blue/Green 배포
문제
    새 서버에는 hosts가 없음
    기존 서버만 정상 동작
    배포 직후 일부 서버만 장애

- 보안 사고 위험
내부자 공격이나 침해시 피해 증폭

- 운영 자동화 도구와도 충돌
충돌 대상
    Ansible
    Terraform
    Kubernetes
    CI/CD 파이프라인
문제
    인프라 도구는 DNS 기준으로 설정함
    hosts는 로컬 수동 설정

- 허용하는 경우

로컬 개발 PC
개인 테스트 서버
단기 장애 우회

- IP 주소를 찾는 과정

URL을 입력 -> /etc/host.conf 조회(hosts 파일의 위치를 조회) -> /etc/hosts를 조회

/etc/hosts에 있는 경우 -> IP 주소 획득

/etc/hosts에 없는 경우 -> /etc/resolv.conf를 조회해서 DNS Server 설정을 확인

DNS Server 설정이 없는 경우 -> 호스트 이름 알 수 없음

DNS Server 설정이 있는 경우 -> DNS 서버에게 질의를 해서 응답을 받아서 IP 주소 획득

### 5) 네트워크 상태 확인

- **ping**:

- 외부와 통신되는지 확인하거나 외부 서버가 동작하는지 확인할 때 사용

- 명령어 형식

```bash
ping [옵션] [목적지 주소]
```

- 옵션
a: 통신이 되면 소리가 남
q: 테스트 결과를 지속적으로 보여주지 않고 종합 결과만 출력
c 개수: 보낼 패킷 수를 지정

- 시스템에 따라서 보안을 강화하기 위해서 ping 패킷이 왔을 때 응답하지 않도록 설정하는 경우도 있기 때문에 ping으로 연결되지 않는다고 해서 무조건 해당 시스템이 동작하지 않는다고 단정할 수 없음

- 옵션없이 사용하면 Linux에서는 계속해서 패킷을 보내고 윈도우즈에서 4개만 보냄
```bash
ping www.google.com
```

- 결과
64 bytes from nchkgb-aa-in-f4.1e100.net (142.250.71.196): icmp_seq=96 ttl=255 time=40.1 ms
#64 bytes from nchkgb-aa-in-f4.1e100.net (142.250.71.196) :패킷의 크기와 목적지 주소
#icmp_seq는 응답이 보낸 ICMP 패킷의 순서
#ttl은 Time To Live의 약자로 패킷이 목적지에 도달하기까지 거친 라우터의 수를 간접적으로 알려주는 것인데 이 값을 시작점에서 라우터를 거칠 때 마다 1씩 감소
#time 은 패킷의 왕복 시간

96 packets transmitted, 95 received, 1.04167% packet loss, time 95105ms
rtt min/avg/max/mdev = 39.034/44.390/104.528/9.667 ms

#96개의 패킷을 전송했고 95개의 응답을 받음
#time 95105ms: 전체 응답에 걸린 시간
rtt min/avg/max/mdev = 39.034/44.390/104.528/9.667 ms: 가장 빠른 것 과 느리것 그리고 평균과 표준편차

- **netstat**:

- 네트워크 연결 상태, 라우팅 테이블, 인터페이스 관련 통계 등을 출력하고 현재 시스템에 열려있는 포트가 무엇인지도 확인할 수 있는 명령

- 형식

```bash
netstat [옵션]
```

- 옵션
a: 모든 소켓 정보를 출력
r: 라우팅 정보를 출력
n: 호스트 이름 대신 IP 주소로 출력
i: 모든 네트워크 인터페이스 정보를 출력
s: 프로토콜 별로 네트워크 통계 정보를 출력
p: 해당 소켓과 관련된 프로세스 이름과 PID를 출력

- 열려있는 포트 확인
```bash
netstat -an | grep LISTEN
```

tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN
#TCP를 사용하고 두번째 0은 수신 대기 큐의 크기이고 세번째 0은 아직 처리되지 않은 패킷의 수 이고 로컬 컴퓨터의 주소 와 포트이고 뒤의 것은 원격 컴퓨터의 주소 와 포트이며 마지막은 현재 상태

unix  2      [ ACC ]     SEQPACKET  LISTENING     3344     /run/udev/control
#유닉스 소켓의 상태를 해석하는 것

- 열려있는 포트를 사용 중인 프로세스 확인: netstat -p

- 프로토콜 별로 사용 중인 포트 통계 정보: netstat -s  

- MAC Address 와 IP 주소 확인

- ARP(Address Resolution Protocol)
- 동일한 네트워크에 연결되 시스템들의 MAC Address 와 IP 주소를 확인하는 명령

```bash
arp [IP주소]
```

- 패킷 캡쳐 명령

- tcpdump
- 형식: tcp [옵션]
- 옵션
c 패킷수: 개수만큼 덤프받고 종료
i 인터페이스: 인터페이스를 지정
n: IP 주소를 호스트이름으로 변경하지 않음
q: 정보를 간단하게 출력
X: 패킷의 내용을 16진수로 출력
w 파일명: 파일에 기록
r 파일명: 파일에서 읽어옴
host 호스트이름: 지정한 호스트가 보낸 패킷만 덤프
port 포트번호: 지정한 포트 번호 패킷만 덤프

```bash
ip: IP 패킷만 덤프
```

### 6) Virtual Box에서 하나의 네트워크에 묶인 컴퓨터 만들기

- NAT 네트워크 생성

- Virtual Box에서 네트워크 메뉴를 선택하고 오른쪽 탭에서 NAT 네트워크를 선택하고 만들기 클릭
- 만들어진 네트워크를 선택하고 일반 옵션을 선택
- 이름을 수정하고 네트워크의 IP 대역을 설정(10.0.2.0/24)하고 DHCP 활성화 해제(해제하지 않으면 IP 수정 못함)

- 필요한 만큼 가상머신을 생성

- 네트워크에 편입

- 가상머신을 선택하고 [설정] 메뉴를 누르고 네트워크로 이동

- Attach to를 NAT 네트워크로 변경하고 아래에서 이름 선택

- 컴퓨터 하나의 인터페이스 확인 및 IP 설정

- 부팅을 수행

- sudo nano /etc/netplan/00-installer-config.yaml

```bash
network:
```

  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses: [10.0.2.101/24]
      gateway4: 10.0.2.1
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]

- 적용: sudo netplan apply

- 확인: ip address show

- 호스트 이름 변경

- 변경

```bash
sudo hostnamectl set-hostname master
```

- 확인
```bash
cat /etc/hostname
```

- 호스트 이름을 /etc/hosts에 설정

- sudo nano /etc/hosts

10. 0.2.101 master

10. 0.2.102 worker1

### 7) 실습

- 3개의 Linux 가상 머신을 만들고 하나의 네트워크로 묶기

- ping 명령으로 컴퓨터들이 통신이 되는지 확인
- hostname으로도 ping이 가능한 지 확인
