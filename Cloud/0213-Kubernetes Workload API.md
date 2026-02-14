# Kubernetes
## 1.Workload API
### 1)개요
#### 클러스터에 컨테이너를 기동시키기 위해 사용되는 리소스
#### 종류
- Pod
- ReplicaController
- ReplicaSet
- Deployment
- DaemonSet
- StatefulSet
- Job
- CronJob
#### 관계
파드 <-> ReplicationControler
파드 <-> ReplicaSet <-> Deployment
파드 <-> DaemonSet
파드 <-> StatefulSet
파드 <-> Job <-> CronJob

### 2)Pod
#### 개요
- 쿠버네티스의 배포 최소 단위
- 하나의 Pod는 하나 이상의 컨테이너로 구성되며 하나의 파드에 여러 개의 컨테이너를 포함시킬 수 있는데 이러한 구조를 Sidecar 패턴이라고 합니다.
- 같은 파드에 포함된 컨테이너끼리는 네트워크 적으로 격리되어 있지 않고 IP 주소를 공유
이런 경우는 localhost를 이용해서 통신이 가능

#### 파드의 디자인 패턴
- Sidecar Pattern
메인 컨테이너 와 보조적인 기능을 갖추는 서브 컨테이너를 포함하는 패턴

특정 변경 사항을 감지해서 동적으로 설정을 변경하는 컨테이너 그리고 깃 저장소 와 로컬 스토리지를 동기화하는 컨테이너 와 애플리케이션의 로그 파일을 오브젝트 스토리지로 전송하는 컨테이너 등의 구성이 자주 사용됨

- Ambassador Pattern
메인 컨테이너가 외부 시스템과 접속할 때 대리로 중계해주는 서브 컨테이너를 포함하는 패턴

파드에 두 개의 컨테이너가 있어서 메인 컨테이너에서는 목적지에 localhost를 지정해서 앰버서더 컨테이너로 접속

외부 환경이 자주 변경되는 경우 메인 컨테이너를 수정하지 않고 적용하기 위해서 사용

- Adapter Pattern
서로 다른 데이터 형식을 변환해주는 컨테이너를 포함하는 패턴

프로메테우스 등의 모니터링 소프트웨어는 정의된 형식으로 메트릭을 수집해야 하는데 대부분의 미들웨어가 메트릭 출력 형식을 지원하지 않기 때문에 어댑터 컨테이너를 사용해서 외부 요청에 맞게 데이터 형식을 변환하고 데이터를 반환해서 사용

#### 컨테이너 실행 원리
- 쿠버네티스는 직접 컨테이너를 실행하지는 않음
- 컨테이너를 생성할 책임을 해당 노드에 설치된 컨테이너 런타임에 맡기는 형태
- 컨테이너 런타임으로 containerd 나 docker를 사용
- 파드는 쿠버네티스가 관리하는 리소스고 컨테이너는 쿠버네티스 외부에서 관리

#### Stateless & Stateful
- Stateless: 사용자가 애플리케이션을 사용할 때 상태나 세션을 저장해 둘 필요가 없는 애플리케이션에서 사용
- Stateful: 사용자가 애플리케이션을 사용할 때 상태나 세션을 별도의 데이터베이스에 저장해야 하는 애플리케이션에서 사용

#### 두 개의 컨테이너를 갖는 Pod를 생성
- sample-2pod.yaml 파일을 생성
apiVersion: v1
kind: Pod
metadata:
  name: sample-2pod
spec:
  containers:
  - name: nginx-container
    image: nginx
  - name: redis-container
    image: redis:3.2

- 파드 생성
kubectl apply -f sample-2pod.yaml

- 파드 확인
kubectl get pods

#### 2개의 컨테이너를 갖는 파드를 생성
- sample-2pod-fail.yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample-2pod-fail
spec:
  containers:
  - name: nginx-container-112
    image: nginx:1.17
  - name: nginx-container-113
    image: nginx

- 매니페스트를 실행: kubectl apply -f sample-2pod-fail.yaml

- 파드를 확인: kubectl get pods
NAME               READY   STATUS             RESTARTS        AGE
sample-2pod        2/2     Running            0               15h
sample-2pod-fail   1/2     CrashLoopBackOff   5 (2m49s ago)   6m17s
sample-pod         1/1     Running            1 (17h ago)     17h

