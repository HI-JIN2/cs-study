**Kubernetes**

컴퓨터를 재시작했을 때 kubectl 명령이 안되는 경우는 대부분 메모리 스왑을 다시 사용하도록 설정되서 입니다.
이 경우에는 `sudo swapoff -a` 명령을 다시 수행해주세요

# 1. Workload API

## 1) CronJob

### 개요
- 특정한 주기를 가지고 Command를 실행하기 위한 워크로드 API
- Job은 한 번만 수행하기 위한 Command를 실행하기 위한 워크로드 API

### busybox 이미지를 이용해서 Pod를 생성하고 생성된 Pod는 1분 단위로 date; echo Hello from Kubernetes cluster 라는 메시지를 출력하도록 수행
- 매니페스트 작성: `cronjob.yaml`

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubenetes cluster
          restartPolicy: OnFailure
```

- 리소스 생성: `kubectl apply -f cronjob.yaml`

- 생성된 파드 확인: `kubectl get pods`
```bash
NAME                   READY   STATUS      RESTARTS   AGE
hello-29524348-wglvg   0/1     Completed   0          8s
```

- 로그 확인: `kubectl logs hello-29524348-wglvg`
```bash
NAME                   READY   STATUS      RESTARTS   AGE
```

- 파드를 다시 확인해보면 오래된 파드를 삭제하고 새로운 파드를 만들어서 작업을 수행
- 기본적으로 3개의 파드만 유지

### 크론잡 작업
- 삭제: `kubectl delete cronjob 이름`
- 일시 정지: `kubectl patch cronjob 이름 -p '{"spec":{"suspend":true}}'`
- 다시 시작: `kubectl patch cronjob 이름 -p '{"spec":{"suspend":false}}'`
 
### 동시 실행 제어
- 크론잡에서는 잡을 생성하는 특성 상 동시 실행에 대한 정책을 설정할 수 있음
- `spec.concurrencypolicy`에 설정
  - **Allow**: 실행 제한을 두지 않음
  - **Forbid**: 이전 잡이 종료되지 않으면 작업을 수행하지 않음
  - **Replace**: 이전 잡을 취소하고 새로운 잡을 실행

## 2) node

### 노드 자원 보호
#### 개요
노드는 쿠버네티스 스케쥴러에서 파드를 할당하고 처리하는 역할을 수행

최근에 몇 차례 문제가 생긴 노드에 파드를 할당하면 문제가 생길 가능성이 높은데 이런 경우 노드를 제거하면 좋지만 해당 노드를 반드시 사용해야 하는 경우라면 이 노드를 제외한 다른 노드에 새로 생기는 파드를 배치해야 합니다.

#### 실습
pod를 배포하는 deployment를 위한 yaml 작성: `sample-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: sample-app
  template:
    metadata:
      labels:
        app: sample-app
    spec:
      containers:
      - name: nginx-container
        image: nginx	
