# 네트워크 관리

### 1) 네트워크 관리자

- ip 명령

- ip 명령으로 네트워크 설정이 가능하지만 재부팅하면 내용이 사라지기 때문에 내용을 보존하기 위해서는 설정 파일에 저장해야 합니다.

- 형식: ip [옵션] 객체 [서브 명령]

- 객체는 디바이스

- 옵션
V: 버전을 출력
s: 자세한 정보를 출력

- 서브 명령
address [add | del | show | help]: ip 주소 관리

route [add | del | help]: 라우팅 테이블 관리

link [set]: 네트워크 인터페이스 카드 활성화 비활성화

- 정보 조회
#전체 조회

```bash
ip address show
```

#특정 디바이스 확인
```bash
ip address show enp0s3
```

- ip 추가
```bash
sudo ip address add IP/서브넷마스크 dev 인터페이스

sudo ip address add 192.168.1.20/24 dev enp0s3
```

- ip 삭제
```bash
sudo ip address del 192.168.1.20/24 dev enp0s3
```

- 라우팅 테이블 과 게이트웨이 주소 관리
ip route 명령은 라우팅 테이블을 출력하거나 게이트웨이를 설정하고 삭제

- gateway
  - 네트워크를 다른 네트워크와 연결할 때 연결점이 되는 장치
  - 게이트웨이도 하나의 컴퓨터로 볼 수 있는데 라우터라고도 부름
  - 게이트웨이는 패킷을 보고 동일한 네트워크로 보내는 것이 아니면 외부로 전달
  - 게이트웨이가 없으면 동일한 네트워크가 아닌 컴퓨터와는 접속할 수 없음

- 라우팅 테이블 확인
```bash
ip route show
```

default는 기본 게이트웨이
10. 0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100 는 서브넷 설정 과 커널이 자동 설정한 것

- 게이트웨이 설정
기본 게이트웨이: sudo ip route add default via 게이트웨이주소 dev 디바이스
```bash
sudo ip route add default via 10.0.2.1 dev enp0s3
```

- 라우팅 경로 삭제
```bash
sudo ip route del 네트워크주소
```

- 네트워크 인터페이스 관리 명령
네트워크 인터페이스 비활성화: sudo ip link set 디바이스 down
네트워크 인터페이스 활성화: sudo ip link set 디바이스 up

- **ifconfig**:

- net-tools 라는 패키지를 설치해야 사용 가능
- 형식

```bash
ifconfig [인터페이스][옵션][값]
```

- 옵션
a: 모든 인터페이스에 대한 정보를 확인
up/down: 인터페이스를 활성화하거나 비활성화
netmask 주소: 넷마스크 주소를 설정
broadcast 주소: 브로드캐스트 주소를 설정

- 모든 인터페이스 확인

```bash
ifconfig
```