sample-2pod-fail 파드는 2개의 컨테이너 중 하나만 생성하고 실패

- 컨테이너 로그 확인: kubectl logs sample-2pod-fail -c nginx-container-113
포트가 이미 사용 중이라서 에러
도커는 전체를 하나의 IP로 묶고 각 컨테이너에 포트를 할당해서 실행하는데 쿠버네티스는 파드 단위로 IP를 할당하고 각 컨테이너를 포트를 할당해서 실행

#### 파드의 컨테이너에 접속
- 파드가 컨테이너를 하나만 가진 경우
kubectl exec -it sample-pod -- /bin/bash

- 파드가 2개인 경우: 컨테이너를 지정하지 않으면 하나의 컨테이너에 임의로 접속
kubectl exec -it sample-2pod -- /bin/bash

- 파드가 여러 개인 경우는 컨테이너를 지정해서 접속하는 것이 좋습니다.
kubectl exec -it sample-2pod -c nginx-container -- /bin/bash

#### ENTRYPOINT 명령과 CMD 명령 그리고 command/args
- Docker에서 ENTRYPOINT 와 CMD 는 컨테이너가 만들어 질 때 수행할 명령어를 기술하는 절 이었습니다.
보통 ENTRYPOINT에 명령어를 설정하고 CMD에 매개변수를 설정했습니다.
- 쿠버네티스에서는 ENTRYPOINT를 command로 CMD를 args로 부름
- 컨테이너를 실행할 때 이미지의 ENTRYPOINT 와 CMD를 덮어쓰기를 하려면 파드의 정의 내용 중 spec.containers[ ].command 와 spec.containers[ ].args를 지정
- sample-entrypoint.yaml 파일: 명령어를 사용 - /bin/sleep 3600 명령
apiVersion: v1
kind: Pod
metadata:
  name: sample-entrypoint
spec:
  containers:
  - name: nginx-container-112
    image: nginx:1.17
    command: ["/bin/sleep"]
    args: ["3600"]
 
#### 파드명 제한
- 이용 가능한 문자는 영문 소문자 와 숫자
- 이용 가능한 기호는 - 와 .
- 시작과 끝은 영문 소문자

#### 호스트의 네트워크 구성을 사용한 파드
- 쿠버네티스에 기동하는 파드에 할당된 IP 주소는 쿠버네티스 노드의 호스트 IP 주소와 범위가 달라 외부에서 볼 수 없는 IP 주소가 할당됨
- 호스트의 네트워크를 사용하는 설정(spec.hostNetwork)을 활성화하면 호스트 상에서 프로세스를 기동하는 것 과 같은 네트워크 구성으로 파드를 기동할 수 있음
- hostNetwork를 사용한 파드는 쿠버네티스 노드의 IP 주소를 사용하는 관계로 포트 번호 충돌을 방지하기 위해 기본적으로 사용하지 않고 사용할 때는 Edge 환경에서의 사용이나 호스트 측의 네트워크를 감시 또는 제어와 같은 특수한 애플리케이션에서만 사용

- pod 확인해서 NODE를 확인: worker1
kubectl get pod sample-pod -o wide

- worker1 노드에 대해서 자세히 확인
kubectl get node worker1 -o wide

- 현재는 파드의 IP 와 Node의 IP가 다름

- hostNetwork를 사용하는 Pod를 위한 매니페스트를 작성: sample-hostnetwork.yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample-hostnetwork
spec:
  hostNetwork: true
  containers:
  - name: nginx-container
    image: nginx

- 리소스 생성: kubectl apply -f sample-hostnetwork.yaml

- pod 확인해서 NODE를 확인: worker1
kubectl get pod sample-hostnetwork -o wide

- worker1 노드에 대해서 자세히 확인
kubectl get node worker1 -o wide