```

- 배포: `kubectl apply -f sample-deployment.yaml`
- 파드 확인: `kubectl get pods -o wide`
  - worker1 과 worker2에 하나씩 배치됨
- 파드의 개수를 4개로 증가: `kubectl scale deployment sample-deployment --replicas=4`
- 파드 확인: `kubectl get pods -o wide`
  - worker1 과 worker2에 두개씩 배치됨
- 파드의 개수를 2개로 감소: `kubectl scale deployment sample-deployment --replicas=2`
- 파드 확인: `kubectl get pods -o wide`
  - worker1 과 worker2에 하나씩 배치됨
- worker1에 더 이상 파드가 배치되지 않도록 설정(현재 상태 보존): `kubectl cordon worker1`
- 파드의 개수를 4개로 증가: `kubectl scale deployment sample-deployment --replicas=4`
- 파드 확인: `kubectl get pods -o wide`
  - worker1은 그대로 하나의 파드만 존재하고 worker2에 두개가 추가되서 3개의 파드가 배치됨
- 2개로 축소: `kubectl scale deployment sample-deployment --replicas=2`
- worker1에 파드를 다시 배치할 수 있도록 수정: `kubectl uncordon worker1`

### 노드의 모든 파드를 다른 노드로 이동: drain
- DaemonSet은 drain을 하더라도 계속 남아있게 됩니다.
- DaemonSet도 drain을 시키고자 하면 그 때는 `--ignore-daemonsets` 옵션을 추가
- 파드를 배치: `kubectl apply -f sample-deployment.yaml`
- 확인: `kubectl get pods -o wide`
- 파드의 개수를 4개로 증가: `kubectl scale deployment sample-deployment --replicas=4`
- worker1의 모든 파드를 다른 노드로 이동: `kubectl drain worker1 --ignore-daemonsets`
- 확인: `kubectl get pods -o wide`
- 해제: `kubectl uncordon worker1`

# 2. Service API

## 1) 개요
- 서비스 API 카테고리로 분류된 리소스는 클러스터 상의 컨테이너에 대한 End Point를 제공하거나 레이블과 일치하는 컨테이너의 디스커버리에 사용되는 리소스
- 내부적으로 사용되는 리소스를 제외하고 사용자가 직접 사용하는 것은 L4 LoadBalancing을 제공하는 서비스 리소스 와 L7 LoadBalancing을 제공하는 인그레스 리소스가 있음

## 2) 종류
### Service
- ClusterIP
- ExternalIP
- NodePort
- LoadBalancer
- Headless
- ExternalName
- None-Selector

### Ingress

## 3) 쿠버네티스 클러스터 네트워크 와 서비스
### 동일한 파드 내의 컨테이너 간 통신
- 같은 파드 내에 있는 컨테이너는 동일한 IP 주소를 할당받게 되므로 같은 파드의 컨테이너로 통신하려면 localhost로 통신

### 다른 파드의 컨테이너 와 통신
- 파드의 IP를 이용해서 통신

### 쿠버네티스 클러스터의 내부 네트워크
- 쿠버네티스 클러스터는 클러스터를 생성하면 노드 상에 파드를 위한 내부 네트워크가 자동으로 구성됨
- 내부 네트워크 구성은 사용하는 CNI(Container Network Interface) 라는 플러그 형 모듈 구현에 따라 다르지만 기본적으로 노드 별로 다른 네트워크 세스먼트를 구성하고 노든 간의 트래픽은 VXLAN 이나 L2 Routing 등의 기술을 사용하여 전송함으로써 노드 간 통신이 가능하게 구성
- 노드 별 네트워크 세그먼트는 쿠버네티스 클러스터 전체에 할당된 네트워크 세그먼드를 자동으로 분할해 할당하므로 사용자가 설정하지 않아도 됩니다.
- 파드 간의 통신은 서비스를 사용하지 않고 가능하지만 서비스를 사용했을 때 얻을 수 있는 장점
  - LoadBalancing
  - Service Discovery 와 Cluster 내부 DNS

### 파드에 대한 트래픽 LoadBalancing: ClusterIP를 이용한 파드간 통신
- nginx를 이용한 파드를 생성하기 위한 `sample-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: sample-app
  template:
    metadata:
      labels:
        app: sample-app
    spec:
      containers:
      - name: nginx-container
        image: amsy810/echo-nginx:v2.0
```

- 배포: `kubectl apply -f sample-deployment.yaml`
- 파드의 IP 확인: `kubectl get pods -o wide`
- ClusterIP를 할당하는 서비스 생성: `sample-clusterip.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: sample-clusterip
spec:
  type: ClusterIP
  ports:
  - name: "http-port"
    protocol: "TCP"
    port: 8080
    targetPort: 80
  selector:
    app: sample-app
```

- 리소스 배포: `kubectl apply -f sample-clusterip.yaml`
- 서비스 확인: `kubectl get services`
```bash
sample-clusterip   ClusterIP   10.102.57.238   <none>        8080/TCP   12s
```
- 상세히 확인: `kubectl describe services sample-clusterip`
  - Selector 부분 과 EndPoints 부분을 확인
  - Selector는 연결된 Pod의 레이블이 되고 EndPoints는 실제로 접근할 애플리케이션 EndPoint
  - 클러스터 내에서 10.102.57.238:8080 으로 요청을 전송하면 실제 처리는 10.244.1.41:80, 10.244.2.120:80, 10.244.2.121:80 에 LoadBalancing 되서 이루어집니다.
- 확인(여러번 수행 - 뒤의 IP는 자신이 만든 Service 의 ClusterIP를 지정)
```bash
kubectl run --image=amsy810/tools:v2.0 --restart=Never --rm -i testpod --command -- curl -s http://10.102.57.238:8080 
```

### 파드에 대한 트래픽 LoadBalancing: 여러 포트 할당
- 하나의 서비스에 여러 포트를 할당할 수 있음
- http 와 https 에서 ClusterIP가 다르면 불편하므로 하나의 서비스에 여러 포트를 가질 수 있도록 설정하는 것이 바람직

```yaml
apiVersion: v1
kind: Service
metadata:
  name: sample-clusterip
