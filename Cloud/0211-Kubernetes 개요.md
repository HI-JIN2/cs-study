# Kubernetes
## 1.개요
### 1)Container Runtime
#### 개요
- 일반적인 가상화는 하드웨어 수준에서 가상화하지만 Container는 운영체제 수준에서 가상화를 수행
- 운영체제의 커널을 공유해서 가볍고 유연하게 운영 가능
- 컨테이너 화된 애플리케이션은 몇 초 내로 빠르게 실행되며 가상머신과 비교했을 때 자원을 더 적게 사용해서 하나의 시스템에서 더 많은 애플리케이션을 구동할 수 있음
- 운영체제를 공유해서 사용하기 대문에 패치, 업데이트 등 유지관리와 관련한 오버헤드가 줄어든다는 장점이 있음
- 빠르게 변화하는 비즈니스 환경에서는 Container가 현재까지는 최적의 솔루션
- 런타임은 프로그래밍 언어로 만든 애플리케이션이 구동되는 환경을 의미하는데 자바스크립트가 브라우저에서 실행되면 런타임은 브라우저
- Container를 생성하고 실행할 수 있도록 도와주는 것이 Container Runtime

#### 종류
- Containerd
컨테이너 실행을 관리하는 오픈 소스 런타임

Docker 및 Kubernetes 와 같은 컨테이너 오케스트레이션 시스템에서 컨테이너 실행을 처리하는데 사용

Container 이미지를 다운로드 받아 압축을 푸는 과정부터 Container를 실행하고 감독하는 과정까지 Container의 전체 수명 주기를 관리하는 기능을 제공

- Docker
Containerd 위에 설치되는 데몬

Container를 생성하고 관리하는데 필요한 모든 기능을 제공

Docker는 컨테이너의 전체 라이프사이클(빌드, 배포, 실행)을 관리하는 반면 Containerd는 컨테이너 실행 과 이미지 관리를 담당하는 핵심 컴포넌트로 Docker 와 Kubernetes는 Containerd를 기본 런타임으로 지원

- CRI-O
Redhat, Intel, SUSE, Hyper, IBM 등의 관리자 및 커뮤니티를 중심으로 개발된 오픈 소스 런타임

Docker를 대체하기 위한 툴로 개발

### 2)Container Orchestration
#### 개요
- 다수의 Container를 유기적으로 연결 및 실행할 뿐 만 아니라 상태를 추적하고 보존하는 등 Container를 안정적으로 사용할 수 있게 만들어주는 솔루션
- Container를 효과적으로 관리하도록 도와주는 것이 Container Orchestration
- Container 오케스트레이션은 여러 시스템에 Container를 분산해서 배치하거나 문제가 생긴 Container를 교체하는 등의 역할을 수행

#### 종류
- Docker Swarm
간단하게 설치할 수 있고 사용하기도 용이

기능이 다양하지 않아 대규모 환경에 적용하려면 사용자 환경을 변경해야 할 수 있기 때문에 소규모 환경에서는 유용하지만 대규모 환경에서는 잘 사용하지 않음

- Mesos
Apache의 오픈 소스 프로젝트로 Twitter, AirBnB, Apple, Uber 등 다양한 곳에서 이미 검증된 솔루션

2016년 DC/OS(Data Center OS, 대규모 서버 환경에서 자원을 유연하게 공유하고 하나의 자원처럼 관리하는 도구)의 지원으로 매우 간결하지만 기능을 충분히 활용하려면 분산 관리 시스템과 연동해야 하기 때문에 여러가지 솔루션을 유기적으로 구성해야 하는 부담이 있음

- Nomad
Vagrant를 만든 HashiCorp 사의 Container 오케스트레이션으로 Vagrant처럼 간단한 구성으로 컨테이너 오케스트레인션 환경을 제공

HashiCorp의 Consul(서비스 검색, 구성 및 분할 기능 제공) 과 Valut(암호화 저장소) 와 연동이 원할하므로 이런 도구에 대한 성숙도가 높은 조직이라면 노마드 도입을 고려해 볼 수 있음

- Kubernetes
컨테이너화 된 애플리케이션으로 자동으로 배포, 스케일링 및 운영하는 오픈 소스 오케스트레이션 플랫폼

구글이 내부적으로 사용하던 Borg 시스템에서 발전한 기술로 현재는 Cloud Native Computing Foundation(CNCF)에서 관리