#### 파드 DNS 설정 과 서비스 디스커버리
- DNS 서버에 관한 설정은 파드 정의 spc.dnsPolicy에 설정
- 설정값
ClusterFirst(기본) 
	클러스터 내부 DNS를 사용해서 이름을 해석
	서비스 디스커버리나 클러스터 내부 로드 밸런싱에서 사용
	기본적으로 클러스터 내부의 DNS 서버에 질의를 하고 클러스터 내부 DNS에서 해석이 안되는 도메인에 대해서는 업스트림 DNS 서버에 질의

None
	클러스터 외부 DNS를 사용
	클러스터 내부 DNS를 사용하지 않음

Default
	쿠버네티스 노드의 DNS 설정을 그대로 상속받는 것

ClusterFirstWithHostNet
	hostNetwork를 사용한 파드에 클러스터 내부의 DNS를 참조하고 싶은 경우 사용

#### 정적 호스트 이름 설정(Linux에서 /etc/hosts 설정)
- spec.hostAliases 를 지정해서 사용

- sample-hostaliases.yaml 파일 작성
apiVersion: v1
kind: Pod
metadata:
  name: sample-hostaliases
spec:
  containers:
  - name: nginx-container
    image: nginx
  hostAliases:
  - ip: 8.8.8.8
    hostnames:
    - google-dns
    - google-public-dns

- 리소스 생성: kubectl apply -f sample-hostaliases.yaml

- 확인: kubectl exec -it sample-hostaliases -- cat /etc/hosts

#### 작업 디렉토리 설정
- 컨테이너 작업 디렉토리는 Dockerfile의 WORKDIR 명령 설정을 따르지만 spec.containers[].workingDir로 덮어쓸 수 있음
- 특정 스크립트 등이 배치된 볼륨을 파드에 마운트 할 때 그 스크립트가 배치된 디렉토리로 이동한 후 실행하고 싶은 경우가 있는데 이는 작업 디렉토리를 변경하는 기능을 사용해서 해결할 수 있음

- sample-workingdir.yaml 파일 작성
apiVersion: v1
kind: Pod
metadata:
  name: sample-workingdir
spec:
  containers:
  - name: nginx-container
    image: nginx
    workingDir: /tmp

- 리소스 생성: kubectl apply -f sample-workingdir.yaml

- 확인: kubectl exec -it sample-workingdir -- pwd
/tmp 가 출력됨

### 3)ReplicaSet/ReplicationController
#### 개요
- ReplicaSet은 일정한 개수의 Pod가 항상 실행되도록 관리하는데 이 기능때문에 서비스의 지속성이 유지됨
- 노드의 하드웨어에서 발생하는 장애 등의 이유로 Pod를 사용할 수 없을 때 다른 노드에서 Pod를 다시 생성해서 사용자에게 중단없는 서비스를 제공할 수 있음
- 원래 레플리카를 생성하는 리소스의 이름은 ReplicationController 였는데 ReplicaSet을 추가하고 일부 기능을 추가

#### 생성
- ReplicaSet을 사용하기 위한 yaml 파일을 작성: sample-rs.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: sample-rs
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
        image: nginx

- 리소스 생성: kubectl apply -f sample-rs.yaml

- 리소스 확인: kubectl get replicasets -o wide

- ReplicaSet이 만든 파드 목록 확인: 레이블을 이용
kubectl get pods -l app=sample-app -o wide
파드의 이름은 ReplicaSet이름-임의의문자열 형태로 설정

#### 파드 정지 와 자동화된 복구
- 레플리카 셋에서는 노드나 파드에 장애가 발생했을 때 지정한 파드 수를 유지하기 위해 다른 노드에서 파드를  기동시켜 주기 때문에 장애 시에도 많은 영향을 받지 않는데 이것이 쿠버네티스의 중요한 컨셉 중 하나인 자동화된 복구 기능

- 파드 삭제:  kubectl delete pods sample-rs-5sc4p

- 파드 확인: kubectl get pods -l app=sample-app -o wide
하나의 파드가 삭제되었으므로 하나가 만들어거나 이미 만들어 진 상태

- 증감 이력 확인: kubectl describe replicaset sample-rs

