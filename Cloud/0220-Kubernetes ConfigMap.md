**Kubernetes**

# 1. ConfigMap

## 1) 개요
- 컨피그맵 리소스는 설정 파일이나 설정 파라미터를 컨테이너에 전달하기 위한 리소스
- 쿠버네티스에서는 컨테이너 이미지와 설정 파일을 별개로 관리하는 것이 기본 원칙 (이미지 수정 없이 설정 변경 가능)
- 컨피그맵 리소스는 하나의 컨피그맵에 여러 Key-Value 값을 저장할 수 있으며 최대 사이즈는 1MB
- `nginx.conf` 파일 전체를 컨피그맵에 저장해도 되고, 특정 설정 파라미터만 저장해도 됨

## 2) 생성
- 파일에서 값을 참조해서 생성

### 파일 시스템에서 생성
- 디렉토리에 있는 모든 파일을 이용해 컨피그맵 생성
```bash
kubectl create configmap [이름] --from-file=[디렉토리경로]
```
- 특정 파일 하나를 이용해 컨피그맵 생성
```bash
kubectl create configmap [이름] --from-file=[파일명]
```
- 여러 파일을 지정해서 생성
```bash
kubectl create configmap [이름] --from-file=[파일1] --from-file=[파일2]
```

### 리터럴 값을 이용해서 생성
```bash
kubectl create configmap [이름] --from-literal=key1=value1 --from-literal=key2=value2
```

### 환경 변수 정의 파일을 이용해서 생성
```bash
kubectl create configmap [이름] --from-env-file=[파일명]
```

### 매니페스트를 이용해서 생성
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sample-configmap
data:
  key1: value1
  key2: value2
```

## 3) 사용 방법
- 컨피그맵에 저장된 데이터를 컨테이너에서 사용하는 방법은 크게 3가지

### 환경 변수로 참조
- 컨피그맵의 특정 키 값을 컨테이너의 환경 변수로 설정
- 컨피그맵의 모든 키-값 쌍을 컨테이너의 환경 변수로 설정

### 볼륨으로 마운트
- 컨피그맵의 데이터를 파일 형태로 컨테이너의 특정 경로에 마운트
- 설정 파일 전체를 전달할 때 유용

### 명령행 인자(command/args)에서 참조
- 쉘 스크립트 등에서 환경 변수 형태로 참조해서 사용

## 4) 실습: 환경 변수로 사용

### 매니페스트 작성: `sample-configmap.yaml`
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sample-configmap
data:
  language: "korean"
  greeting: "안녕하세요"
```

### 컨피그맵 생성
```bash
kubectl apply -f sample-configmap.yaml
```

### 컨피그맵 확인
```bash
kubectl get configmap sample-configmap
kubectl describe configmap sample-configmap
```

### 파드에서 컨피그맵 참조: `sample-configmap-pod.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample-configmap-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
    env:
    - name: LANG_SET
      valueFrom:
        configMapKeyRef:
          name: sample-configmap
          key: language
    - name: GREETING_SET
      valueFrom:
        configMapKeyRef:
          name: sample-configmap
          key: greeting
```

### 파드 생성 및 확인
```bash
kubectl apply -f sample-configmap-pod.yaml
kubectl exec sample-configmap-pod -- env | grep _SET
```
```bash
LANG_SET=korean
GREETING_SET=안녕하세요
```

### 모든 데이터를 환경 변수로 한 번에 등록: `envFrom` 이용
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample-configmap-pod-envfrom
spec:
  containers:
  - name: nginx-container
    image: nginx
    envFrom:
    - configMapRef:
        name: sample-configmap
```

## 5) 실습: 볼륨으로 마운트해서 사용

### 파드 매니페스트 작성: `sample-configmap-volume-pod.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample-configmap-volume-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: sample-configmap
```

### 실행 및 확인
```bash
kubectl apply -f sample-configmap-volume-pod.yaml
kubectl exec sample-configmap-volume-pod -- ls /etc/config
kubectl exec sample-configmap-volume-pod -- cat /etc/config/language
```
- 결과: `language`, `greeting` 파일이 생성되어 있고, 내용은 컨피그맵의 데이터 값과 일치

### 특정 키만 선택해서 마운트하고 파일명 변경하기
```yaml
volumes:
- name: config-volume
  configMap:
    name: sample-configmap
    items:
    - key: language
      path: mylang.txt
```
- 결과: `/etc/config/mylang.txt` 파일만 생성됨

## 6) 업데이트 및 동기화
- 컨피그맵을 볼륨으로 마운트한 경우, 컨피그맵의 내용을 수정하면 컨테이너 내부의 파일도 일정 시간(Kubelet 주기) 후에 자동으로 업데이트됨 (Hot Swap 가능)
- 단, 환경 변수로 참조한 경우는 파드를 다시 시작해야 변경 사항이 반영됨
- 볼륨 마운트 시 `subPath`를 사용한 경우는 자동 업데이트가 되지 않음
