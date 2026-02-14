# Docker Swarm

## 1. Container Orchestration

### 1) 개요
컨테이너 오케스트레이션은 수 많은 컨테이너의 배포, 관리, 확장, 네트워킹을 자동화하는 프로세스

### 2) 필요한 이유
- **대규모 관리**: 여러 개의 컨테이너를 하나 하나 수동으로 실행하고 종료하는 것은 비효율적
- **장애 복구**: 특정 컨테이너가 죽었을 때 즉시 감지하고 새 컨테이너를 실행해야 함
- **확장성**: 사용자가 몰릴 때 컨테이너 수를 자동으로 늘리고 한산해지면 줄여야 함
- **로드밸런싱**: 트래픽을 여러 컨테이너에 골고루 분산시켜야 함

### 3) 핵심 기능
- **프로비저닝 및 배포**: 어떤 서버에 컨테이너를 띄울지 결정하고 실행
- **리소스 할당**: 컨테이너가 필요로 하는 CPU나 메모리를 적절히 배정
- **상태 모니터링**: 컨테이너가 정상적으로 동작하고 있는지 감시
- **서비스 디스커버리**: 컨테이너끼리 서로를 찾아서 통신할 수 있게 연결
- **보안**: 컨테이너 간의 접근 권한을 관리

### 4) 대표적인 도구
- **docker-swarm**: 도커에서 직접 만든 도구로 설치가 쉽고 간단한 환경에 적합
- **kubernetes**: 사실상의 표준으로 기능이 강력하고 생태계가 넓지만 배우기가 어려움
- **Public Cloud에서 제공하는 도구**: AWS의 ECS나 EKS 등

## 2. Docker Swarm

### 1) 개요
- 여러 도커 호스트를 클러스터로 묶어주는 컨테이너 오케스레이션 도구
- 컨테이너 오케스트레이션 도구 없이 도커 호스 여러 대를 사용하는 확장성있는 애플리케이션을 만들기가 어려움
- 직접 어느 도커 호스트에 어떤 컨테이너를 배치해야 하는지 서로 다른 호스트에 위치한 컨테이너간의 통신은 어떻게 제어해야 하는지 등의 조율을 오케스트레이션 도구없이 하기는 거의 불가능

### 2) 도커 도구
- **compose**: 여러 컨테이너로 구성된 도커 애플리케이션을 관리(주로 단일 호스트)하며 명령어는 `docker-compose`로 시작
- **swarm**: 클러스터 구축 및 관리(주로 멀티 호스트)하며 명령어는 `docker swarm`으로 시작
- **service**: 스웜에서 클러스터 안의 서비스(하나 이상의 컨테이너 집합)를 관리하며 명령어는 `docker service`로 시작
- **stack**: 스웜에서 여러 개의 서비스를 합한 전체 애플리케이션을 관리하며 명령어는 `docker stack`으로 시작

### 3) 도커 호스트로 스웜 클러스터 구성
- 도커 머신즈를 사용하면 여러 대의 도커 호스트를 만들 수 있는데 도커 머신즈를 사용할려면 Mac OS에서는 virtualbox 드라이버가 그리고 윈도우에서는 hyper 드라이버가 필요
- Virtual Box 같은 가상화 소프트웨어를 이용해서 물리 호스트 1대에 여러 대의 도커 호스트를 만들어서 구성하는 것도 가능
- Docker in Docker라는 기능을 이용하면 도커 컨테이너 안에서 도커 호스트를 실행할 수 있음
- 가상 머신을 이용하면 가상 머신 시작 및 정지에 시간이 걸리고 오버헤드로 소모되는 머신 리소스도 큼

### 4) 주요 기능

#### 역할이 분리된 분산 설계

도커 스웜 클러스터의 노드의 역할:
- manager node
- leader node
- worker node

클러스터가 단일 관리자 환경이면 매니저 노드와 리더 노드가 별도로 생성되지 않고 매니저 노드가 리더 노드의 역할을 수행하는데 매니저 노드와 워커 노드로 구분

매니저 노드는 클러스터의 관리 역할로 컨테이너 스케쥴링 서비스 및 상태 유지 등을 제공하고 워커 노드는 컨테이너를 실행하는 역할만 수행

도커 스웜의 매니저 노드는 서비스 컨테이너를 실행하는 역할도 가능

> **참고**: 매니저 노드에 컨테이너 서비스를 실행하지 않도록 하려면 서비스를 만들 때 `--constraint node.role!=manager` 옵션을 추가해주면 됩니다.

#### 도커 엔진과 통합된 다중 서버 클러스터 환경
도커 엔진에 포함된 도커 스웜 모드를 통해 별도의 오케스트레이션 도구를 설치하지 않아도 컨테이너 애플리케이션 서비스를 배포하고 관리할 수 있음

