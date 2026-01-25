# 네트워크 관리

### 1) 네트워크 기본

- TCP/IP Protocol

- Protocol: 컴퓨터 와 컴퓨터가 데이터를 주고받기 위한 통신 규칙 또는 규약
- TCP/IP: 인터넷에서의 프로토콜
- 구조

| 계층 | 기능 | 프로토콜 | 장비 | 전송단위 |
| --- | --- | --- | --- | --- |
| 응용 계층 | 서비스 제공 응용 프로그램 | DNS, FTP, SSH, HTTP | Gateway | Message |
| 전송 계층 | 응용 프로그램으로 데이터 전달, 흐름 제어 | TCP, UDP | - | Segment |
| 네트워크 계층 | 주소 관리, 경로 탐색(Routing) | IP, ICMP | Router | Packet |
| 링크 계층 | 네트워크 장치 드라이버 | ARP | Switch | Frame |
| 물리 계층 | 전송 매체 | 케이블, 무선 | HUB | Bit |

- **주소**:

- MAC Address
Media Access Control의 약자로 하드웨어를 위한 주소로 NIC를 위한 주소인데 이더넷 주소, 하드웨어 주소, 물리 주소라고도 합니다.

네트워크 인터페이스 카드가 만들어질 때 부여되며 원칙적으로는 수정할 수 없지만 일부 네트워크 인터페이스 카드의 경우 사용자가 MAC 주소를 수정할 수 있도록 허용함

각 하드웨어를 구분하는 역할

주소는 :으로 구분하고 16진수 12자리인데 두 자리씩 묶어서 표현

앞 부분 24bit는 제조회사 번호이고 뒤의 24bit는 일련번호

```text
00:50:56:3e:3c:fe
```
00:50:56 는 제조회사 번호

동일 네트워크 내에서 통신할 때 MAC Address를 사용

- IP 주소
3계층 주소

일반적으로 컴퓨터가 인터넷에 연결될려면 IP(Internet Protocol) Address 가 있어야 함

인터넷으로 연결된 네트워크에서 각 컴퓨터를 구분하기 위해 사용

IPv4는 32비트 주소 체계로 8비트씩 나누어서 십진수로 표현하고 각 자리는 .으로 구분
192. 168.0.23형태로 작성하는데 여기는 다시 네트워크 부분 과 호스트 부분으로 나누어서 해석합니다.

IPv4의 주소 고갈 문제로 인해서 128비트 주소 체계인 IPv6가 등장했는데 이 주소는 16비트씩 끊어서 16진수로 표현하고 ::로 구분

- Netmask
IP 주소에서 네트워크 부분을 알려주는 역할을 하는 것이 Netmask

Netmask는 IP 주소와 동일한 방법으로 표현하는데 맨 앞에서부터 1이 나올 수가 있고 한 번 0이 나오면 다시는 1이 나올 수 없습니다.
Netmask의 1인 부분까지 동일하면 동일하 네트워크로 간주

1인 부분의 bit 수를 기재하는 방법도 존재

192. 168.0.7    255.255.255.0

192. 168.0.7/24

- Broadcast Address
자신의 네트워크 대역에서 가장 마지막 IP 주소로 이 주소에 데이터를 전송하면 네트워크에 속한 모든 컴퓨터에게 데이터가 전송됩니다.
192. 168.0.101/24 인 경우 Broadcast Address는 192.168.0.255

- Network Address
자신의 네트워크 대역에서 첫번째 IP 주소로 이 주소는 네트워크를 찾아가기 위한 주소입니다.

- 호스트 이름
컴퓨터가 인터넷에 연결될려면 IP 주소가 있어야 하는데 이 주소는 숫자로 되어 있어서 기억하기가 어려움
문자로 된 주소를 고안했는데 이것이 호스트이름 또는 도메인 입니다.
호스트이름도 두분으로 구성되는데 하나는 호스트이고 다른 하나는 도메인 입니다.
www.naver.com에서 보면 naver.com이 도메인이고 www가 호스트 입니다.

- 포트 번호
네트워크 서비스를 이용할 때 사용자의 패킷은 IP 주소를 보고 해당 서버 컴퓨터를 찾아가는데 서버 컴퓨터에 도착한 사용자의 패킷은 어떤 서비스를 요청했는지 확인한 다음 해당 데몬에 패킷을 전달

웹 서비스를 요청했으면 httpd 데몬에 전달

사용자가 어떤 서비스를 요청했는지 구분해주는 것이 포트번호

사용 중인 포트번호 확인: cat /etc/services | grep ftp  

/etc/services 파일에 저장된 포트 번호는 국제 표준으로 합의하여 사용하고 있는 것
사용자가 개발한 네트워크 프로그램은 이 파일에 정의되지 않은 번호를 사용해서 서비스를 제공하는 것을 권장

- 인터넷에 연결할려면 설정해야 할 주소

IP Address
Subnet Mask
Broadcast Address
Gateway Address
DNS Address

### 2) 네트워크 관리자

- **개요**:

- 우분투에서는 NetworkManager가 네트워킹 서비스를 제공
- 네트워크 관리자는 네트워크의 제어와 설정을 관리하는 데몬
- 네트워크 관리자를 이용해서 IP 주소 설정, 고정 라우터 설정, DNS 설정 등을 수행할 수 있음
- 유닉스 와 리눅스가 예전부터 제공하던 ifcfg 형식의 설정 파일도 지원
- 네트워크 관리와 관련된 도구
네트워크 관리자: 기본 네트워킹 데몬

```bash
nmcli 명령: 네트워크 관리자를 사용하는 명령 기반 도구
ip 명령: 네트워크를 설정하는 명령을 제공
```

GUI에서는 [설정] - [네트워크]
GUI에서는  nm-connection-editor

- 설치 및 활성화

- 설치

```bash
sudo apt install network-manager
```

- 확인
```bash
systemctl status NetworkManager
```

- 부팅하자 마자 활성화
```bash
systemctl enable NetworkManager
```

- nmcli 명령

- 기본 형식

```bash
nmcli [옵션] 명령 [서브 명령]
```

- 옵션
t: 실행 결과를 간단하게 출력
p: 사용자가 읽기 출력
v: 버전 출력
h: 도움말

- 서브 명령
general {status | hostname}: 네트워크 관리자의 전체적인 상태를 출력하고 호스트 이름을 읽거나 변경할 수 있음

networking {on | off | connectivity}: 네트워크를 시작 또는 종료하고 연결 상태를 출력

connection {show | up | down | modify | add | delete | reload | load}: 네트워크를 설정

device {status | show}: 네트워크 장치 상태를 출력

- 네트워크의 전체 상태를 확인: nmcli general
CONNECTIVITY 값
    none: 네트워크에 연결되지 않음
    limited: 호스트가 네트워크에 연결되어 있지만 인터넷에 연결이 안된 것
    full: 네트워크에 연결되어 있고 인터넷도 사용 가능
    unknown: 알 수 없음

- 활성화 또는 비활성화
```bash
nmcli net con #상태 확인

nmcli net off #비활성화

nmcli net on #활성화
```

- 디바이스(NIC) 상태 확인
#전체 상태 확인
```bash
nmcli dev status
```

#특정 장치 상태 확인
```bash
nmcli dev show enp0s3
```