#### 레플리카셋 과 레이블
- 레플리카셋은 쿠버네티스가 파드를 모니터링하여 파드 수를 조정
- 모니터링은 특정 레이블을 가진 파드 수를 계산하는 형태로 이루어 짐
레플리카 수가 부족한 경우 매니페스트에 기술된 spec.template으로 파드를 생성하고 레플리카 수가 많을 경우 레이블이 일치하는 파드 중 하나를 삭제
- 레이블은 spec.selector 와 spec.tempate.metadata.labels 부분에 해당
- 2개의 레이블이 다르면 에러가 발생
- 2개의 레이블이 다은 yaml 파일: sample-rs-fail.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: sample-rs-fail
spec:
  replicas: 3
  selector:
    matchLabels:
      app: sample-app
  template:
    metadata:
      labels:
       app: sample-app-fail
    spec:
      containers:
      - name: nginx-container
        image: nginx

- 리소스 생성: kubectl apply -f sample-rs-fail.yaml
The ReplicaSet "sample-rs-fail" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"app":"sample-app-fail"}: `selector` does not match template `labels`

#### 스케일링
- 방법
매니페스트를 수정하여 kubectl apply -f 명령어를 수행
kubectl scale 명령어를 사용하여 스케일 관리

- 명령어를 이용하는 방식
kubectl scale replicaset sample-rs --replicas 5

#### 일치성 기준 조건 과 집합성 기준 조건
- 레플리카의 제어 조건은 서비스 중단 예정인 레플리케이션 컨트롤러의 일치성 기준 셀렉터였지만 레플리카셋에서는 좀 더 강화된 집합성 기준 셀렉터를 사용하여 유연한 제어도 가능

일치성 기준: 조건부에 일치 및 불일치 조건을 지정(=, !=)

집합성 기중: 조건부에 일치 및 불일치 조건 지정과 집합 조건(in, notin, exists) 지정 가능

쿠버네티스에서 어떤 조건을 지정할 때 이 두가지 방법이 있고 레플리카셋 외에 스케줄링할 때도 이 조건 지정이 가능

### 4)Deployment
#### 개요
- 여러 레플리카셋을 관리하여 롤링 업데이트나 롤백 등을 구현하는 리소스
- 디플로이먼트가 레플리카셋을 관리하고 레플리카셋이 파드를 관리하는 관계
- 디플로이먼트가 레플리카셋의 상위 개념으로서 Pod의 개수를 유지할 뿐 아니라 배포 작업을 좀 더 세분화해서 관리할 수 있음
- 신규 레플리카셋에 컨테이너가 기동되었는지 와 헬스 체크는 통과했는지를 확인하면 전환 작업이 진행되며 레플리카셋의 이행 과정에서 파드 수에 대한 상세한 지정도 가능
- 쿠버네티스에서 가장 권장하는 컨테이너 기동 방법
- 하나의 파드를 기동만 하는 경우에도 디플로이먼트 사용을 권장하는데 파드로만 배포한 경우는 파드에 장애가 발생하면 자동으로 파드가 생성되지 않으며 레플리카셋으로만 배포한 경우에는 롤링 업데이트 기능을 사요할 수 없음

#### Deployment 생성
- 매니페스트 파일 생성: sample-deployment.yaml
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
        image: nginx

- 리소스 생성: kubectl apply -f sample-deployment.yaml

- 확인
kubectl get deployments

kubectl get replicasets

kubectl get pods

- 업데이트 이력을 저장하는 옵션을 사용해서 리소스를 생성: --record=true 옵션을 추가
kubectl apply -f sample-deployment.yaml --record=true

- 레플리카 셋 목록 확인
kubectl get replicasets -o yaml | head

- 이미지 업데이트
kubectl set image deployment sample-deployment nginx-container=nginx:1.17 --record

- 확인
kubectl rollout status deployment sample-deployment

- 디플로이먼트 와 레플리카셋을 확인
kubectl get deployments

kubectl get replicasets

#### 레플리카셋이 생성되는 조건
- 디플로이먼트에서는 변경이 발생하면 레플리카셋이 생성되는데 변경에는 레플리카의 수의 변경 등은 포함되지 않으며 생성된 파드의 내용 변경이 필요
- spec.template에 변경이 있으면 생성된 파드의 설정이 변경되기 때문에 레플리카셋을 신규로 생성하고 롤링 업데이트를 수행
- 실제로 매니페스트를 쿠버네티스에 등록한 후 레플리카셋의 정의를 보면 spec.template 아래의 해시값을 계산을 하고 이 값을 사용한 레이블로 관리
- 수작업으로 이미지 등을 이전 버전으로 재변경하여 해시값이 동일해지면 레플리카셋을 신규로 생성하지 않고 기존 레플리카셋을 이용

