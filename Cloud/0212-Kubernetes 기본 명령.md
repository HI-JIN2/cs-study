# 쿠버네티스
## 1. 쿠버네티스 기본 명령
### 1)분리성 확보
#### namespace
- 가상적인 쿠버네티스 클러스터 분리 기능
- 완전한 분리 개념이 아니기 때문에 용도는 제한되지만 하나의 쿠버네티스 클러스터를 여러 팀에서 사용하거나 서비스 환경/스테이징 환경/개발 환경으로 구분하는 경우 또는 애플리케이션 스택을 분리하고자 하는 경우에 사용
- 기본 설정에서 제공하는 Namespace
kube-system: 쿠버네티스 클러서터의 구성 요소 와 애드온이 배포될 네임스페이스
 
kube-public: 모든 사용자가 사용할 수 있는 ConfigMap 등을 배치하는 네임스페이스

kube-node-lease: 노드의 하트비트(노드의 상태) 정보를 저장하기 위한 Lease 리소스가 저장되는 네임스페이스

### 2)kubectl
#### 개요
- 쿠버네티스에서 클러스터 조작은 쿠버네티스 Master의 API Server를 통해 수행
- 수동으로 조작할 때 사용하는 CLI 도구가 kubectl

#### 인증 정보
- kubectl이 쿠버네티스 마스터와 통신할 때는 접속 대상의 서버 정보, 인증 정보 등이 필요
- kubectl은 kubeconfig(기본 위치는 HOME 디렉토리의 .kube/config
- kubeconfig도 매니페스트 와 동일한 형식으로 작성
- 여러 개의 클러스터를 조작하고자 하는 경우 이 정보를 수정해가면서 하면 됩니다.

#### .kube/config 수정
- 직접 파일을 편집
- kubectl config 명령으로 수정

#### 컨텍스트 관련 명령
- 현재 컨텍스트 확인
kubectl config current-context 

- 명령어를 실행할 때 특정 컨텍스트에 명령을 수행
kubectl 명령어 --context 컨텍스트이름

kubectl get pods --context kubernetes-admin@kubernetes

#### 리소스 생성/삭제/갱신
- kubectl run
가장 간단하게 pod를 생성하는 명령어

run 명령어는 실행 커맨드에 각종 정보를 입력해서 생성

간편하지만 실행 시 입력한 정보가 남지 않기 때문에 재실행하기가 어렵고 문제가 발생했을 때 원인을 찾거나 해결 방법을 공유하는 것이 불편

테스트나 간단한 pod를 만들 때는 유용하지만 많은 configuration을 활용한다면 불편

nginx 파드 생성: kubectl run nginx --image=nginx

리소스(pod) 확인: kubectl get pods


- kubectl create
여러 종류의 리소스를 만들 수 있는 명령어

기본 형식: kubectl create 리소스종류 리소스이름 필요한옵션

디플로이먼트를 생성
kubectl create deployment dpy-nginx --image=nginx

매니페스트를 이용해서 생성: kubectl create -f 매니페스트파일경로(yaml로 작성)
이 명령은 리소스가 존재하면 에러가 발생

	sample-pod.yaml 작성
		apiVersion: v1
		kind: Pod
		metadata:
  		  name: sample-pod
		spec:
  		  containers:
  		  - name: nginx-container
    		    image: nginx

	리소스 생성(없을 때는 생성): kubectl create -f sample-pod.yaml

	리소스 생성(있을 때는 에러): kubectl create -f sample-pod.yaml

- 리소스 삭제: kubectl delete
형식: kubectl delete 생성할때명령

상위 객체는 하위 객체가 지워지면 자동으로 다시 생성

kubectl 명령은 문법에 맞게 작성을 하고 API Server가 정당한 명령이라고 인식을 하면 완료되었다고 하지만 실제 리소스 처리는 비동기 방식으로 처리가 되기 때문에 리소스 처리가 완료된 상태는 아닐 수 있음

--wait를 사용하면 리소스의 삭제 완료를 기다렸다가 명령어 실행을 종료할 수 있습니다.

리소스를 강제로 즉시 삭제하려면 정지까지 유예 기간을 0으로 하는 --grace-period 0 옵션과 강제로 삭제하는 --force 옵션을 사용

쿠버네티스 최신 버전은 --force 가 --grace-period 0 옵션을 포함

yaml 파일에 작성한 리소스를 생성
	yaml 파일을 작성: sample-pod.yaml

	apiVersion: v1
	kind: Pod
	metadata:
  	  name: sample-pod
	spec:
  	  containers:
  	  - name: nginx-container
    	    image: nginx


	생성 명령: kubectl create -f sample-pod.yaml

	확인: kubectl get pods

	삭제 명령이 실제로 처리될 때까지 응답 유보: kubectl delete -f sample-pod.yaml --wait 
	
#### 리소스 업데이트
- kubectl apply

- 리소스가 없을 때는 생성하고 리소스가 존재하는 경우 변경 부분이 있으면 업데이트하고 변경 부분이 없으면 아무일도 하지 않음

- 없을 때는 생성
kubectl apply -f sample-pod.yaml

- 리소스가 존재하는 경우 - 변경 사항이 없음: unchanged 라고 나옵니다.
kubectl apply -f sample-pod.yaml

- sample-pod.yaml 파일 수정
apiVersion: v1
kind: Pod
metadata:
  name: sample-pod
spec:
  containers:
  - name: nginx-container
    image: nginx:1.17

- 리소스가 존재하는 경우 - 변경 사항이 있음: 업데이트 수행
kubectl apply -f sample-pod.yaml

- 가동 중인 파드의 세부 내용 확인
kubectl get pods -o jsonpath='{.items[*].spec.containers[*].image}'

- 리소스 생성에 create 대신 apply를 사용해야 하는 이유
생성할 때는 create를 사용하고 업데이트 할 때 apply를 사용해야 한다면 CI/CD를 구현할 때 스크립트 작성 시 분기 문을 사용해야 하는데 분기문은 CI/CD에서 사용하지 않는 것을 권장

apply 와 create를 섞어서 사용하면 apply가 변경 사항을 검출하지 못하는 경우가 있습니다.
create는 이전 상태를 저장하지 않기 때문입니다.
create를 할 때 --save-config 옵션을 사용해서 상태를 저장을 해야 합니다.
그렇지 않으면 create 후에 apply를 하게 되면 경고와 함께 변화 여부에 상관없이 수정을 합니다.

#### 리소스 필드 수정
- 현재 변경 사항의 산출은 클라이언트 측에서 계산해서 요청을 보내기 때문에 여러 사용자나 구성 요소가 동시에 같은 필드를 변경하는 경우 경합 현상이 발생할 수 있는데 이 문제를 해결하기 위해 필드를 변경한 구성 요소를 기록하는 기능과 서버 측에서 변경 사항을 계산하는 기능이 도입됨
- 매니페스트 파일을 수정하지 않고 변경을 하면 서버에 상태가 저장되지 않기 때문에 다시 apply를 하면 이전 상태로 돌아가버립니다.
- 리소스를 배포할 때 서버 정보를 이용하면서 생성하거나 업데이트 하려면 --server-side 옵션을 같이 사용하면 됩니다.

- sample-pod.yaml 확인
apiVersion: v1
kind: Pod
metadata:
  name: sample-pod
spec:
  containers:
  - name: nginx-container
    image: nginx:1.17

- 리소스 배포
kubectl apply -f sample-pod.yaml

- 이미지 확인 
kubectl get pods -o jsonpath='{.items[*].spec.containers[*].image}'

- 이미지를 명령어로 수정
kubectl set image pod sample-pod nginx-container=nginx

- 이미지 확인
kubectl get pods -o jsonpath='{.items[*].spec.containers[*].image}'

- 리소스 배포
kubectl apply -f sample-pod.yaml

- 이미지 확인
kubectl get pods -o jsonpath='{.items[*].spec.containers[*].image}'

#### 파드 재시작
- 리소스와 연결되어 있는 모든 파드를 재기동: rollout restart
- 파드가 이미 시작되어 실행 중인데 재실행을 해야 하는 경우는 대부분 시크릿 리소스에서 참조되는 환경 변수를 변경하고 싶을 때 사용합니다.
- 리소스에 연결되어 있지 않은 단독 파드에는 사용할 수 없음
- Deployment 배포를 위한 yaml 파일 작성: sample-deployment.yaml
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

- 리소스 배포: kubectl apply -f sample-deployment.yaml

- 현재 파드 확인: kubectl get pods
NAME                                 READY   STATUS    RESTARTS      AGE
sample-deployment-556b965888-7gfb7   1/1     Running   0             2m28s #deployment로 만들어 짐
sample-deployment-556b965888-85rxz   1/1     Running   0             2m28s
sample-deployment-556b965888-slrgh   1/1     Running   0             2m28s

sample-pod                           1/1     Running   2 (14m ago)   19m #파드 단독으로 만들어 짐

- deployment 의 모든 파드 재시작: 실제로는 파드를 새로 만들고 그 파드가 정상 동작하면 이전 파드를 삭제하는 방식: Rolling Update라고 합니다.
kubectl rollout restart deployment sample-deployment

- 파드를 재시작: 파드는 단독으로 배포되는 경우 재시작이 안됨
kubectl rollout restart pod sample-pod 

#### generateName
- create 명령을 이용해서 생성할 때 난수가 있는 이름의 리소스를 생성할 수 있음
- metadata.name 대신에 metadata.generateName을 지정하고 리소스를 생성하면 그 이름에 접두사를 붙여 이름이 자동으로 생성
- yaml 파일 작성: sample-generatename.yaml
apiVersion: v1
kind: Pod
metadata:
  generateName: sample-generatename-
spec:
  containers:
  - name: nginx-container
    image: nginx

- 리소스 생성: kubectl create -f sample-generatename.yaml

- 확인: kubectl get pods
설정한 이름 뒤에 난수가 붙어서 pod의 이름이 결정됨

- 모든 리소스 삭제
kubectl delete all --all

#### 리소스 상태 체크와 대기
- 리소스가 정상적으로 기동될 때 까지 대기시키고자 하는 경우는 kubectl wait --for=condition=Ready 리소스종류/리소스이름
- 모든 리소스가 스케쥴링 될 때 까지 대기시키고자 하는 경우는 kubectl wait --for=condition=PodScheduled 리소스종류 --all
- 리소스 생성
kubectl create -f sample-pod.yaml

kubectl create -f sample-generatename.yaml

kubectl create -f sample-generatename.yaml

#sample-pod가 준비될 때 까지 대기
kubectl wait --for=condition=Ready pod/sample-pod

#모든 pod가 스케쥴링 될 때 까지 대기
kubectl wait --for=condition=PodScheduled pod --all

#모든 파드가 삭제될 때 까지 파드마다 5초씩 대기: 삭제 명령을 수행하지 않았으므로 타임아웃
kubectl wait --for=delete pod --all --timeout=5s

#모든 파드를 삭제 후 곧바로 kubectl wait를 실행
kubectl delete pod --all --wait=false

#모든 파드가 전부 삭제될 때 까지 대기
kubectl wait --for=delete pod --all

#매니페스트 파일에도 가능
kubectl apply -f sample-pod.yaml

kubectl wait --for=condition=Ready -f sample-pod.yaml

#### 매니페스트 파일 설계
- 하나의 매니페스트 파일에 여러 리소스를 정의
보통의 경우는 하나의 매니페스트 파일에 하나의 리소스를 정의하는 경우가 많음

워크로드 API 와 이 리소스를 외부에 공개하기 위한 서비스 API를 사용해야 하는 경우 리소스 간의 결합도를 높이기 위해서 하나의 파일에 작성하는 경우도 있음

- 하나의 매니페스트 파일에 여러 개 리소스 정의: sample-multi-resource-manifest.yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order1-deployment
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

---
apiVersion: v1
kind: Service
metadata:
  name: order2-service
spec:
  type: LoadBalancer
  ports:
  - name: "http-port"
    protocol: "TCP"
    port: 8080
    targetPort: 80
  selector:
    app: sample-app

- 리소스 생성: kubectl apply -f sample-multi-resource-manifest.yaml

- 여러 개의 리소스를 하나의 매니페스트에서 생성하는 경우 중간에 문제가 생겨서 리소스를 생성하지 못하면 아래 문장들도 수행되지 않습니다.

- 여러 개의 리소스 파일을 한꺼번에 적용하고자 하는 경우는 하나의 디렉토리에 저장한 후 kubectl apply 명령을 할 때 그 디렉토리를 지정하면 되는데 주의할 점은 파일명 순으로 실행되므로 순서대로 실행하고자 하는 경우는 연번의 인덱스 번호를 이용해야 하고 내부에 하위 디렉토리가 있는 경우는 -R 옵션을 추가해 주어야 합니다.
실제 실무에서는 많이 사용하는 형태입니다.

- 실습
디렉토리 생성 - dir

매니페스트 파일 생성: nano dir/sample-pod1.yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample-pod1
spec:
  containers:
  - name: nginx-container
    image: nginx:1.17

매니페스트 파일 생성: nano dir/sample-pod2.yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample-pod2
spec:
  containers:
  - name: nginx-container
    image: nginx:1.17

내부 디렉토리 생성: mkdir dir/inner

내부 디렉토리에 매니페스트 파일 생성: nano dir/inner/sample-pod3.yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample-pod3
spec:
  containers:
  - name: nginx-container
    image: nginx:1.17

매니페스트 파일 실행: kubectl apply -f dir, kubectl apply -f  dir -R

- 설계
아주 규모가 크지 않을 때는 시스템 전체를 구성하기 위한 모든 마이크로서비스의 매니페스트 파일을 하나의 디렉토리로 통합하여 사용

한 개의 디렉토리에 파일 통합이 어려울 정도로 거대한 시스템인 경우는 분리가 가능하다면 서브 시스템이나 부서 별로 디렉토리를 나누어서 사용

마이크로서비스마다 디렉토리로 구분하여 리소스 종류 별로 파일을 생성할 수 도 있는데 이 경우 가독성이 높아지지만 적용 순서 제어가 어렵다는 단점도 있음 - 헬름 같은 별도의 배포 도구를 이용


#### 어노테이션 과 레이블
- 쿠버네티스에서는 각 리소스에 대해 어노테이션 과 레이블이라는 메타 데이터를 부여할 수 있는데 쿠버네티스가 리소스를 관리할 때 사용한다는 점에서는 비슷하지만 용도가 다름
- 차이

		Laber				Annotation
목적		리소스 분류, 선택, 검색		리소스에 추가 설명
검색		지원(Label Selector로 매칭)		지원하지 않음
Key 형식		prefix/name			prefix/name
Key 길이	제한	253자 이하			253자 이하
Value 길이 제한	63자 이하				262,144자 이하
Value 용도	간단한 구분자			설명

- 레이블을 이용한 검색
kubectl get pods -l 키=값 또는 키


#### 매니페스트 관리 와 자동 적용
- 쿠버네티스를 실제 운용할 때 수동으로 kubectl 명령어를 실행하는 일은 거의 없는데 수동으로 하면 휴먼 에러의 가능성이 높기 때문에 매니페스트를 git 과 저장소에 저장하고 변경이 있을 때만 kubectl apply 명령을 사용해서 자동으로 매니페스트를 적용시키는 방법을 사용하는 경우가 많음

- kubectl apply 명령어를 실행할 때 매니페스트에서 삭제된 리소스를 감지하여 자동으로 삭제하는 기능을 사용할 때는 --prune 옵션을 사용

#### 실습
- 리소스를 생성: kubectl apply -f ./dir

- 현재 상태 확인: kubectl get pods 
2개의 파드가 존재

- sample-pod2.yaml을 이동: mv ./dir/sample-pod2.yaml .

- 리소스를 생성: kubectl apply -f ./dir

- 현재 상태 확인: kubectl get pods 
2개의 파드가 존재: sample-pod2.yaml 파일이 없어졌지만 삭제되지는 않음

- 기존 리소스를 삭제(kubectl delete all --all)하고 sample-pod2.yaml 파일을 dir 디렉토리로 이동(mv sample-pod2.yaml ./dir)


- 기존 매니페스트 파일 수정
apiVersion: v1
kind: Pod
metadata:
  name: sample-pod1
  labels:
    system: a
spec:
  containers:
  - name: nginx-container
    image: nginx:1.17

apiVersion: v1
kind: Pod
metadata:
  name: sample-pod2
  labels:
    system: a
spec:
  containers:
  - name: nginx-container
    image: nginx:1.17

- 리소스를 생성: kubectl apply -f ./dir --prune -l system=a

- 현재 상태 확인: kubectl get pods 

- sample-pod2.yaml을 이동: mv ./dir/sample-pod2.yaml .

- 리소스를 생성: kubectl apply -f ./dir --prune -l system=a

- 현재 상태 확인: kubectl get pods 

#### 리소스 정보 업데이트
- 기본 형식: kubectl set 변경할정보 리소스종류 리소스이름 실제값
- 변경 가능한 설정 정보
env
image
resources
selector
serviceaccount
subject

-  이미지를 변경
kubectl apply -f sample-pod.yaml

#sample-pod 의 nginx-container의 image를 1.17 버전으로 변경
kubectl set image pod sample-pod nginx-container=1.17

#변경 내용 확인
kubectl diff -f sample-pod.yaml

#### 사용 가능한 리소스 종류 확인
- 모든 리소스 종류: kubectl api-resources
- 네임스페이스 수준의 리소스: kubectl api-resources --namespaced=true
- 클러스터 수준의 리소스: kubectl api-resources --namespaced=false

#### 리소스 정보 가져오기
- 리소스 전체 가져오기: kubectl get all
- 특정 리소스 정보만 가져오기: kubectl get 리소스종류
- 하나의 리소스만 가져오기: kubectl get 리소스종류 리소스이름
- 특정 레이블을 가진 리소스 가져오기: kubectl get 리소스종류 label이름=label값
- 노드 목록 표시: kubectl get nodes
- 자세히 보기: -o 형식(JSON/YAML/Custom Columns/JOSN Path/Go Template)

kubectl get pods 

kubectl get pods -o yaml #자세히 보기

kubectl get pods -o yaml sample-pod #pod 정보를 yaml 형식으로 보기

#Custom-columns를 이용해서 특정 컬럼의 값을 출력하기
#.metadata.name을 NAME으로 .status.hostIP를 NodeIP로 변경해서 출력
kubectl get pods -o custom-columns="NAME:{.metadata.name}, NodeIP:{.status.hostIP}"

#특정 속성의 값만 출력
kubectl get pods sample-pod -o jsonpath="{.metadata.name}" 

#### 리소스 사용량 확인: Metric Service가 있어야 가능
- Metric Server 설치: kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

- Metric Server Pod 확인
kubectl -n kube-system get pods

- 노드의 리소스 사용량 확인 
kubectl top node

- 파드의 리소스 사용량 확인
kubectl -n kube-system top pod

- 컨테이너의 리소스 사용량 확인
kubectl -n kube-system top pod --containers

#### 컨테이너에 명령 수행 
- 배포: kubectl apply -f sample-pod.yaml
- 컨테이너에 접속해서 명령 수행: kubectl exec -it sample-pod -- /bin/ls
- 컨테이너에 명령 수행: kubectl exec -it sample-pod -c nginx-container -- /bin/ls

#### 포트 포워딩
kubectl port-forward 리소스 외부포트:내부포트
- 리소스에 파드를 설정하면 파드에 포워딩
- 리소스에 레플리카 나 디플로이먼트를 설정하면 파드 중 하나로 포워딩
- 서비스를 포트 포워딩 하면 서비스에 연결된 파드 중 하나로 포트 포워딩

#### 로그 확인
- 파드의 로그 확인: kubectl logs 파드이름
- 컨테이너의 로그 확인: kubectl logs 파드이름 -c 컨테이너이름
- 실시간 로그 출력: -f 옵션을 추가
- 특정 시간 이내의 로그를 출력: --since=숫자단위
1시간 이내: --since=1h
- 시간 같이 출력: --timestamp=true
- 마지막 몇 개 출력: --tail=개수 
- 특정 레이블의 모든 파드의 로그 출력: --selector 레이블

#### 파일 복사
kubectl cp 소스 타겟















