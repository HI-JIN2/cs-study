# 기본 개념

### 1) Protocol

- **개요**:

- 규정이나 규약과 관련된 내용
- 프로토콜은 어떤 표준 협회나 워킹 그룹이 만들었는지 또는 어떤 회사에서 사용하느냐에 따라 특징이 많이 달라지고 다양한 프로토콜이 존재
- 최근에는 복잡하고 산재되어 있던 여러가지 프로토콜 기술이 이더넷-TCP/IP 기반 프로토콜들로 변경되고 있음
  물리적 측면: 데이터 전송 매체, 신호 규약, 회선 규격 등으로 이더넷이 널리 쓰임
  논리적 측면: 장치들 끼리 통신하기 위한 프로토콜 규격으로 TCP/IP가 널리 쓰임
- 적은 컴퓨팅 자원 과 느린 네트워크 속도를 이용해서 최대한 효율적으로 통신하는 것이 목표이다 보니 대부분의 프로토콜이 문자 기반이 아니라 2진수 bit 기반
- bit 기반은 몇 번째 전기 신호는 보내는 사람 주소 몇 번째 전기 신호는 받는 사람 주소 몇 번째 전기 신호는 상위 프로토콜 지시자 등 과 같이 까다롭게 약속하고 그 약속을 철저히 지켜야 통신을 할 수 있는데 애플리케이션 레벨(사용자)에서는 비트 기반이 아닌 문자 기반 프로토콜을 사용(HTTP, SMTP 등): 비트로 메시지를 전달하지 않고 문자 자체를 이용해 헤더 와 헤더 값 데이터를 표현하고 전송
- 문자 기반 프로토콜은 효율성은 비트 기반 프로토콜보다 떨어지지만 다양한 확장이 가능

### 2) OSI 7계층 과 TCP/IP

- TCP/IP 프로토콜 스택

- TCP/IP는 프로토콜이라 부르지 않고 프로토콜 스택이라고 부는데 TCP 와 IP는 별도 계층에서 동작하는 프로토콜인데 함께 사용하기 때문에 묶어서 표현하는 경우가 많은데 이런 경우 프로토콜 스택이라고 합니다.
- TCP/IP 프로토콜 스택에는 TCP, IP, UDP, ICMP, ARP, HTTP, SMTP, FTP 와 같은 매우 다양한 애플리케이션 레이어 프로토콜이 존재
- TCP/IP 프로토콜은 4개 계층으로 구분
Application - FTP, SSH, TELNET, DNS, SNMP
Transport - TCP, UDP
Network - IP, ICMP, ARP
DataLink 와  Phsical - Ethernet

- OSI 7계층

- 과거에는 통신용 규약이 표준화되지 않고 각 벤더에서 별도로 개발했기 때문에 호환되지 않는 시스템이나 애플리케이션이 많았고 통신이 불가능했는데 이를 하나의 규약으로 통합하려는 노력이 OSI 7계층
- 네트워크의 동작으로 나누어 이해하고 개발하는데 많은 도움을 주어서 주요 레퍼런스 모델로 활용하고 있지만 최근에는 대부분의 프로토콜이 TCP/IP 프로토콜 스택을 기반으로 되어 있음

| OSI 계층 | Data Unit |
| --- | --- |
| Application | Data |
| Presentation | Data |
| Session | Data |
| Transport | Segments |
| Network | Packet |
| DataLink | Frames |
| Physical | Bits |

- 1~4계층을 하위 계층이라 하고 5~7계층을 상위 계층이라고 하는데 Network Engineer 입장에서는 하위 계층이 중요하고 Application Developer 입장에서는 상위 계층이 중요

- **TCP/IP**:

| OSI 계층 | TCP/IP 계층 |
| --- | --- |
| Application | Application |
| Presentation | Application |
| Session | Application |
| Transport | Transport |
| Network | Internet |
| DataLink | Network Access |
| Physical | Network Access |

- OSI 7계층 별 이해

- 1계층(Physical - 물리)
물리적 연결과 관련된 정보를 정의

전기 신호를 전달하는데 초점을 맞춤

주요 장비로는 Hub(여러 대의 장비를 연결), Repeater(증폭기), Cable, Connector, Transiber, TAP(네트워크 모니터링이나 패킷 분석을 하기 위해서 전기 신호를 다른 장비로 복제해주는 장비) 등이 있습니다.

허브는 주소의 개념이 없으므로 전기 신호가 들어온 포트를 제외하고 모든 포트에 같은 전기 신호를 전송

- 2계층(Data Link)
전기 신호를 모아서 우리가 알아볼 수 있는 데이터 형태로 처리