## 2.Kubernetes
### 1)개요
#### Container 기반의 애플리케이션을 개발하고 배포할 수 있도록 설계된 오픈 소스 플랫폼으로 Container 오케스트레이션 도구의 일존

#### k8s 라고도 하는데 k 와 s 사이의 8글자가 있어서 임

#### Kubernetes는 일반적인 프로그래머가 관리하는 일은 거의 없음
- 대규모 시스템을 개발하는 프로그래머가 대규모 서버 군을 직접 관리하는 일은 드물듯이 kubernetes를 직접 관리하는 일음 드뭄
- kubernetes로 어떤 일을 할 수 있는지에 대한 지식은 시스템을 개발할 때 유용할 수 있는데 kubernetes로 관리되는 시스템은 이를 전제로 개발되어야 한느데 그렇지 않다면 Kubernetes의 이점을 제대로 살릴 수 없기 때문에 프로젝트 매니저나 시스템 엔지니어를 담당하는 사람도 kubernetes로 어떤 일을 할 수 있는가는 잘 이해해야 함

#### 쿠버네티스는 여러 대의 물리적 서버에 걸쳐 실행되는 것을 전제로 함
- 물리적 서버 한 대 한 대 마다 제각기 여러 개의 Container를 실행한다고 가정
- 여러 대의 서버에서 일일이 Container를 실행하고 관리하기는 쉬운일이 아닌데 Kubernetes는 이를 위한 도구
- Docker 만을 이용해서 20개의 Container를 만들려면 docker run 명령을 20번 실행해야 하고 서로 다른 컴퓨터에서 작업을 수행해야 함

### 2)쿠버네티스를 사용하는 이유
#### 자동화된 컨테이너 오케스트레이션
- 컨테이너의 배포, 관리, 확장, 복구를 자동으로 수행
- 여러 개의 컨테이너를 그룹화하여 하나의 애플리케이션 처럼 운영

#### 무중단 서비스 - Rolling Update
- 새 버전의 Pod를 점진적으로 늘림
- 새 Pod가 준비되면 구 버전의 Pod를 종료
- 이 과정을 통해 전체 Pod를 한 번에 교체하는 것이 아니라 일부 인스턴스 별로 순차적으로 교체해서 서비스의 가용성을 유지

#### 효유적인 자원 사용
- Pod가 사용할 수 있는 자원(CPU, Memory, Request 등)을 사전에 지정할 수 있어서 시스템의 전체 자원을 관리할 수 있기 때문에 자원을 효율적으로 사용할 수 있음
- Traffic Routing: 트래픽은 Service 와 Ingress를 통해 원하는 Pod로 전달

#### 유연한 확장성
- Auto-Scaling: CPU/Memory 사용량을 모니터링하여 자동으로 확장 또는 축소하는 것
- Kubernetes는 Pod의 자원 사용률에 따라 Pod의 개수를 늘리거나 줄일 수 있기 때문에 CPU 사용률이 200%로 증가하는 경우 Pod를 1개에서 5개로 늘리고 CPU 사용률이 감소하면 Pod의 개수를 1개로 줄일 수 잇음

#### 항상 바람직한 상태를 유지
- Kubernetes도 Container를 생성하거나 삭제할 수 있지만 일일이 명령어를 입력하는 방식을 사용하지는 않음
- Container는 00개 볼륨을 XX개로 구성하라와 같이 어떤 바람직한 상태를 YAML 파일에 정의하면 이를 읽어서 자동으로 Container를 생성하거나 삭제하면서 이 상태를 만들고 유지하는 것이 Kubernetes의 기본적인 아이디어
- docker-compose는 옵션을 이용해서 수동으로 Container의 수를 바꿀 수는 있지만 모니터링 기능이 없어서 Container를 만들 때 외에는 관여하지 않지만 이에 비해 kubernetes는 이 상태를 유지하는 기능이 있음
- self-healing
장애가 발생한 컨테이너를 자동으로 다시 시작
node의 장애가 발생하면 애플리케이션을 다른 노드로 이동

#### 쿠버네티스 표준 생태계로 편해진 IT 인프라 구축: 클라우드 벤더 종속성 해결
- CSP에서는 전부 쿠버네티스 서비스를 제공

### 3)쿠버네티스 리소스
#### 리소스 분류
- Workload API: 컨테이너 실행에 관련된 API
Pod
ReplicationController
ReplicaSet
Deployment
DaemonSet
StatefulSet
Job
CronJob