```text
enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255            :IP, netmask, broadcast
        inet6 fe80::a00:27ff:fec3:ca76  prefixlen 64  scopeid 0x20<link>            : IPv6 주소
        inet6 fd17:625c:f037:2:a00:27ff:fec3:ca76  prefixlen 64  scopeid 0x0<global>
        ether 08:00:27:c3:ca:76  txqueuelen 1000  (Ethernet)                : MAC Address
        RX packets 1752  bytes 663942 (663.9 KB)                    : 부팅 후 받은 패킷 수 와 바이트수
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 914  bytes 107116 (107.1 KB)                    : 부팅 후 보낸 패킷 수 와 바이트 수
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

- 인터페이스 설정
```bash
sudo ifconfig 디바이스 ip주소 netmask 서브넷마스크 broadcast 브로드캐스트주소
```

192. 168.0.101 /24로 설정

```bash
sudo ifconfig enp0s3 192.168.0.101 netmask 255.255.255.0 broadcast 192.168.0.255
```

- 게이트웨이 설정

- 기본 형식

```bash
route [명령]
```

- 명령
add: 라우팅 경로나 기본 게이트웨이 추가
del: 라우팅 경로나 기본 게이트웨이 제거

- 라우팅 테이블 보기: route
Kernel IP routing table
Destination     Gateway         Genmask         
목적지           게이트웨이    서브넷마스크(255.255.255.255 이면 호스트이고 0.0.0.0 이면 default 경로)            

Metric                     Ref                            
대상까지의 거리(리눅스에서는 사용하지 않음)    해당 경로에 대한 참조 수로 리눅스에서는 사용하지 않음

Use         Iface
사용 여부        인터페이스

- 기본 게이트웨이 설정
```bash
sudo route add default gw 게이트웨이주소 dev 인터페이스이름
```

- DNS 설정

- DNS는 domain name service의 약자로 호스트 이름을 IP 주소로 변경하는 역할
- DNS가 설정되어 있지 않으면 이름으로 다른 컴퓨터에 접속할 수 없습니다.
- 우분투는 /etc/resolv.conf 파일에 저장
기본적으로 설정된 127.0.0.53은 실제 DNS 주소가 아님
- DNS 정보 확인: resolvectl status로 확인
- DNS 설정

```bash
nmcli con mod 인터페이스이름 ipv4.dns DNS주소
```

- DNS 질의 명령: nslookup

### 2) netplan

- **개요**:

- 우분투에서 네트워크 구성을 관리하는 도구 중 하나
- 네트워크 구성을 설정 파일을 이용해서 작업
- /etc/netplan 디렉토리에 YAML 파일을 만들어주면 됩니다.
파일 이름은 아무거나 상관없습니다.

- **yaml**:

- 데이터 직렬화 양식(데이터를 텍스트로 표현하기 위한 방법)
- 사람이 읽기 쉽고 쓰기 매우 편하게 설계된 방식
- 설정 파일, 데이터 저장, 시스템 간 데이터 교환(쿠버네티스, 도커, 스프링 부트 등)에 널리 사용
- JSON이나 XML 보다 문장 구조가 단순
- 공백을 이용해서 계층 구조를 정의(들여쓰기를 이용)
- 주석 지원(#으로 시작 - JSON은 주석을 허용하지 않음)
- 상위 호환성(모든 JSON 파일은 유효한 yaml 파일)
- 확장자는 일반적으로 yaml 이나 yml을 사용

- **dhcp**:

- 동적 호스트 설정
- IP pool을 가지고 있다가 클라이언트가 접속할 때 IP를 할당해주는 서비스
- 직접 IP를 설정하면 관리자가 계속해서 IP가 중복되지 않도록 할당을 해야 하기 때문에 복잡함
- DHCP를 이용하면 부팅할 때 마다 IP가 변경될 수 있습니다.

- 인터페이스 설정

- sudo vim /etc/netplan/01-netcfg.yaml

- 작성

```bash
network:
```

  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - IP주소
      gateway4: 게이트웨이주소
      nameservers:
        addresses: [DNS 서버 주소 나열]

```bash
network:
```

  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.0.101/24
      gateway4: 192.168.0.1
      nameservers:
        addresses: [8.8.8.8]

- 파일의 권한 설정을 수정: sudo chmod 600 /etc/netplan/01-netcfg.yaml

- 적용: sudo netplan apply 또는 재접속

### 3) hostname 설정

- **uname**:

- hostname을 출력
- 형식: uname [옵션]
- 옵션
m: 아키텍쳐
n: 호스트 이름
r: 운영체제 릴리즈 정보
s: 운영체제의 이름
v: 운영체제의 버전
a: 모든 정보

- **hostname**:

- 시스템의 네트워크 이름을 확인하거나 일시적으로 변경하고자 할 때 사용
- hostname [옵션] [이름]
- 옵션
I: 모든 IP 주소 출력
f: 전체 도메인(FQDN)
- 이름 변경(현재 세션에서만 유효)

```bash
sudo hostname itstudy
```

- **hostnamectl**:

- 호스트 이름을 확인하고 설정하는 명령
- 형식: hostnamectl [옵션] [명령]
- 옵션
h: 도움말
version: 버전 출력
- 명령
status: 현재 호스트 이름 과 관련 정보 출력
set-hostname 이름: 호스트 이름 설정

### 4) /etc/hosts 파일

- 도메인 이름을 IP 주소로 매핑하는 로컬 DNS 파일

- **역할**:

- 도메인을 IP로 변환해서 로컬에서 강제로 지정
- 127.0.0.1 localhost 로 설정되어 있으면 localhost를 접속하고자 하는 명령을 수행하면 127.0.0.1로 변환해서 수행합니다.
- DNS를 조회하지 않음

- 윈도우즈에서는 c:\windows\system32\drivers\etc\hosts

- 작성 시 주의사항

- 0.0.0.0 접속 대상 IP가 아니기 때문에 연결
- 특정 사이트 차단
0. 0.0.0 도메인

- hosts 파일을 수정 후 체크사항

- DNS 캐시를 삭제
Linux: sudo systemctl restart systemd-resolved

Windows: ipconfig /flushdns

Mac OS

```bash
    sudo dscacheutil -flushcache
    sudo killall -HUP mDNSResponder
```

- 실제 해석 확인

```bash
ping 도메인이름
```

getent hosts 도메인명

- hosts 와 DNS 우선 순위

1. hosts 파일
2. DNS 캐시
3. DNS 서버