정확한 주소로 통신이 되도록 하는데 초점이 맞춰져 있음

출발지와 목적지 주소를 확인

에러 탐지 와 수정 기능(최근에는 수행하지 않음)도 있음

장비로는 NIC(네트워크 인터페이스 카드) 와 Switch

MAC Address를 사용

- 3계층(Network - Internet)
논리적인 주소를 사용

IP 주소를 사용(네트워크 주소 와 호스트 주소로 구분)

경로 설정(Routing)이 가장 중요한 역할

- 4계층(Transport - 전송)
해당 데이터들이 정상적으로 잘 보내지도록 확인하는 역할을 수행

컴퓨터 안에서 애플리케이션(프로세스)을 구분

패킷의 번호를 인식해서 에러 여부 만 메시지 조립 여부를 결정

애플리케이션 구분자(포트번호) 와 시퀀스(패킷의 번호), ACK 번호(긍정 응답)를 이용해서 부하를 분산하거나 보안 정책을 수립해 패킷을 통과시키거나 차단하는 기능을 수행

해당하는 장비가 Load Balancer, Firewall 등이 있음

- 5계층(Session)
양 끝 단의 응용 프로세스가 연결을 성립하도록 도와주고 연결이 안정적으로 유지되도록 관리하고 작업 완료 후에는 이 연결을 끊는 역할을 수행

에러 복구 와 재전송도 담당

- 6계층(Presentation - 표현)
표현 방식이 다른 애플리케이션이나 시스템 간의 통신을 돕기 위해서 동일한 형식으로 변환시키는 역할을 수행

인코딩, 암호화, 압축 등

- 7계층(Application)
최상위 계층으로 애플리케이션 서비스를 수행

네트워크를 사용하는 소프트웨어의 UI 부분이나 사용자 입출력 부분을 정의하는 게층

HTTP 나 FTP 와 같은 실제 서비스가 구동되는 계층

### 3) Encasulation & Decapsulation

- **개요**:

- 상위 계층에서 하위 계층으로 데이터를 보내면 물리 계층에서 전기 신호로 네트워크를 통해 신호를 보냄
- 받는 쪽에서는 다시 하위 계층에서 상위 계층으로 데이터를 전송
- 하나의 컴퓨터에서 다른 컴퓨터로 데이터를 전송할 때 Application에서 Presentation 계층으로 내려보내서 압축 이나 암호화를 진행하고 Session 계층으로 내려보내서 연결을 유지하도록 합니다.
4계층에서 데이터에 4계층 헤더(포트)를 붙여서 Segment를 만들고 3계층 네트워크 계층으로 내려보내서 3계층 헤더(IP)를 붙여서 Packet을 만들고 2계층 데이터링크 계층으로 보내서 2계층 헤더(MAC Address)를 붙여서 Frame을 만들어서 1계층으로 내려보내서 전기 신호를 만들어서 전송하면 받는 쪽에서 역으로 하나씩 열어서 해석

- Known Port(IANA(국제 인터넷 주소 관리 기구)에서 주요 서비스용으로 미리 할당해 놓은 번호: 0~1023)

TCP 20, 21 - FTP(파일 전송 서비스)
TCP 22 - SSH(Secure Shell)
TCP 23 - Telnet
TCP 25 - SMTP(Simple Mail Transport - 이메일 전송)
UDP 49 - TACACS(Terminal Access Controller Access Control System)- 네트워크 장비에 접속하려는 사용자 인증 및 권한 부여
(Authentication - 인증, Authorization - 인가, Accounting - 계정)
TCP 54/UDP 53 - DNS
UDP 67, 68 - Bootstrap Protocol: 네트워크 장비가 부팅될 때 자신의 IP 주소와 필요한 설정 정보를 서버로부터 할당받기 위한 프로토콜
TCP 80/UDP 80 - HTTP
UDP 123- NTP(Network Time Protocol): 네트워크에 연결된 컴퓨터들의 시간을 표준 시간과 동기화해서 사용하기 위한 프로토콜
UDP 161, 162 - SNMP(Simple Network Management Protocol): 네트워크 관리 프로토콜
TCP 443 - HTTPS

- Registerd Ports(1024~49151)

특정 애플리케이션이나 기업이 등록해서 사용하는 포트
3306(MySQL, Maria DB)
5432(PostgreSQL)
6379(Redis DB)
27017(Mongo DB)
1521(Oracle)

- Dynamic/Private Ports(49152~65535)

클라이언트가 서버에 접속할 때 임시로 할당받는 포트