- Service API: 컨테이너를 외부에 공개하는 End Point를 제공하는 리소스
ClusterIP
ExternalIP
NodePort
LoadBalancer
Headless
ExternalName
None-Selector
Ingress

- Config & Storage API: 설정이나 기밀 정보 또는 영구 볼륨에 관련된 리소스
Secret
ConfigMap
Persitent Volume Claim

- Cluster API: 보안이나 쿼터 등에 관련된 리소스
Node
Namespace
Persistent Volume
ResourceQuota
ServiceAccount
Role
ClusterRole
RoleBinding
ClusterRoleBinding
NetworkPolicy

- Meta Data API: 클러스터 내부의 다른 리소스를 관리하기 위한 리소스
LimitRange
HorizontalPodAutoScaler
PodDistruptionBudget
CustomResourceDefinition

### 4)쿠버네티스 아키텍쳐
#### Pod
- 하나의 애플리케이션을 생성하기 위해서는 하나 이상의 Pod가 필요한데 Pod는 쿠버네티스에서 생성할 수 있는 가장 작은 배포단위이면서 단일 혹은 다수의 Container를 포함
- Pod 외에 Service, Volume, Namespace 등을 묶어서 Object라고 하고 Object는 Kubernetes에서 상태 관리가 필요한 대상
- Pod 와 Container
Pod는 Kubernetes에서 가장 기본적인 배포단위이면서 하나 이상의 Container를 포함

kubernetes는 Container를 개별적으로 하나식 배포하는 것이 아니라 유사한 역할을 하는 Container를 Pod라는 단위로 묶어서 배포

쇼핑몰 웹 사이트에는 사용자가 상품을 검색하고 주문을 하는 애플리케이션 과 상품 정보 및 이미지를 저장하는 저장소(데이터베이스)가 있는데 이 둘은 서로 유기적인 관계를 갖기 때문에 애플리케이션과 저장소는 별도의 Container이지만 이 둘을 하나로 묶어서 Pod로 배포할 수 있음

마이크로서비스를 이야기 할 때 Pod를 기준으로 분리해야 한다라고도 합니다. 

#### 쿠버네티스 구조
- Cluster: 여러 리소스를 관리하기 위한 집합체로 Master Node 와 Worker Node를 이용해 하나의 클러스터를 구성

- Node
Kubernetes 리소스 중에서 가장 큰 개념
Kubernetes Cluster의 관리 대상으로 실제 Pod가 배치되는 대상
클러스터 전체를 관리하는 Master(Control Plane) Node 가 1개 이상 존재해야 함
쿠버네티스 클러스터						영구 스토리지(Persistent Storage)
Master Node			Worker Node
	API Server			kubelet
	Scheduler				proxy
	Controller Manager			container runtime
						pod
							container
	etcd
	
- Master Node
Kubernetes Cluster 전체를 관리하는 시스템으로 Control Plane

Master Node를 설정하는 관리자의 컴퓨터에는 kubectl을 설치하는데 kubectl을 설치해야 Master Node에 로그인을 해서 초기 설정을 진행하거나 추후 조정 가능

- Persistent Storage
Pod는 휘발성이므로 Worker Node에 떠 있는 Pod가 삭제되면 Pod 안의 모든 데이터도 삭제되기 때문에 중요한 데이터는 Pod 외부에 있는 저장소에 저장해 두어야 하며 CSI(Container Storage Interface)로 외부 저장소를 Pod에 연결할 수 있습니다.

- Worker Node
Container Runtime이 설치된 환경으로 Pod가 실제로 생성되고 유지되는 Node

Linux 위에 Container Runtime을 설치하고 그 위에 하나 이상의 Container를 포함한 Pod를 생성하면 목적에 맞는 서비스가 실행됨

### 5)Cluster 구축
#### 방법
- Local Kubernetes: 물리 머신 1대에 구축해서 사용
- Kubernetes 클러스터 구축 도구 이용: Onpremise 환경이나 클라우드 환경에서 클러스터를 구축해서 사용
- 관리형 Kubernetes: CSP에서 제공하는 관리형 서비스를 이용

#### Local Kubernetes 활용
- Docker Desktop 활용

- Minikube 
물리 머신에 로컬 쿠버네티스를 쉽게 구축하고 실행할 수 잇는 도구