#### 변경 롤백
- 디플로이먼트에는 롤백 기능이 있음
- 현재 사용 중인 레플리카셋의 전환
- 디플로이먼트가 생성한 기존 레플리카셋은 레플리카 수가 0인 상태로 남아있기 때문에 레플리카 수를 변경시켜 다시 사용할 수 있는 상태가 됨
- 변경 이력을 확인할 때는 kubectl rollout history 명령을 사용하는데 CHANGE-CAUSE 부분이 디플로이먼트를 생성할 때 --record 옵션을 사용하여 이력 내용이 있지만 --record 옵션을 사용하지 않으면 none
- 변경 롤백 확인: kubectl rollout history deployment sample-deployment
- 상세하게 보기:  kubectl rollout history deployment sample-deployment --revision 넘버

- 특정 revision 넘버를 이용한 롤백
kubectl rollout undo deployment 이름 --to-revision 번호

- sample-deployment를 2번 버전으로 롤백
kubectl rollout undo deployment sample-deployment --to-revision 2

- 직전 버전으로 롤백
kubectl rollout undo deployment 이름 

- 실제 환경에서는 롤백 기능을 사용하는 경우는 많지 않은데 CI/CD 파이프라인에서 롤백을 하는 경우 kubectl rollout 명령보다는 이전 매니페스트를 다시 kubectl apply 명령어로 실행하여 적용하는 것이 호환성 면에서 좋기 때문이며 spec.template을 같은 내용으로 되도렸을 경우에는 pod-template 해시 값이 같으므로 kubectl rollout 처럼 기존있던 레플리카셋의 파드가 기동됨

#### 변경 일시 정지
- 일반적으로 Deployment를 업데이트 하면 바로 적용되어 업데이트 처리가 실행되지만 안전을 위해서 디플로이먼트에 대한 업데이트를 하더라도 바로 적용되지 않는 것을 원하는 경우도 있음

- 업데이트를 일시 중지
kubectl rollout pause deployment 디플로이먼트이름

- 업데이트 일시 정지
kubectl rollout pause deployment sample-deployment

- 이미지 업데이트
kubectl set image deployment sample-deployment nginx-container=nginx:1.17

- 업데이트 상태 확인: 업데이트 실패
kubectl rollout status deployment sample-deployment

- 롤백
kubectl rollout undo deployment sample-deployment

- 일시정지 해제
kubectl rollout resume deployment sample-deployment

#### 업데이트 전략
- 개요
Deployment의 배포 전략은 주로 애플리케이션이 변경될 때 사용

이전 버전의 애플리케이션에서 업데이트가 필요한 경우에 주로 사용되며 방법으로는 RollingUpdate, Recreate가 있음

기본 전략은 RollingUpdate

- recreate
모든 이전 버전의 pod를 한 번에 종료하고 새로운 버전의 pod로 일괄적으로 교체하는 방식

빠르게 교체할 수 있지만 새로운 버전의 Pod에 문제가 발생하면 대처가 늦어질 수 있습니다

설정 방법은 spec.strategy.type에 Recreate를 설정하면 됩니다.

- Recreate 업데이트 전략을 사용하는 매니페스트를 생성: sample-deployment-recreate.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-deployment-recreate
spec:
  strategy:
    type: Recreate
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
        image: nginx

- 리소스 생성: kubectl apply -f sample-deployment-recreate.yaml

- 이미지 변경: kubectl set image deployment sample-deployment-recreate nginx-container=nginx:1.17

- 레플리카셋 확인: kubectl get replicasets --watch

- Rolling Update
새 버전의 애플리케이션은 하나씩 늘려가고 기존 버전의 애플리케이션은 하나씩 줄여나가는 방식

Kubernetes에서 사용하는 표준 배포 방식

새로운 버전으로 배포된 pod에 문제가 생기면 이전 버전의 pod로 서비스를 대체할 수 있어서 상당히 안정적인 배포 방식이지만 업데이트가 느리게 진행되는 단점이 있음

