# 통신을 위한 네트워크 주요 기술

### 1) NAT/PAT

- **개요**:

- NAT(Network Address Translation)
IP 주소를 다른 IP 주소로 변환해 라우팅을 원활히 해주는 기술
1:1이 기본

- PAT(Port Address Translation)
여러 개의 IP 주소를 하나의 IP 주소로 변환하는 기술
NAPT(Network Address Port Translation)인데 줄여서 PAT로 사용
NAT의 일종

- AFT(Address Family Translation)
IPv4 의 주소를 IPv6로 변환하거나 반대로 변환하는 기술
NAT의 일종

- 용도 와 필요성

- IPv4의 주소 고갈 문제를 해결
IPv4의 주소 고갈을 해결하는 방법
    클래스리스를 이용한 서브네팅(CIDR)
    NAT/PAT: 여러 개의 IP를 하나로 묶어서 사용하면 IP 개수를 줄여서 인터넷을 사용할 수 있습니다.
    IPv6

- 보안 강화
    외부와 통신할 때 내부 IP를 다른 IP로 변환해 통신하면 외부에 사내 IP 주소 체계를 숨길 수 있습니다.

- IP 주소 체계가 같은 두 개의 네트워크 간 통신을 가능하게 해줌
    두 개의 다른 네트워크가 동일한 Private IP를 이용해서 내부 네트워크가 구성된 경우 NAT를 이용해서 주소 변환을 한 번 더 수행하면 내부 네트워크의 주소를 수정하지 않아도 통신이 가능

- 불필요한 설정 변경을 줄일 수 있습니다.
    외부
에서 사용 가능한 Public IP로 서버를 설정한다면 ISP(인터넷 업체)를 변경하거나 서버를 이전한다면 서버의 설정을 변경해야 하는데 Private IP를 이용해서 설정하고 NAT/PAT를 이용해서 외부와 연결한다면 ISP 변경이나 이전을 했을 경우 외부 설정 변경만으로 적용이 가능 

- **단점**:

- 네트워크 운영자 입장에서는 IP가 변환되면 장애가 발생했을 때 문제 해결이 힘듬
- 애플리케이션 개발자 입장에서는 개발할 때 더 많은 고려사항이 생겼습니다.
- IPv6 전환은 IPv4 주소 부족 해결이라는 목표는 어느 정도 해결이 되서 NAT를 없애려는 움직임도 있었습니다.
NAT로 인해 주소가 변환되면서 단말 간 직접적인 연결성이 무너졌고 이로 인해 개발자들이 애플리케이션을 제작할 대 NAT 환경을 항상 고려해야 하는 상황이 벌어졌습니다.
NAT 밑에 있는 단말도 직접 연결하게 도와주는 홀 펀칭(Hole Punching) 기술이 나오기 이 기술을 이용하기 위해서 애플리케이션이 더 복잡해지는 악순환이 발생

- NAT 동작 순서

출발지 사용자는 10.10.10.10 이라는 Private IP를 소유하고 있는 상황에서 목적지 IP는 20.20.20.20 이고 포트는 80번 인 경우

- 출발지 서비스 포트는 임의로 할당이 되는데 2000번으로 가정

- NAT 역할을 수행하는 장비에서는 사용자가 보낸 패킷을 수신한 후 NAT 정책에 따라 외부 네트워크 와 통신이 가능한 Public IP 11.11.11.11로 IP 주소를 변환하고 이 때 변경 전후의 IP 주소는 NAT 테이블에 저장

- NAT 장비에서는 출발지 주소를 11.11.11.11로 변경해서 목적지 웹 서버로 전송

- 패킷을 수신한 웹 서버에서는 사용자에게 응답을 보내는데 응답이므로 수신과 반대로 출발지는 웹 서버 20.20.20.20 이 되고 목적지는 NAT 장비에의해 변환된 11.11.11.11로 사용자에게 전송