쿠버네티스의 SIG-Cluster-Lifecyle 이라는 분과회에서 만듬

Minikube를 사용하기 위해서는 로컬 가상 머신에 쿠버네티스를 설치하기 위해 하이퍼바이저가 필요한데 Docker 또는 Virtual Box 나 베어메탈 등

https://minikube.sigs.k8s.io/docs/start/

- kind
Docker 컨테이너 노드를 이용해서 Local Kubernetes Cluster를 실행하기 위한 도구

Kubernetes 자체를 테스트하기 위해서 설계되었지만 로컬 개발이나 CI에 사용될 수 있음

kind 설치: https://kind.sigs.k8s.io/docs/user/quick-start

kubectl 설치: https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-linux/

여러 개의 Worker Node를 가진 클러스터를 생성하는 것이 가능

- k3s & k0s
쿠버네티스 클러스터를 구축하는데 필수적인 구성 요소들을 간추려서 단일 파일로 설치 및 실행하기 때문에 리소스 경량화와 클러스터 구축 간소화를 모두 얻을 수 있음

쿠버네티스는 데이터의 저장소로 etcd를 사용하는데 이 두 프로젝트는 더 가벼운 sqlite3를 사용할 수 있고 etcd 나 MySQL도 사용 가능

적은 사용량 덕분에 테스트 및 배포 환경에서도 사용하지만 IoT 환경에도 사용 가능

현재는 Linux에서만 가능

k3s: https://docs.k3s.io
k0s: https://docs.k0sproject.io/head/install/

#### 구성형 Kubernetes 
- 개요
사용하는 시스템에 Kubernetes Cluster를 자동으로 구성해주는 솔루션을 사용

주요 솔루션으로는 kubeadm, kops(Kubernetes Operations), KRIB(Kubernetes Rebar Integrated Bootstrap), kubespray 등 이 있음

kubeadm이 가장 널리 알려져 있는데 kubeadm은 사용자가 변경하기도 수월하고 온프레미스 와 클라우드를 모두 지원하고 배우기도 쉬운편

- 리눅스 머신이 통신이 가능하도록 설정

- kubeadm을 이용한 클러스터 구축
Master Node
	Core가 2개 이상이어야 합니다.
	포트 개방
		API Server: 6443
		etcd Server: 2379, 2380
		kubelet API: 10250
		kube scheduler: 10251
		kube controller manager: 10252
		Flannel CNI 플러그인: 8285, 8472(CNI 플러그 인에 따라 다르게 설정)

Worker Node
	하드웨어 제약은 없음
	포트 개방
		API Server: 6443
		kube-proxy가 서비스를 Load Balancing 하기 위해서 사용하는 포트: 26443
		Flannel CNI 플러그인: 8285, 8472(CNI 플러그 인에 따라 다르게 설정)
		NodePort로 사용할 포트: 30000~32767


- 모든 머신에 공통된 설치
패키지 정보 업데이트: sudo apt update && sudo apt upgrade -y

인증서 패키지 설치: sudo apt install -y curl apt-transport-https ca-certificates software-properties-common gnupg

가상 메모리 사용을 해제 
	sudo swapoff -a
	sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

	확인: sudo free -m 

iptable 설정

	cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
	overlay
	br_netfilter
	EOF

	sudo modprobe overlay

	sudo modprobe br_netfilter

	cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF


	sudo sysctl --system


컨테이너 런타임 설치
	sudo apt install -y containerd

	sudo mkdir -p /etc/containerd

	containerd config default | sudo tee /etc/containerd/config.toml

systemd를 cgroup으로 전환
	sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
	
	sudo systemctl restart containerd

	sudo systemctl enable containerd

쿠버네티스 설치
	sudo curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

	echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list

	sudo apt update

	sudo apt install -y kubelet kubeadm kubectl

	sudo apt-mark hold kubelet kubeadm kubectl #패키지를 업그레드하더라도 3가지는 그대로 유지(쿠버네티스 버전 유지)