#### 서비스 확장과 원하는 상태 조정
- 도커 스웜 모드에서 서비스 생성 시 안정적인 서비스를 위해서 중복된 서비스 배포가 가능하고 초기 구성 후 스웜 관리자를 이용해서 애플리케이션 가용성에 영향을 주지 않고도 서비스 확장 및 축소를 수행할 수 있음
- 배포된 서비스는 매니저 노드를 통해 지속적으로 모니터링
- 사용자가 요청한 상태와 다르게 서비스 장애가 생길 경우 장애가 발생한 서비스를 대체할 새로운 복제본으로 자동으로 생성해서 사용자 요구를 지속하게 되는데 이것이 요구 상태 관리

#### 서비스 스케쥴링
- 스케쥴링 기능은 도커 스웜 모드 클러스터에 작업 단위의 서비스 컨테이너를 배포하는 작업
- 도커 클래스 스웜 엔진의 노드 선택 전략:
  - 모든 작업자 노드에 균등하게 할당하는 **spread 전략**
  - 작업자 노드의 자원 사용량을 고려해서 할당하는 **binpack 전략**
  - 임의의 노드에 할당하는 **random 전략**
- 도커 스우머 모드는 단일 옵션으로 고가용성 분산 알고리즘(HA spread alogrithm)을 사용하는데 이 방식은 생성되는 서비스의 복제본을 분산 배포하기 위해 현재 복제본이 가장 적은 작업자 노드 중에서 이미 스케쥴링 된 다른 서비스 컨테이너 수가 가장 적은 작업자 노드를 우선 선택

#### 로드 밸런싱
- 도커 스웜 모드를 초기화하면 자동으로 생성되는 네트워크 드라이버 중 하나가 Ingress Network
- ingress network는 서비스의 노드 간 load balancing이나 외부에 서비스를 노출하기 위해 사용되는 overlay network
- 도커 스웜 모드는 서비스 컨테이너에 published port를 자동으로 할당(30000~32767)하거나 수동으로 노출할 포트를 구성할 수 있는데 서비스 컨테이너가 포트를 오픈하면 동시에 모든 노드에서 동일한 포트가 오픈되기 때문에 클러스터에 합류된 어떤 노드에 요청을 전달해도 실행 중이 서비스 컨테이너에 자동으로 전달되는데 이유는 모든 노드가 스웜 모드의 routing mesh에 참여하기 때문

#### 서비스 검색
- 도커 스웜 모드는 서비스 검색을 위해서 자체 DNS 서버를 통한 서비스 검색 기능
- 도커 스웜 모드 매니저 노드는 클러스터에 합류된 모든 노드의 서비스에 고유한 DNS 이름을 할당하고 할당된 DNS 이름은 도커 스웜 모드에 내장된 DNS 서버를 통해 스웜모드에서 실행 중인 모든 컨테이너를 조회할 수 있게 되는데 이것을 service discovery라고 합니다

#### 롤링 업데이트
- 매니저 노드를 통해 현재 실행 중인 서비스의 컨테이너의 업데이트를 노드 단위로 점진적으로 적용할 수 있는 Rolling Update를 할 수 있음
- 롤링 업데이트는 각 작업자 노드에서 실행 중인 서비스 컨테이너를 노드 단위의 지연적 업데이트를 수행하는 것
- 새 버전 서비스 컨테이너를 하나씩 늘려가고 이전 버전 서비스 컨테이너는 하나씩 줄여나가는 방식
- 업데이트가 실패한 경우 다양한 기능을 통해 재시도 및 업데이트 중지 등을 수행할 수 있고 잘못된 업데이트를 취소하기 위해 rollback 기능도 제공

### 5) 도커 스웜 클러스터 구성

#### 구성
- manager 1개
- worker 2개

#### 호스트 이름과 네트워크 설정

NatNetwork 생성

가상 머신의 네트워크를 위에서 생성한 NatNetwork로 설정

IP 수동 설정: `/etc/netplan` 디렉토리에 yaml 파일을 생성해서 작성
- swarm-manager: 10.0.2.101
- swarm-worker1: 10.0.2.102
- swarm-worker2: 10.0.2.103

호스트 이름 설정:
```bash
sudo hostnamectl set-hostname 이름
```

내부 DNS 파일 수정:
```bash
sudo vi /etc/hosts
```

#### 모든 컴퓨터에 도커가 설치되어야 함

#### 클러스터 초기화

manager로 사용할 컴퓨터에서 초기화:
```bash
docker swarm init
```

현재 스웜 모드 상태를 조회:
```bash
docker info | grep Swarm
```
active 상태이면 초기화가 수행된 것임

스웜 모드에서 사용하는 포트: 2377번과 7946입니다.
방화벽을 사용 중이면 이 2개의 포트는 개방
```bash
sudo netstat -nlp | grep dockerd
```

#### 워커 노드를 클러스터에 연결

`docker swarm init` 했을 때 나온 `docker swarm join` 문장을 복사해서 붙여넣기 하면 됩니다.

매니저에서 토큰을 다시 확인:
```bash
docker swarm join-token worker
```

확인: 매니저 노드에서
```bash
docker node ls
```

새로운 네트워크 확인:
```bash
docker network ls
```

### 6) 서비스 생성 및 제거