- NAT 장비는 자신의 NAT 테이블을 확인해서 목적지 IP에 대한 원래 패킷을 발생시킨 출발지 IP 주소가 10.10.10.10을 확인한 후 사용자에게 전송을 합니다.

- PAT 동작 순서

출발지 사용자는 10.10.10.10 이라는 Private IP를 소유하고 있는 상황에서 목적지 IP는 20.20.20.20 이고 포트는 80번 인 경우

- 출발지 서비스 포트는 임의로 할당이 되는데 2000번으로 가정

- NAT 역할을 수행하는 장비에서는 사용자가 보낸 패킷을 수신한 후 NAT 정책에 따라 외부 네트워크 와 통신이 가능한 Public IP 11.11.11.11로 IP 주소를 변환하고 이 때 변경 전후의 IP 주소 뿐 만 아니라 포트번호인 2000번까지 NAT 테이블에 저장

- NAT 장비에서는 출발지 주소를 11.11.11.11로 변경하고 서비스 포트도 3000번으로 변경해서 목적지 웹 서버로 전송

- 패킷을 수신한 웹 서버에서는 사용자에게 응답을 보내는데 응답이므로 수신과 반대로 출발지는 웹 서버 20.20.20.20 이 되고 목적지는 NAT 장비에의해 변환된 11.11.11.11로 사용자에게 전송

- NAT 장비는 자신의 NAT 테이블을 확인해서 목적지 IP에 대한 원래 패킷을 발생시킨 출발지 IP 주소가 10.10.10.10에 2000번 포트인 것을 확인한 후 사용자에게 전송을 합니다

- NAT 와 PAT의 차이점은 NAT의 경우는 IP만 변환을 하지만 PAT의 경우는 포트도 변환을 수행합니다.

- 사용할 수 있는 포트가 제한적이어서 서비스 포트는 재사용하게 되는데 서비스 포트가 동시에 모두 사용 중이거나 재사용할 수 없을 대는 PAT가 제대로 동작하지 않습니다.
동시 사용자가 많을 때는 Public IP를 하나가 아닌 Pool로 구성해서 사용해야 합니다.

- PAT는 다수의 IP가 있는 출발지에서 목적지로 갈 때 NAT 테이블이 생성되고 응답에 대해 NAT 테이블을 참조할 수 있지만 PAT IP가 목적지일 때는 해당 IP가 어느 IP에 바인딩 되어 있는지 확인할 수 없습니다.

- SNAT 와 DNAT

- SNAT: 출발지 주소를 변경하는 NAT
- DNAT: 목적지 주소를 변경하는 NAT

트래픽이 출발하는 시작 시점을 기준으로 합니다
요청을 하면 SNAT를 해서 목적지로 전송을 하면 해당 트래픽에 대한 응답을 받을 때는 출발지와 목적지가 반대로 되므로 DNAT가 되는데 이 때 트래픽을 요청하는 시작 지점만 고려해 SNAT를 설정을 해야 합니다.

- 사용하는 경우
SNAT
    인터넷할 때 많이 사용
    보안 상 SNAT를 사용
    로드밸런서 구성에도 이용: 출발지와 목적지 서버가 동일한 대역일 때는 로드밸런서 구성에 따라 트래픽이 로드밸런서를 거치지 않고 응답할 수 있기 때문에 SNAT를 통해서 응답 트래픽이 로드밸런서를 거치게 할 수 있습니다.

DNAT 
    로드밸런서에서 많이 사용: 사용자는 서비스 요청을 위해 로드밸런서에 설정된 Virtual IP로 서비스를 요청하고 로드밸런서에서는 Virtual IP를 로드밸런싱된 Real IP로 DNAT해서 전송

- 동적 NAT 와 정적 NAT

- 동적 NAT는 다수의 IP 풀에서 필요할 때 마다 매핑을 하는 형태
- 정적 NAT는 출발지와 목적지 매핑 관계가 특정 IP 사전에 정의되는 형태