maxSurge
	Rolling 업데이트를 위해 최대로 생성할 수 있는 pod 개수
	배포를 빠르게 적용 가능
	% 또는 개수로 지정
	설정하지 않을 경우 기본 값은 25%

maxUnavailabel
	Rolling 업데이트 중 최대로 삭제할 pod 개수
	배포를 빠르게 적용 가능
	% 또는 개수로 지정
	설정하지 않을 경우 기본 값은 25%

- Rolling 업데이트 전략을 사용하는 매니페스트를 생성: sample-deployment-rollingupdate.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-deployment-rollingupdate
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 0
      maxSurge: 1
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
        image: nginx

- 리소스 생성: kubectl apply -f sample-deployment-rollingupdate.yaml

- 이미지 변경: kubectl set image deployment sample-deployment-recreate nginx-container=nginx:1.17

- 레플리카셋 확인: kubectl get replicasets --watch
maxUnavailable: 0
maxSurge: 1


이전 레플리카는 3 신규 레플리카는 0
이전 레플리카는 3 신규 레플리카는 1
이전 레플리카는 2 신규 레플리카는 1
이전 레플리카는 2 신규 레플리카는 2
이전 레플리카는 1 신규 레플리카는 2
이전 레플리카는 1 신규 레플리카는 3
이전 레플리카는 0 신규 레플리카는 3


maxUnavailable: 1
maxSurge: 0


이전 레플리카는 3 신규 레플리카는 0
이전 레플리카는 2 신규 레플리카는 0
이전 레플리카는 2 신규 레플리카는 1
이전 레플리카는 1 신규 레플리카는 1
이전 레플리카는 1 신규 레플리카는 2
이전 레플리카는 0 신규 레플리카는 2
이전 레플리카는 0 신규 레플리카는 3

- 블루/그린 업데이트
2개의 동일한 운영 환경을 같이 구축해서 트래픽을 제어하는 방식

Blue(기존 버전): 현재 사용자들이 접속해서 사용 중인 환경
Green(신규 버전): 새롭게 업데이트된 코드가 배포된 환경

Green 환경에서 새 버전을 배포하고 내부 테스트를 진행한 후 문제가 없다고 판단이 되면 로드밸런서의 설정을 변경하여 사용자 트래픽을 Blue 환경에서 Green 환경으로 한 번에 돌립니다.

Green이 새로운 Blue가 되고 기존 Blue는 만약을 위해 유지하거나 폐기

장점은 제로 다운 타임, 빠른 롤백, 안전한 테스트 가능
단점은 비용 부담, 동기화 문제(전환 시점에 데이터베이스 데이터가 엉키는 문제), 설정 복잡

서비스가 조금이라도 끊기면 안되는 고가용성 서비스나 업데이트 시 발생할 수 있는 오류에 대비해 즉각적인 복구가 중요한 서비스나 서버 자원 비용보다는 배포의 안정성이 중요한 경우 사용

- Canary 업데이트
블루/그린과 유사하지만 더 진보적인 방식

애플리케이션의 몇몇 새로운 기능을 테스트할 때 사용

두 개의 배전을 모두 배포하고 새 버전에는 조금씩 트래픽을 증가시켜 새로운 기능을 테스트

두 개의 버전이 동시에 서비스되기 때문에 한 명의 유저가 서로 다른 버전에 접속하는 상황이 생길 수 있습니다.
스티키 세션을 이용해서 사용자가 한 번 특정한 서버에 접속되어 있으면 그 서버에만 계속 접속되도록 만들 수 있음

#### 업데이트 파라미터
- minReadySecods: 파드가 Ready 상태가 된 다음부터 디플로이먼트 리소스에서 파드 가동이 완료되었다고 판단하기 까지의 최소 시간
- revisionHistoryLimit: 디플로이먼트가 유지할 레플리카셋의 개수로 롤백이 가능한 이력 수
- progressDeadlineSeconds: Recreate/RollingUpdate 처리 타임아웃 시간으로 이 시간이 경과하면 자동으로 롤백
- 사용 방법
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-deployment-params
spec:
  minReadySeconds: 0
  revisionHistoryLimit: 2
  progressDeadlineSecods: 3600
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 0
      maxSurge: 1
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
        image: nginx

