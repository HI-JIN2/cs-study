**Kubernetes**

# 1. 쿠버네티스 보안

## 1) RBAC (Role-Based Access Control)
- 사용자나 서비스 어카운트(ServiceAccount)에 권한을 부여하는 방식
- **Role**: 특정 네임스페이스 내에서의 권한 정의
- **ClusterRole**: 클러스터 전체 수준에서의 권한 정의
- **RoleBinding**: Role과 대상(User/Group/SA)을 연결
- **ClusterRoleBinding**: ClusterRole과 대상을 연결

## 2) 서비스 어카운트 (ServiceAccount)
- 파드 내에서 실행되는 프로세스가 쿠버네티스 API에 접근할 때 사용하는 계정
- 기본적으로 각 네임스페이스에는 `default` 서비스 어카운트가 존재함

### 실습: 서비스 어카운트 생성 및 권한 부여
```bash
# 서비스 어카운트 생성
kubectl create serviceaccount my-sa

# 롤 생성 (파드 조회 권한)
kubectl create role pod-reader --verb=get,list,watch --resource=pods

# 롤 바인딩
kubectl create rolebinding my-sa-binding --role=pod-reader --serviceaccount=default:my-sa
```

# 2. Helm

## 1) 개요
- 쿠버네티스용 패키지 매니저
- 복잡한 매니페스트 파일들을 차트(Chart)라는 단위로 묶어서 관리하고 배포할 수 있게 해줌

## 2) 주요 개념
- **Chart**: 템플릿화된 매니페스트 파일들의 집합
- **Values**: 차트 배포 시 주입할 설정 값 (커스터마이징)
- **Release**: 클러스터에 배포된 차트의 인스턴스

## 3) 실습: Helm을 이용한 배포
```bash
# 리포지토리 추가
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# 차트 설치
helm install my-release bitnami/nginx

# 설치된 릴리스 확인
helm list

# 릴리스 업그레이드 (설정 변경 시)
helm upgrade my-release bitnami/nginx --set service.type=NodePort

# 릴리스 삭제
helm uninstall my-release
```

# 3. Kustomize

## 1) 개요
- 템플릿 없이 매니페스트를 커스터마이징하는 도구 (Overlay 방식)
- 기본(Base) 매니페스트는 건드리지 않고, 환경별(Overlays) 차이점만 정의함
- `kubectl`에 내장되어 있음 (`kubectl apply -k .`)

## 2) 실습: Kustomize 사용

### kustomization.yaml 작성
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- deployment.yaml
- service.yaml
- ingress.yaml
commonLabels:
  app.kubernetes.io/name: echo
```

### 실행
```bash
# 빌드 결과 확인
kubectl kustomize .

# 직접 적용
kubectl apply -k .
```

### 주요 기능
- **commonLabels**: 모든 리소스에 공통 레이블 추가
- **namePrefix**: 리소스 이름 앞에 접두사 추가
- **images**: 컨테이너 이미지 태그 변경