| 항목 | 동적 NAT | 정적 NAT |
| --- | --- | --- |
| NAT 설정 | 1:N, N:1, N:M | 1:1 |
| NAT 테이블 | NAT 수행 시 생성 | 사전 생성 |
| NAT 테이블 타임아웃 | 동작 | 없음 |
| NAT 수행 정보 | 실시간으로 확인(별도 변경 로그 저장 필요) | 별도 필요없음(설정이 NAT 내역) |

- 라우터에서 설정

R1 과 R2를 Ethernet2/0로 연결 - 외부로 나가는 것(192.168.1.0/24)
R1 과 PC를 Ethernet2/1로 연결 - 내부 네트워크(10.1.1.0/24)

- Router 2번 설정

```text
R2# configure terminal
R2(config)#interface ethernet2/0
R2(config-if)#ip address 192.168.1.254 255.255.255.0
R2(config-if)#no shutdown
R2(config-if)#exit
R2(config)#exit
R2#show interfaces ethernet2/0
```

- Router 2번 설정
```text
R1# configure terminal
R1(config)#interface ethernet2/0
R1(config-if)#ip address 192.168.1.1 255.255.255.0
R1(config-if)#no shutdown


R1(config-if)#interface ethernet2/1
R1(config-if)#ip address 10.1.1.1 255.255.255.0
R1(config-if)#no shutdown

R1(config-if)#exit
R1(config)#exit
R1#show interfaces ethernet2/0
R1#show interfaces ethernet2/1
```

- PC 설정
```text
PC1> ip 10.1.1.2 255.255.255.0 10.1.1.1
```

- 정적 NAT 설정(고정)
#정적 NAT를 활성화
```text
Router(config)#ip nat inside source static 로컬IP  전역IP

Router(config)#ip nat inside source static 10.1.1.2  192.168.1.2
```

#내부 인터페이스를 설정 - Router에서 내부로 연결된 인터페이스에 설정
```text
Router(config-if)# ip nat inside

R1(config)#interface ethernet2/1
R1(config-if)#ip nat inside
```

#외부 인터페이스를 설정 - Router에서 외부로 연결된 인터페이스에 설정
```text
Router(config-if)# ip nat  outside

R1(config)#interface ethernet2/0
R1(config-if)#ip nat outside
```

#확인
```text
Router#show ip nat translations
```

- 동적 NAT 설정
과정
    Public IP(외부 주소) 풀을 설정
    Router(config)# ip nat pool 이름 시작IP 끝IP netmask 서브넷마스크

    Router(config)# ip nat pool net-200 192.168.1.209 192.168.1.222 netmask 255.255.255.240

    변환될 주소의 범위를 설정
    Router(config)# access-list 번호 permit 네트워크주소 와일드카드마스크
    Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255

    동적NAT를 활성화
    Router(config)# ip nat inside source list 번호 pool 이름
    Router(config)# ip nat inside source list 1 pool net-200

    내부 인터페이스 활성화
    Router(config-if)# ip nat inside

    Router(config)# interface ethernet2/1
    Router(config-if)# ip nat inside

    외부 인터페이스 활성화
    Router(config-if)# ip nat outside

    Router(config)# interface ethernet2/0
    Router(config-if)# ip nat outside

    #확인
    Router#show ip nat translations

- R1 과 R2가 통신이 가능하도록 동적 라우팅
R1: 10.1.1.0/24, 192.168.1.0/24
R2: 192.168.1.0/24

```text
R1(config)# router ospf 1
```

R1(config-router) # network 192.168.1.0 0.0.0.255 area 0
R1(config-router) # network 10.1.1.0 0.0.0.255 area 0
R1(config-router) # exit
R1(config) # exit
```text
R1# show ip route

R2(config)# router ospf 1
```