spec:
  type: ClusterIP
  ports:
  - name: "http-port"
    protocol: "TCP"
    port: 8080
    targetPort: 80
  - name: "https-port"
    protocol: "TCP"
    port: 8443
    targetPort: 443
  selector:
    app: sample-app
```

### 파드에 대한 트래픽 로드밸런싱: name을 이용한 포트 설정
- 디플로이먼트 수정: `sample-deployment` 수정
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: sample-app
  template:
    metadata:
      labels:
        app: sample-app
    spec:
      containers:
      - name: nginx-container
        image: amsy810/echo-nginx:v2.0
        ports:
        - name: http
          containerPort: 80
```

- service 파일 수정
```yaml
apiVersion: v1
kind: Service
metadata:
  name: sample-clusterip
spec:
  type: ClusterIP
  ports:
  - name: "http-port"
    protocol: "TCP"
    port: 8080
    targetPort: http
  selector:
    app: sample-app
```

### 서비스 디스커버리
- DNS A 레코드를 사용한 서비스 디스커버리
- 다른 파드에서 서비스로 할당되는 End Point에 접속할려면 당연히 목적지가 필요하지만 할당된 IP 주소를 사용하는 방법 외에도 자동 등록된 DNS 레코드를 사용할 수 있음
- 쿠버네티스에서 IP 주소를 편하게 관리하려면 기본적으로 자동 할당된 IP 주소에 연결된 DNS 이름을 사용하는 것이 바람직
- 할당되는 IP 주소를 서비스를 생성할 때 마다 변경되므로 IP 주소를 송신 측 컨테이너 설정 파일 등에서 명시적으로 설정하면 변경될 때 마다 설정 파일을 등을 변경해야 하기 때문에 컨테이너를 변경 불가능한 상태로 유지할 수 없어서 추천하지 않음
- 서비스 이름을 이용해서 통신을 하면 이런 문제는 해결
```bash
kubectl run --image=amsy810/tools:v2.0 --restart=Never --rm -i testpod --command -- curl -s http://sample-clusterip:8080
```
- 서비스 이름을 이용해서 통신하면 서비스를 삭제하고 다시 생성하더라도 통신하는데는 아무런 문제가 발생하지 않음
- FQDN(정식 명칭): `서비스이름.네임스페이스이름.svc.cluster.local`
- FQDN 확인:
```bash
kubectl run --image=amsy810/tools:v2.0 --restart=Never --rm -i testpod --command -- dig sample-clusterip.default.svc.cluster.local
kubectl run --image=amsy810/tools:v2.0 --restart=Never --rm -i testpod --command -- cat /etc/resolv.conf
```

## 4) ClusterIP Service
### 개요
- 쿠버네티스에서 가장 기본이 되는 서비스
- `type: ClusterIP` 서비스를 생성하면 쿠버네티스 클러스터 내에서만 통신 가능한 Internal Network에 생성되는 가상 IP가 할당됨
- ClusterIP 와 통신은 각 노드 상에서 실행 중인 시스템 구성 요소 kube-proxy가 파드로 전송
- ClusterIP는 쿠버네티스 클러스터 외부에서 트래픽을 수신할 필요가 없는 환경에서 클러스터 내부 LoadBalancer로 사용
- 기본적으로는 Kubernetes API Server에 접속하기 위한 Kubernetes 서비스가 생성되어 있고 ClusterIP가 할당되어 있음

### 생성
- `type: ClusterIP`를 지정
- `spec.ports[].port`에는 ClusterIP에서 수신할 포트 번호를 지정하고 `spec.ports[].targetPort`는 목적지 컨테이너 포트 번호를 지정

### 가상 IP를 정적 지정
- 애플리케이션에서 데이터베이스 서버를 지정하는 경우에는 기본적으로 쿠버네티스 서비스에 등록된 클러스터 내부 DNS 레코드를 사용하여 호스트를 지정하는 것이 바람직
- IP 주소로 지정하고자 하는 경우 ClusterIP를 정적으로 지정하는 것이 가능
- ClusterIP를 정적으로 지정하고자 하는 경우는 `spec.clusterIP`를 지정
- `sample-clusterip.yaml` 파일을 수정

```yaml
apiVersion: v1
kind: Service
metadata:
  name: sample-clusterip
spec:
  type: ClusterIP
  clusterIP: 10.101.106.108
  ports:
  - name: "http-port"
    protocol: "TCP"
    port: 8080
    targetPort: http
  selector:
    app: sample-app
```