#### 명령어를 이용한 deployment 스케일링
kubectl scale deployment sample-deployment --replicas=5

#### 매니페스트 없이 디플로이먼트 생성
kubectl create deployment 이름 --image 이미지
kubectl create deployment sample-deployment-cli --image nginx

### 5)DaemonSet
#### 개요
- ReplicaSet의 특수한 형태
- 모든 노드에 Pod를 생성
- 레플리카 수를 지정할 수 없고 하나의 노드에 두 개의 파드를 배치할 수 도 없음
- 파드를 배치하고 싶지 않을 때는 nodeselector나 노드 안티어피니티를 사용한 스케쥴링에서 예외 처리를 하면 됩니다.
- 쿠버네티스의 노드를 늘렸을 때도 데몬셋의 파드는 자동으로 늘어난 노드에서 기동되기 때문에 각 파드가 출력하는 로그를 호스트 단위로 수집하는 Fluentd 나 각 파드 리소스 사용 현황 및 노드 상태를 모니터링하는 Datadog  등 모든 노드에서 반드시 동작해야 한느 프로세스를 위해 사용하는 것이 유용

#### prometheus-export 를 데몬셋으로 배포
- 리소스를 위한 yaml 파일 작성: daemonsets.yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: prometheus-daemonset
spec:
  selector:
    matchLabels:
      tier: monitoring
      name: prometheus-exporter
  template:
    metadata:
      labels:
        tier: monitoring
        name: prometheus-exporter
    spec:
      containers:
      - name: prometheus
        image: prom/node-exporter
        ports:
        - containerPort: 80

- 리소스 생성: kubectl apply -f daemonsets.yaml

- 상세 정보를 확인: kubectl describe daemonsets/prometheus-daemonset

- 파드 확인: kubectl get pods

- 데몬셋 삭제: kubectl delete -f daemonsets.yaml

#### 데몬셋 업데이트 전략
- OnDelete
데몬셋 매니페스트를 수정해서 이미지 등을 변경했더라도 기존 파드는 업데이트 되지 않음

디플로이먼트 와 달이 데몬셋은 모니터링이나 로그 전송과 같은 용도로 많이 사용되기 때문에 업데이트는 다음 번에 다시 생성할 때나 수동으로 임의의 시점에 하게 되어 있으며 type 외 지정할 수 있는 항목이 없음

이전 버전이 장기간 사용되게 됩니다.

임의의 시점에 파드를 업데이트하는 경우는 데못셋과 연결된 파드를 kubectl delete pod 명령으로 수동으로 정지시키고 자동화된 복구 기능으로 새로운 파드를 생성해야 함

spec.updateStraegy.type에 OnDelete를 설정하면 됩니다.

- RollingUpdate
하나의 노드에 동일한 파드를 여러 개 생성할 수 없으므로 디플로이먼트와 달리 동시에 생성할 수 있는 파드 수를 설정할 수 없고 동시에 정지 가능한 최대 파드 수만 지정해서 수행

maxUnavailable=2 의 경우 파드를 두 개씩 동시에 업데이트해 나가는 형태

### 6)StatefulSet
#### 개요
- 레플리카셋의특수한 형태
- 데이터베이스 등 과 같은 stateful 한 워크로드에 사용하기 위한 리소스
- ReplicaSet 과 차이점
생성되는 파드 이름의 접미사는 숫자 인덱스가 부여된 것

파드 이름은 변경되도 내용은 바뀌지 않음

데이터를 영구적으로 저장하기 위한 구조

스테이트풀셋에서는 spec.volumeClaimTemplates을 지정할 수 있는데 이 설정으로 스테이트풀셋으로 생성되는 각 파드에 영구 볼륨 클레임을 설정할 수 있으며 영구 볼륨 클레임을 사용하면 클러스터 외부의 네트워크를 통해 제공되는 영구 볼륨 파드에 연결할 수 있으므로 파드를 재기동할 때나 다른 노드로 이동할 때 같은 데이터를 보유한 상태로 컨테이너가 다시 생성되고 영구 볼륨은 하나의 파드가 소유할 수 잇고 영구 볼륨 종류에 따라 여러 파드에서 공유하는 것도 가능 