R2(config-router) # network 192.168.1.0 0.0.0.255 area 0
R2(config-router) # exit
R2(config) # exit
```text
R2# show ip route
```

- PAT 설정
```text
Router(config)# ip nat inside source list 번호 interface 실제인터페이스 overload

Router(config)#no ip nat inside source list 1 pool net-200
Router(config)#ip nat inside source list 1 interface ethernet2/0 overload
```

### 2) DNS

- **개요**:

- 도메인 주소를 IP 주소로 변환하는 역할을 수행
- 클라우드 기반 인프라 구성이 많아지면서 인프라가 빈번히 변경되어 DNS 설계가 더 중요
- MSA(Micro Service Architecture) 기반의 서비스 설계가 많아지면서 다수의 API를 사용하다보니 사용자의 호출 뿐 만 아니라 서비스 간 API 호출이나 인터페이스가 많아져 DNS 설계가 중요해 졌습니다.

- DNS 구조 와 명명 규칙

- 도메인은 계층 구조여서 수많은 인터넷 주소 중 원하는 주소를 효율적으로 찾아갈 수 있습니다.
- 역트리 구조로 최상위 루트부터 Top-Level 도메인, Second-Level 도메인, Third-Level 도메인과 같이 하위 레벨로 원하는 주소를 단계적으로 찾아갑니다.
- 계층의 경계를 .으로 표시하고 뒤에서 앞으로 해석합니다.
Third.Second.Top. 같은 형태
www(Third-Level).naver(Second-Level).com(Top)
- 도메인 계층은 128계층까지 구성할 수 있으며 계층별 길이는 최대 63바이트까지 사용할 수 있고 도메인 계층을 구분하는 .을 포함해서 전체 도메인의 길이는 최대 255바이트까지 사용할 수 있습니다.
문자는 알파벳, 숫자, - 만 가능하며 대소문자 구분 안합니다.

- 루트 도메인
최상위 영역

도메인 요청을 하면 DNS 서버가 이를 받아서 처리를 하는데 도메인의 정보가 없으면 루트 도메인을 관리하는 루트 DNS에 쿼리하게 됩니다.
루트 DNS는 전 세계에 13개가 있음
DNS 서버를 설치하면 루트 DNS의 IP 주소를 기록한 힌트 파일을 가지고 있어 루트 DNS 관련 정보를 설정하지 않아도 됩니다. 

- Top-Level Domain(TLD)
IANA에서 6가지 유형으로 구분
유형
    gTLD(일반적으로 사용되는 최상위 도메인으로 3글자 이상으로 구성):  com, edu, gov, int, mil, net, org

    ccTLD(국가 최상위 도메인)

    sTLD(특정 목적을 위한 스폰서를 두고 있는 최상위 도메인): aero, asia, edu, museum 등

    infrastructure: .arpa(.IN-ADDR.ARPA 가 arpa의 하위 도메인으로 IPv4 주소를 도메인 이름에 매핑하는 역방향 도메인에서 사용)

    grTLD(특정 기준을 만족하는 사람이나 단체가 사용할 수 있는 최상위 도메인): biz, name, pro 등

    tTLD(테스트용): test

- DNS 주요 레코드

- A 레코드
기본 레코드로 도메인 주소를 IP 주소로 변환하는 레코드

사용자가 DNS에 질의한 도메인 주소를 A레코드에 설정한 IP 주소로 응답

기본적으로는 한 개의 도메인 주소 와 한 개의 IP 주소가 1:1로 매핑되지만 동일한 도메인을 가진 A 레코드를 여러 개 생성하는 것도 가능하고 다수의 도메인에 동일한 IP를 매핑한 A 레코드를 만들 수 있습니다. 

서버 한 대에 여러 웹 서비스를 구동해야 한다면 여러 도메인에 동일한 IP를 매핑하고 HTTP 헤더의 HOST 필드에 도메인을 명시해 웹 서버를 구분해 서비스 할 수 있습니다.