클러스터 구성
	Master에서 수행하는 작업
		클러스터 초기화
			sudo kubeadm init --pod-network-cidr=10.244.0.0/16
			#설치되는 CNI에 따라서 cidr 값은 변경됩니다.
			#이 값은 flannel을 사용할 때 값입니다.
			#결과로 kubeadm join 문구가 출력되는데 Worker에서는 이 구문을 수행해야 클러스터에 참여됩니다.

			mkdir -p $HOME/.kube
			
			sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
			
			sudo chown $(id -u):$(id -g) $HOME/.kube/config
		
		CNI 설치
			kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml


	Worker 수행해야 하는 작업 - Master 작업이 끝나야 가능
		sudo kubeadm join 10.0.2.101:6443 --token 58ds6a.jiiotw0j4j1ew9td \
        --discovery-token-ca-cert-hash sha256:c160d8f52de3dbca2382cb7099ed137b11ff4eddaf0ad8501c6c5692e0774958
	

	확인: master에서 kubectl get nodes 를 확인해서 노드들이 참여했는지 확인


#### 외부 관리형 쿠버네티스: CSP에서 제공하는 Kubernetes 환경
- AWS의 EKS
- MS에서는 AKS
- Google에서는 GKE
- NHN의 NKS
- Kakao의 DKOS
- SUSE의 Rancher 나 Redhat의 Openshift 와 같은 플랫폼에서 제공하는 설치형 Kubernetes를 사용할 수 있음

### 6)Kubernetes Component
#### 개요
- Master Node에는 Cluster를 유지하고 제어하기 위한 다양한 Component가 있고 Worker Node에는 애플리케이션을 실행하기 위한 Component 들이 있음

- Master Node
etcd
API Server
Controller Manager
Scheduler

- Worker Node
Proxy
kubelet
Container Runtime

#### kubectl
- 쿠버네티스 클러스터에 명령을 내리는 역할
- 다른 구성 요소들과 달리 바로 실행되는 명령 형태인 binary로 배포되기 때문에 Master Node에 있을 필요는 없지만 API Server 와 통신을 하기 때문에 마스터 노드에 구성하는 것을 권장

#### API Server
- Kubernetes Cluster의 API를 사용할 수 있게 해주는 프로세스로 Cluster로 요청이 들어왔을 때 그 요청이 유효한지 검증
- 명령이 들어오면 사용자 인증 과 명령어 검증을 한 후 명령을 Worker Node에 전달을 수행
파드를 생성하라는 명령이 오면 워커 노드에 파드를 생성하라고 지시를 한 상태: 생성은 아직 되지 않음

#### etcd
- Cluster에 필요한 Pod와 같은 리소스들의 상태 정보를 저장한 데이터베이스
- API Server는 Pod를 만든다는 사실을 etcd에 알려서 상태를 저장하고 사용자에게는 pod가 생성되었다고 알림
- 정보들은 key-value 형태로 저장되는데 사용자에게 pod가 생성되었다고 알렸지만 내부적으로 pod가 생성되지 않음
- etcd를 여러 곳에 저장해두면 장애가 나더라도 시스템의 가용성을 확보할 수 있음 - Multi Master Node

#### scheduler
- Pod를 어떤 노드에 할당해야 하는지를 결정하는데 Worker Node의 리소스 사용량(CPU 나 메모리 사용량)이나 레이블 그리고 노드의 상태 등 을 참조해서 결정

#### Controller Manager
- 클러스터의 상태를 지속적으로 감시하고 선언된 상태와 실제 상태를 일치시키기 위해 다양한 컨트롤러들을 실행하는 핵심 컴포넌트
- 역할
클러스터 내 리소스를 지속적으로 모니터링

선언된 상태와 실제 상태를 비교하여 필요한 변경 수행

컨트롤러들을 관리하고 실행

장애가 발생하면 자동으로 복구 작업 수행
 
다양한 Component의 상태를 지속적으로 모니터링하고 실행 상태를 유지하는 역할을 한느데 Controller Manager 가 Worker Node 와 통신이 불가능하다고 여기면 Worker Node의 Pod를 삭제하고 다른 Worker Node에 Pod를 생성하도록 하는 역할

#### kubelet
- API Server는 Pod가 생성될 Worker Node에 있는 kubelet에게 Pod의 생성 정보를 전달
- 전달받은 정보를 이용해서 Pod를 생성하는데 kubelet은 Cluster 의 각 노드에서 실행되는 에이전트로 Pod에서 Container의 동작을 관리
- Pod를 생성하면 kubelet은 API Server에 정보를 전달하고 API Server는 etcd를 업데이트하고 이 정보를 etcd에 저장해서 어떤 노드에 어떤 pod가 존재하는지 할 수 있습니다.