- IP는 변경할 수 없는 항목이라서 기본 서비스를 삭제: `kubectl delete service sample-clusterip`
- 리소스 생성: `kubectl apply -f sample-clusterip.yaml`
- 리소스 확인(IP를 확인): `kubectl get services`

## 5) ExternalIP Service
### 개요
- 특정 쿠버네티스 노드 IP:포트 에서 수신한 트래픽을 컨테이너로 전송하는 형태로 외부와 통신할 수 있도록 해주는 서비스

### 워커 노드의 IP 주소를 확인: `kubectl get nodes -o wide`
- master: 10.0.2.101
- worker1: 10.0.2.102
- worker2: 10.0.2.103

### ExternalIP 서비스에 설정
- `spec.externalIPs`: 쿠버네티스 노드 IP 주소
- `spec.ports[].port`: ExternalIP 와 ClusterIp에서 수신할 포트 번호
- `spec.ports[].targetPort`: 목적지 컨테이너 포트 번호

### 매니페스트 작성: `sample-externalip.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: sample-externalip
spec:
  type: ClusterIP
  externalIPs:
  - 10.0.2.102
  - 10.0.2.103
  ports:
  - name: "http-port"
    protocol: "TCP"
    port: 8080
    targetPort: 80
  selector:
    app: sample-app
```

### 리소스 배포: `kubectl apply -f sample-externalip.yaml`
```bash
sample-externalip   ClusterIP   10.97.31.207     10.0.2.102,10.0.2.103   8080/TCP   6s
```

### 외부에서 ExternalIP를 이용해서 접근이 가능
```bash
curl 10.0.2.102:8080
curl 10.0.2.103:8080
```

### NodePort 와 유사해 보임
- 외부에서 Node의 IP 통해 접근

### 클러스터 내부에서 확인하면 클러스터 내부 DNS에서 반환하는 서비스 디스커버리의 IP 주소는 ExternalIP 가 아닌 ClusterIP
```bash
kubectl run --image=amsy810/tools:v2.0 --restart=Never --rm -i testpod --command -- dig sample-externalip.default.svc.cluster.local
```

## 6) NodePort
### 개요
- NodePort 서비스는 ExternalIP 서비스와 유사
- ExternalIp는 지정한 쿠버네티스 노드의 IP주소:포트 에서 수신한 트래픽을 컨테이너에 전송하는 형태로 외부와 통신할 수 있는데 NodePort는 모든 쿠버네티스 노드의 IP:포트에서 수신한 트래픽을 컨테이너에 전송하는 형태로 외부 와 통신
- ExternalIP는 등록한 IP의 노드만 참여하지만 NodePort는 모든 노드가 참여를 한다는 것이 다름
- ExternalIP는 등록한 Node의 IP에 바인딩 되지만 NodePort는 0.0.0.0:포트 로 바인딩되는 형태
- 이번에는 `nodePort`를 등록할 수 있는데 30000~32767 사이의 포트를 등록할 수 있으며 생략하면 임의의 포트가 등록됩니다. 

### 서비스 작성: `sample-nodeport.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: sample-nodeport
spec:
  type: NodePort
  ports:
  - name: "http-port"
    protocol: "TCP"
    port: 8080
    targetPort: 80
    nodePort: 30080
  selector:
    app: sample-app
```

- 기존 서비스 모두 제거: `kubectl delete service --all`
- 서비스 배포: `kubectl apply -f sample-nodeport.yaml`
- 확인: `kubectl get services`
```bash
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP             PORT(S)          AGE
sample-nodeport     NodePort    10.110.40.120    <none>                  8080:30080/TCP   16s
```
- 클러스터 IP가 자동 생성

### 외부에서 접속할 때는 nodeport를 이용: 클러스터 내부의 어떤 노드의 IP로도 접속이 가능(내부에서 ClusterIP를 가지고 LoadBalancing을 수행합니다.)
```bash
curl 10.0.2.102:8080
```

### nodeport를 실행하면 모든 node의 nodeport 가 전부 개방됩니다.
- 요청이 오면 자신이 처리할 수 있으면 자신이 처리하고 자신이 처리할 수 없으면 다른 노드에게 처리해달라고 요청을 하는 형태로 동작하기 때문에 실제 파드가 처리할 수 있는 경우에 처리를 수행합니다.

### ExternalIP 와 NodePort를 이용하면 클러스터 외부에서 클러스터 내부로 트래픽 전달이 가능하면 내부에서는 ClusterIP가 LoadBalancing을 수행합니다.