- AAAA 레코드
IPv6에서 사용되는 레코드

- CNAME(Canonical NAME) 레코드
별칭 이름을 사용하게 해주는 레코드

레코드 값에 IP 주소를 매핑하는 A 레코드와 달리 CNAME 레코드는 도메인 주소를 매핑합니다.
네임서버가 CNAME 레코드에 대한 질의를 받으면 CNAME 레코드에 설정된 도메인 정보를 확인하고 그 도메인 정보를 내부적으로 다시 질의한 결과 IP 값을 응답합니다.
CNAME 레코드의 대표적인 것이 www 입니다

adam.net 이라는 웹 사이트에 접속하려면 일반적으로 adam.net 이나 www.adam.net으로 접속을 합니다.
adam.net 과 www.adam.net을 각각 A 레코드로 매핑하면 IP 주소가 변경될 때 두 개의 레코드 값을 모두 변경해야 하지만  adam.net 만 A 레코드로 IP 주소를 매핑하고 www.adam.net은 CNAME으로 adam.net 으로 매핑하면 IP 주소가 변경될 때 adam.net 만 변경해도 동일한 결과를 만들어 냅니다.

클라우드 환경에서 CNAME으로 도메인 설정을 많이 합니다.

- SOA(Start Of Authority) 레코드
도메인 영역에 대한 권한을 나타내는 레코드
현재 네임 서버가 이 도메인 영역에 대한 관리 주체임을 의미하므로 해당 도메인에 대해서는 다른 네임 서버에 질의하지 않고 직접 응답합니다.

- NS(Name Server) 레코드
네임 서버 정보를 설정하는 레코드

- MX(Mail eXchange) 레코드
메일 서버를 구성할 때 사용하는 레코드

- 화이트 도메인

- 정상적으로 발송하는 대량 이메일이 RBL(Realtime Blackhole List, Blackhole 대신에 Blocking이라고도 함) 이력으로 간주되어 차단되는 것을 예방하기 위해 등록된 개인이나 사업자에 한해 국내 주요 포탈 사이트로의 이메일 전송을 보장해주는 제도
- KISA에서는 불법적인 방법으로 발송되는 스팸메일 차단활동을 하고 있는데 불법적인 스팸메일을 발송하는 사이트를 실시간 블랙리스트 정보로 관리해서 메일 발송을 제한하는데 이 실시간 블랙리스트를 RBL이라고 합니다.
- 현재 보유중인 도메인을 화이트 도메인으로 등록하려면 KISA RBL 사이트에서 화이트 도메인으로 등록하면 됩니다.
사전에 해당 도메인에 SPF 레코드를 설정해야 합니다.

- 한글 도메인

- 퓨니코드로 DNS에 도메인을 생성해야 합니다.
- 퓨니코드: 다국어 도메인이 아스키로 변환된 구문인데 xn으로 시작

### 3) GSLB

- **개요**:

- DNS에서 동일한 레코드 이름으로 서로 다른 IP 주소를 동시에 설정할 수 있습니다.
이렇게 하면 도메인 질의에 따라 응답받는 IP 주소를 나누어 로드밸런싱 할 수 있습니다.
이것을 DNS LoadBlancing 이라고 합니다.

- DNS 만 이용한 로드밸런싱으로는 정상적인 서비스를 하지 못할 수 있습니다.
DNS는 설정된 서비스 상태의 정상 여부를 확인하지 않고 도메인에 대한 질의에 대해 설정된 값을 무조건 응답합니다.
DNS에 저장된 레코드와 매핑된 서비스가 모두 정상일 때는 문제가 없지만 특정 서비스에 문제가 있을 때 DNS 서버는 이를 감지하지 못해 사용자의 도메인 질의 요청에 비정삭적 상태인 서비스 IP 주소를 응답한 경우 사용자는 해당 서비스에 접근할 수 없습니다.
DNS 서버는 각 레코드에 대한 서비스 체크가 이루어지지 않고 설정된 값에 따라 동작하므로 서비스 가용성 방법으로는 부적합

