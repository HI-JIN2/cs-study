**Kubernetes**

# 1. PersistentVolume (PV) & PersistentVolumeClaim (PVC)

## 1) 개요
- 파드는 기본적으로 휘발성(Ephemeral) 스토리지를 사용하므로, 파드가 재시작되거나 삭제되면 내부 데이터가 손실됨
- 데이터를 영구적으로 보존하기 위해 쿠버네티스에서는 PV와 PVC 리소스를 사용

### PersistentVolume (PV)
- 클러스터 관리자가 프로비저닝한 물리적 스토리지 리소스
- NFS, iSCSI, 클라우드 제공업체의 특정 스토리지(EBS, Azure Disk 등)와 연결됨

### PersistentVolumeClaim (PVC)
- 사용자가 스토리지 리소스를 요청하는 선언
- 사용자는 원하는 용량과 접근 모드(ReadOnlyMany, ReadWriteOnce 등)를 PVC에 작성하여 요청함

## 2) 스토리지 클래스 (StorageClass)
- PV를 동적으로 생성하기 위한 템플릿
- 관리자가 미리 정의해두면, 사용자가 PVC를 생성할 때 자동으로 적절한 PV가 생성됨

## 3) 실습: HostPath를 이용한 PV/PVC

### PV 매니페스트 작성: `sample-pv.yaml`
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: sample-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

### PVC 매니페스트 작성: `sample-pvc.yaml`
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: sample-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

### 파드에서 PVC 사용: `sample-pv-pod.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample-pv-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
    volumeMounts:
    - name: sample-volume
      mountPath: /usr/share/nginx/html
  volumes:
  - name: sample-volume
    persistentVolumeClaim:
      claimName: sample-pvc
```

### 실행 및 확인
```bash
kubectl apply -f sample-pv.yaml
kubectl apply -f sample-pvc.yaml
kubectl apply -f sample-pv-pod.yaml
```

# 2. StatefulSet

## 1) 개요
- 애플리케이션의 상태를 유지해야 하는 경우(예: 데이터베이스, 분산 시스템) 사용하는 워크로드 API
- Deployment와 달리 파드에 고유한 식별자(이름-0, 이름-1 등)와 안정적인 네트워크 아이덴티티를 제공함
- 각 파드는 자신만의 PVC를 가질 수 있음 (`volumeClaimTemplates` 이용)

## 2) 실습: StatefulSet 생성

### 매니페스트 작성: `sample-statefulset.yaml`
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: sample-statefulset
spec:
  serviceName: "nginx"
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```

### 확인
```bash
kubectl get pods
```
- 결과: `sample-statefulset-0`, `sample-statefulset-1`, `sample-statefulset-2` 순서대로 생성됨
