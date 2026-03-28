**Kubernetes**

# 1. 리소스 제한

## 1) 개요
- 쿠버네티스에서는 컨테이너 단위로 리소스 제한 설정이 가능
- 서비스에 맞는 성능을 내기 위해서도 리소스 제한 설정은 필요
- 제한이 가능한 리소스는 CPU, Memory, Ephemeral 스토리지이지만 Device Plugin을 사용하면 GPU 등의 다른 리소스에 대해서도 제한 설정을 할 수 있음

## 2) CPU 와 메모리 제한
- CPU는 클럭 수로 지정하지 않고 1vCPU(가상 CPU 1개)를 1000m(millicores) 단위로 지정
- 3GHz CPU 라고 해도 1코어 정도를 지정할 경우 3000m 가 아니고 1000m

### 설정 항목
- **requests**: 최소 보장 리소스. 스케쥴러가 파드를 배치할 노드를 결정할 때 이 값을 기준으로 여유 공간이 있는지 판단함
- **limits**: 사용할 수 있는 최대 리소스. 컨테이너가 이 값을 초과하려고 하면 제약이 발생함

### 리소스 초과 시 동작
- **CPU**: limits를 초과하면 스로틀링(Throttling)이 발생하여 성능이 저하됨 (프로세스가 죽지는 않음)
- **Memory**: limits를 초과하면 OOM(Out Of Memory) Killer에 의해 컨테이너가 강제 종료됨

## 3) 실습: 리소스 제한 설정

### 매니페스트 작성: `sample-resource.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample-resource
spec:
  containers:
  - name: nginx-container
    image: nginx
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

### 실행 및 확인
```bash
kubectl apply -f sample-resource.yaml
kubectl describe pod sample-resource
```

## 4) LimitRange

### 개요
- 네임스페이스 수준에서 기본 리소스 제한 값을 설정하거나, 허용되는 최소/최대 범위를 지정하는 리소스
- 개별 파드에 리소스 제한 설정이 누락된 경우 기본값으로 채워주거나, 너무 과도한 리소스 요청을 방지함

### 주요 설정 항목
- **default**: 기본 limits
- **defaultRequest**: 기본 requests
- **max**: 최대 리소스
- **min**: 최소 리소스
- **maxLimitRequestRatio**: limits/requests 의 최대 비율

### 설정 대상
- **Container**: CPU, Memory, Ephemeral Storage 설정 가능
- **Pod**: default 와 defaultRequest를 제외한 max, min 등 설정
- **PersistentVolumeClaim**: max 와 min 지정 가능

## 5) 리소스 제한의 중요성
- 파드나 컨테이너에 대해 리소스 제한을 전혀 하지 않을 경우, 실제 노드 부하와 상관없이 계속 파드를 스케쥴링하게 됨
- 스케쥴러는 requests에서 지정한 리소스를 확보할 수 있을지에 따라 스케쥴링 여부를 판단하기 때문에, requests를 지정하지 않으면 스케쥴러는 계속 그 노드에 파드를 배치할 수 있다고 판단함
- 리소스 소비가 과도하게 많아지면:
  - **Memory**: OOM killer에 의해 프로세스가 정지됨
  - **CPU**: 전체 동작이 느려져 최악의 경우 운영체제 자체가 응답하지 않을 수 있음