#### kube-proxy
- 클러스터 내에서 네트워크 프록시의 역할을 하는 컴포넌트로 Service 와 Pod 간의 트래픽을 라우팅하는 역할을 수행
- 역할
클러스터 네트워크 트래픽 관리
	쿠버네티스의 서비스 개념을 구현하기 위해 각 노드에서 실행됨
	클러스터 내부에서 서비스 와 파드 간 트래픽을 적절히 분배
서비스 IP 와 포트 포워딩
	쿠버네티스 서비스가 가상 IP(Cluster IP)를 사용해도 실제 파드로 트래픽이 전달되도록 합니다.
	iptables, ipvs, userspace 모드를 활용해서 트래픽을 제어
- 실행 방식
클러스터의 각 노드에서 DaemonSet(모든 노드에 배치되는 Pod)으로 실행	
kubectl get pods -n kube-system 명령으로 확인 가능

- iptables
각 노드에서 iptables 규칙을 생성해서 패킷을 올바른 파드로 전달
빠르고 효율적 - 커널 레벨에서 처리
수 천개의 서비스가 존재하는 경우 규칙이 많아져 성능 저하 우려

- ipvs
ipvsadm을 사용해서 커널 공간에서 패킷을 처리
고성능, 대규모 트래픽 처리에 적합
L4 Load Balancing도 지원
ipvs 커널 모듈이 필요

- userpace 모드(비추천)
트래픽을 kube-proxy 프로세스를 거쳐 전달
성능이 낮아서 현재는 거의 사용하지 않음 

#### Container Runtime
- Container 실행을 담당
- Kubernetes는 다양한 종류의 Runtime을 지원하는데 Containerd, CRI-O, Docker(Kubernetes 1.20 버전부터 Docker는 지원 중단) 등

#### Kubernetes Controller
- ReplicaSet
몇 개의 Pod를 유지할 지 결정하는 Controller
ReplicaSet이 관리하는 동일한 구성의 Pod를 Replica라고 부름

- Deployment
Kubernetes에서 sateless 애플리케이션을 배포할 때 사용되는 가장 기본적인 Controller
ReplicaSet의 상위 개념

container <- Pod <- Replicaset <- Deployment

- DaemonSet
모든 Node에 Pod를 배포하고 관리하는 Controller
모든 Node에 배치되어야 하는 성능 수집 및 로그 수집같은 작업에 사용

taint 와 toleration
	kubernetes Cluster를 운영하다 보면 특정  Worker Node에는 특정 성격의 Pod만 배포하고 싶을 때가 있는데 특정 성격의 pod만 배치하는 것을 taint 라고 하고 toleration은 모든 모드는 전부 배치
	Cluster를 구성을 할 때 비용 상의 문제로 GPU가 있는 Node 와 GPU가 없는 Node가 있을 때 GPU를 사용해야 하는 Pod는 GPU가 있는 노드에게만 배포하는 것이 taint 

- StatefulSet
애플리케이션의 상태를 관리하는데 사용하는 Pod의 집합
상태를 저장할 수 있기 때문에 Pod 들의 순서 및 고유성을 보장
Depoyment 와 유사하지만 동일한 스펙으로 Pod를 만들지만 서로 교체는 불가능
재스케쥴링을 해도 지속적으로 유지되는 식별자를 가짐
Storage Volume을 사용해서 워크로드에 지속성을 제공하려는 경우 사용 가능

- Job
하나 이상의 Pod를 지정하고 지정된 수의 Pod가 성공적으로 실행되도록 해주는 Controller

노드의 하드웨어 장애나 재부팅 등으로 Pod가 비정상적으로 작동하면 다른 노드에서 Pod를 시작해 서비스가 지속되도록 함 

- CronJob
Job의 일종으로 특정 시간에 특정 Pod를 실행시키는 것과 같이 지정한 일정에 따라서 Job을 실행시킬 때 사용

애플리케이션 프로그램, 데이터베이스 등에서 데이터를 주기적으로 백업하고자 할 때 많이 사용

### 7)Kubernetes 의 통신
#### Kubernetes 의 Service
- 개요
pod는 Kubernetes Cluster 안에서 옮겨다니는 특성이 있는데 Pod가 실행 중인 Worker Node에 문제가 생기면 Worker Node에서 Pod가 다시 생성될 수 도 있고 다른 Worker Node에서 생성될 수 있는데 이 때 Pod의 IP는 변경되기도 함