#### 서비스 생성
```bash
docker service create --name swarm-start alpine:3 /bin/ash -c "while true; do echo 'Docker Swarm Mode Start'; sleep 3; done"
```

#### 서비스 목록 조회
```bash
docker service ls
```

#### 서비스의 정보 조회
```bash
docker service ps swarm-start
```

#### 로그 확인
```bash
docker service logs -f {ID}
```

#### 서비스 삭제
```bash
docker service rm swarm-start
```

### 7) Load Balancing 확인

#### 개요
- 도커 스웜 모드 클러스터에서는 서비스 컨테이너를 복제 모드로 배포할 경우 구성된 모든 클러스터 노드에서 접속이 가능
- 서비스 컨테이너 생성 시 노출할 포트를 `--publish` 옵션으로 구성할 수 있고 서비스 컨테이너가 포트를 오픈하면 동시에 모든 노드에서 동일한 포트가 오픈되기 때문에 클러스터에 합류된 어떤 노드에 요청을 전달해도 실행 중인 서비스 컨테이너에 자동 던달되는데 이것은 모든 노드 스웜 모드의 라우팅 메시에 참여하기 대문
- 인그레스 네트워크를 통해서 자동 로드 밸런싱을 수행

#### nginx 컨테이너를 이용해서 로드밸런싱을 확인

서비스 생성:
```bash
docker service create --name web-alb --constraint node.role==worker --replicas 2 --publish 8001:80 nginx
```

서비스 확인:
```bash
docker service ps web-alb
```

클러스터의 모든 노드에 8001 포트가 개방(netstat가 없으면 `sudo apt install net-tools`):
```bash
sudo netstat -nlp | grep 8001
```

**worker1에서 작업**

`index.html` 작성:
```html
<h1>Hi Docker Swarm 1</h1>
```

docker ps 명령으로 컨테이너 이름을 확인

index.html을 복사:
```bash
docker cp index.html 컨테이너이름:/usr/share/nginx/html/index.html
```

**worker2에서 작업**

`index.html` 작성:
```html
<h1>Hi Docker Swarm 2</h1>
```

docker ps 명령으로 컨테이너 이름을 확인

index.html을 복사:
```bash
docker cp index.html 컨테이너이름:/usr/share/nginx/html/index.html
```

**manager에서 수행**
```bash
curl 10.0.2.101:8001
curl 10.0.2.101:8001
curl 10.0.2.101:8001
```

어떤 노드의 IP를 이용하더라도 Manager 노드가 받아서 로드밸런싱을 수행해서 적절한 컨테이너에게 요청을 전달

#### 동적 확장 및 축소

확장:
```bash
docker service scale web-alb=3
```

확인:
```bash
docker service ps web-alb
```

#### 모든 노드에 서비스를 전부 배포
로깅을 하거나 모니터링하는 서비스를 배포할 때 사용

```bash
docker service create --name global_nginx --mode global nginx

docker service ps global_nginx
```

#### 서비스 삭제
```bash
docker service rm web-alb global_nginx
```

### 8) 서비스 유지 관리 기능

#### 개요
- 오케스트레이션 도구는 장애 복구 기능을 가지고 있음
- 배포된 서비스 장애 및 노드의 장애가 발생하면 자동으로 복제 수 만큼의 서비스를 맞추어 장애에 대한 자동 복구를 수행
- 패키지 버전 변경 수행 시 서비스를 중단하지 않고 롤링 업데이트 기능을 사용해서 새 버전 서비스 컨테이너는 하나씩 늘려가고 이전 버전 서비스 컨테이너는 하나씩 줄여가는 방식으로 버전 업데이트를 수행할 수 있음
- 업데이트가 실패할 경우 다양한 기능을 통해 재시도 및 업데이트 중지 등을 선택 수행할 수도 있고 잘못된 업데이트를 취소하기 위해 롤백 기능도 제공

#### 장애 발생

서비스 생성:
```bash
docker service create --name web-alb --replicas 3 --publish 8001:80 nginx
```

노드 중 하나에서 컨테이너 확인:
```bash
docker ps
```

강제 삭제:
```bash
docker rm -f 이름
```

매니저에서 서비스 확인:
```bash
docker service ps web-alb
```

#### 롤링 업데이트

서비스 생성:
```bash
docker service create --name my-database --replicas 3 redis:6.0-alpine
```

확인:
```bash
docker service ps my-database
```

서비스 업데이트:
```bash
docker service update --image redis:6.2.5-alpine my-database
```

확인:
```bash
docker service ps my-database
```

#### 추가적인 옵션도 제공

**롤링 업데이트 관련**
- `--update-parallelism 개수`: 동시에 업데이트할 컨테이너 개수 설정
- `--update-delay`: 하나의 업데이트가 끝나고 다음 업데이트까지의 대기 시간
- `--update-order`: 업데이트 순서 결정
  - `start-first`: 먼저 시작하고 기존 것을 삭제
  - `stop-first`: 기존 것을 먼저 삭제하고 새로운 것을 시작

**업데이트 중 오류 발생**
- `--update-failure-action`