#### 생성 및 확인
- 리소스를 위한 매니페스트 작성: sample-statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: sample-statefulset
spec:
  serviceName: sample-statefulset
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
        image: nginx
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html

  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 1G

- 리소스 생성: kubectl apply -f sample-statefulset.yaml

- 확인: kubectl get pods

- 볼륨 클레임 확인: kubectl get persistentvolumeclaims

- persistemvolume 확인: kubectl get persistentvolumes

#### 스케일링
- 스케일링 수행
kubectl scale statefulset sample-statefulset --replicas=5
접미사가 일련 번호
삭제할 때는 접미사의 일련번호가 큰 것 부터 삭제됩니다.

### 7)Job
#### 개요
- 컨테이너를 사용하여 한 번만 실행되는 리소스
- N개의 병렬로 실행하면서 지정한 횟수의 컨테이너 실행을 보장하는 리소스

#### 파이를 2천자리까지 계산해서 출력하는 잡을 생성
- 매니페스트 파일: sample-job.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl:5.34.0
        command: ["perl", "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4

- 잡 실행
kubectl apply -f sample-job.yaml

- 잡 확인
kubectl get jobs

- 파드 확인
kubectl get pods --watch

- 결과 확인
kubectl logs pi

#### restartPolicy
- spec.template.restartPolicy에 OnFailure 또는 Never 중 하나를 지정해야 하는데 Never를 지정한 경우 파드에 장애가 발생하면 신규 파드가 생성되고 OnFailure의 경우 다시 동일한 파드를 사용하여 잡을 다시 시작

- Never 적용: sample-job-never-restart.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: errorjob-unvalidcommand
spec:
  template:
    spec:
      containers:
      - name: errorjob-unvalidcommand
        image: busybox
        command: ["ls", "unvalid path"]
      restartPolicy: Never

리소스 생성: kubectl apply -f sample-job-never-restart.yaml

파드 확인: kubectl get pods
명령이 에러라서 정상 수행하기 위해서 파드를 계속 생성합니다.



- OnFailure 적용: sample-job-onfailure-restart.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: errorjob-unvalidcommand
spec:
  template:
    spec:
      containers:
      - name: errorjob-unvalidcommand
        image: busybox
        command: ["ls", "unvalid path"]
      restartPolicy: OnFailure

리소스 생성: kubectl apply -fsample-job-onfailure-restart.yaml

파드 확인: kubectl get pods --watch
파드를 다시 만들지 않고 명령어만 재수행

#### 태스크 와 작업 큐 병렬 실행
- 성공 횟수를 지정하는 completions 와 병렬성을 지정하는 parallelism 설정 값은 기본이  1
- 2개의 파라미터는 잡을 병렬화해서 실행할 때 사용하는 옵션
- backoffLimit는 실패를 허용하는 횟수
					completions	parallelism	backoffLimit
한번만 실행하는 태스크			1		1		0
N개를 병렬로 실행하는 태스크		임의의숫자	N		임의의숫자
한 개씩 순서대로 실행하는 큐 형태 작업	설정하지 않음	1		임의의숫자
N개를 병렬로 실행하는 큐 형태 작업		설정하지 않음	N		임의의숫자

#### 잡이 완료된 후 일정 시간이 지나면 잡 삭제
- spec.ttlSecondsAfterFinished: 초단위설정
apiVersion: batch/v1
kind: Job
metadata:
  name: ttl
spec:
  ttlSecondsAfterFinished: 30
  template:
    spec:
      containers:
      - name: ttl
        image: perl:5.34.0
        command: ["perl", "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4

### 8)CronJob
#### 주기적으로 특정 행동을 수행하는 워크로드 
#### spec.schedule에서 주기 지정해야 합니다
주기를 지정하는 방법은 Linux에서 Crontab을 설정할 때 와 동일합니다.
#### 크론 잡 수행
- 매니페스트 파일 생성: cronjob.yaml
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
          - name: name
            image: busybox
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from Kubernetes Cluster
          restartPolicy: OnFailure

