동적으로 변하는 Pod에 고정된 방법으로 접근하기 위해서 사용하는 것이 Service

Service를 사용하면 Pod가 Cluster 내의 어떤 Worker Node에 존재하든지 고정된 주소를 이용해서 접근이 가능하고 Cluster 외부에서 Pod에 접근하는 것 도 가능

하나의 서비스가 관리하는 Pod는 모두 동일한 구성을 갖는데 구성이 다른 Pod는 별도의 Service로 관리

서비스의 역할은 Load Balancer로 각 서비스는 자동적으로 고정된 IP 주소를 설정해서 이 주소로 들어오는 통신을 처리하는 객체

내부적으로 동일한 구성을 가진 Pod가 여러 개 있어도 하나의 IP 주소만 볼 수 있으며 이 주소로 접근하면 서비스가 통신을 적절히 분해주는 구조

서비스가 분배하는 통신은 한 Worker Node 안으로 국한되며 여러 Worker Node 간의 분배는 Load Balancer 나 Ingress가 담당하는데 이들은  Master Node 도 Worker Node도 아닌 별도의 노드에서 동작을 하거나 물리적 전용 하드웨어

- 종류
Cluster IP: Cluster 내의 Pod 들은 기본적으로 외부에서 접근할 수 있는 IP를 할당받지만 같은 Cluster 내부에서 접근할 방법을 제공해주어야 하는데 그것이 Cluster IP

Node Port: 서비스를 외부(클러스터 외부)로 노출할 때 사용하는 것으로 Worker Node의 IP 와 Node Port를 이용해서 노출하는데 이렇게 하면 Node의IP:NodePort를 이용해서 클러스터 외부에서 접근이 가능

Load Balancer: 클러스터 외부에 설정해서 Load Balancing 기능까지 이용하고자 하는 경우 사용

Ingress: URI, Hostname, Path 등과 같은 웹 개념을 이해하는 프로토콜로 인지형 설정 매커니즘을 이용하여 HTTP 나 HTTPS Network  서비스를 사용가능하게 해주는 것으로 하나의 트래픽을 서로 다른 Service에 매핑할 수 있게 해줍니다.

External Name: 외부의 특정 FQDN(Fully Qualified Domain Name - 완전한 도메인 이름)에 대한 CNAME(Canonical NAME- 도메인 이름을 다른 도메인 이름으로 매핑하는 역할) 매핑을 제공하는 것으로 Pod가 CNAME을 이용해서 특정 FQDN 과 통신하기 위함

#### Kubernetes에서 통신할 때 나타나는 특징
- Pod가 사용하는 Network 와 Node가 사용하는 Network가 다름: Node는 물리적 네트워크를 사용하고 Pod는 가상의 Network를 사용합니다.
- Pod를 생성하면 동일한 Worker Node에 존재하는 Pod끼리는 통신이 가능하지만 외부 와 의 통신은 불가능
Pod의 IP는 Worker Node에서 할당
- 하나의 Pod 내에 있는 Container끼리는 모두 동일한 IP를 사용하기 때문에 localhost로 통신이 가능
- 다른 노드의 Pod와 통신을 하기 위해서는 CNI(Container Network Interface)가 필요
- CNI가 Overlay Network를 구성
- 물리적인 네트워크 위에 가상의 Network를 구성
- 오버레이 네트워크는 모든 Worker Node의 물리적 네트워크를 포함
- 쿠버네티스에서 사용 가능한 CNI 플러그인
Flannel
Calico
Cilium
Multus: 다중 Network 지원을 위한 플러그인

#### Pod 와 Service 간 통신
- Service의 특성
Service도 하나의 IP를 갖음

Pod 와 Service에서 사용하는 IP 대역은 다름

Pod의 IP 대역은 10.244.1.x 이지만 Service 의 IP 대역은 10.101.X.X 

Service에서 사용하는 가상 Network는 ifconfig 나 Routing Table에서 확인이 안됨

- 외부에서 Service Name으로 HTTP 요청을 하면 Cluster DNS 서버에서 서비스 이름에 대항하는 IP를 찾아서 Client Pod에 전달하고 자신의 라우팅 테이블에 값이 있으면 처리하고 값이 없으면 다른 Client Pod에 전달합니다.
이 기능을 수행하기 위한 커널의 기능이 netfilter 와 iptables

#### 외부와 통신할 수 있게 해주는 서비스
-Node Port
-Load Balancer
-Ingress