- GSLB(Global Server/Service Load Balancing)는 DNS의 이런 문제점을 해결해서 도메인을 이용해서 로드밸런싱 하는 것을 도와줍니다.
GSBL는 DNS 와 동일하게 도메인 질의에 응답해주는 역할 과 동시에 로드 밸런서처럼 등록된 도메인에 연결된 서비스가 정상적인지 헬스체크를 수행

### 4) DHCP

- **개요**:

- 호스트가 네트워크 와 통신을 할려면 물리적 네트워크 구성은 물론 IP 주소, SubnetMask, Gateway 와 같은 네트워크 정보 와 DNS 주소 설정도 필요
이런 네트워크 정보를 호스트에 적용하려면 사용자가 직접 설정하거나 이런 정보를 할당해주는 서버를 이용해 자동으로 설정해야 합니다.
수동으로 IP 와 네트워크 정보를 직접 설정하는 것을 정적 할당이라고 하고 자동으로 설정하는 것을 동적 할당이라고 합니다.
- 데이터 센서의 서버 팜 과 은 운영망에서는 주로 정적 할당을 이용하지만 PC 사용자를 위해 운영되는 사무실 네트워크에서는 IP 자동으로 할당받는 동적 할당 방식을 많이 사용합니다.
클라우드 환경에서 인프라를 임대해서 사용할 때는 기본적으로 동적 할당인데 정적 할당으로 수정할 수 있습니다.
- 예전에는 보안 문제 때문에도 정적 할당을 사용햇는데 지금은 동적 할당 방식을 이용하면서도 보안을 강화하고 쉽게 관리하도록 도와주는 서비스 와 장비가 대중화되어서 동적 할당 방식이 많이 사용됩니다.
- IP를 동적으로 할당하는데 사용되는 프로토콜이 DHCP
DHCP를 사용하면 사용자가 직접 입력해야 하는 IP 주소, 서브넷 마스크, 게이트웨이, DNS 정보를 자동으로 할당받아 사용합니다.
사용자는 별도의 IP 설정 작업없이 편리하게 네트워크에 접속할 수 있고 사용하지 않는 IP 정보를 회수되어 사용하는 경우에만 재할당되어 사용자 이동이 많고 한정된 IP 주소를 가진 경우 유용하게 사용할 수 있습니다.

- DHCP 프로토콜

- BOOTP(Bootstrap Protocol)라는 프로토콜을 기반으로 함
- DHCP는 BOOTP 와 유사하게 동작하지만 BOOTP에서 지원하지 않는 몇 가지 기능이 추가된 확장 프로토콜
- DHCP와 BOOTP 프로토콜 사이에는 호환성이 있어서 BOOTP 와 DHCP에서 사용되는 서비스 포트가 같고 BOOTP 클라이언트가 DHCP 서버를 사용하거나 DHCP 클라이언트가 BOOTP 서버를 사용해 정보를 수신할 수 있습니다.
- DHCP는 서버 와 클라이언트로 동작하는데 클라이언트의 서비스 포트는 68 번이고 서버의 서비스 포트는 67입니다.
- 이 서비스는 UDP 프로토콜

- 리눅스에서 DHCP 서버 만들기

- dhcp 패키지를 설치

- /usr/share/doc/dhcpcd-버전/dhcpd.com.example 파일을 /etc/dhcp/dchpd.conf 로 복사

- 파일 수정
default-lease-time 초; #기본 임대 시간
max-lease-time 초: 최대 임대시간

subnet 네트워크주소 netmask 서브넷마스크{
 range 실제서비스시작주소 실제서비스끝주소;

 option routers 디폴트게이트웨이주소;
 option domain-name-servers DNS 주소;
}

- 서비스 데몬을 실행

```bash
systemctl start dhcpd.service
```
