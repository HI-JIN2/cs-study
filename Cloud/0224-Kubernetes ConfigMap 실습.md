**Kubernetes**

# 1. ConfigMap 실습 (계속)

## 1) 파일에서 컨피그맵 생성
- 설정 파일 자체를 컨피그맵으로 만들어서 사용하는 방법

### 실습용 파일 생성: `nginx.conf`
```nginx
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }
    }
}
```

### 파일을 이용한 컨피그맵 생성
```bash
kubectl create configmap nginx-config --from-file=nginx.conf
```

### 컨피그맵 확인
```bash
kubectl describe configmap nginx-config
```
- `Data` 섹션에 `nginx.conf`라는 키로 파일 내용이 저장되어 있음

### 파드에서 컨피그맵 마운트: `nginx-config-pod.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-config-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
    volumeMounts:
    - name: config-volume
      mountPath: /etc/nginx/nginx.conf
      subPath: nginx.conf
  volumes:
  - name: config-volume
    configMap:
      name: nginx-config
```
- `subPath`를 사용하면 디렉토리 전체가 아닌 특정 파일만 덮어쓰거나 추가할 수 있음

## 2) 환경 변수 정의 파일을 이용한 생성
- 여러 개의 환경 변수를 파일에 정의하고 한 번에 컨피그맵으로 생성

### 환경 변수 파일 생성: `env.txt`
```text
DB_URL=jdbc:mysql://localhost:3306/mydb
DB_USER=admin
DB_PASS=1234
```

### 컨피그맵 생성
```bash
kubectl create configmap db-config --from-env-file=env.txt
```

### 컨피그맵 확인
```bash
kubectl get configmap db-config -o yaml
```
- `Data` 섹션 하위에 각 행이 Key-Value 쌍으로 등록됨

## 3) 리터럴 값을 이용한 생성
- 명령행에서 직접 Key-Value를 지정
```bash
kubectl create configmap literal-config --from-literal=APP_COLOR=blue --from-literal=APP_MODE=prod
```

# 2. Secret

## 1) 개요
- 비밀번호, OAuth 토큰, ssh 키와 같은 민감한 정보를 저장하기 위한 리소스
- 구조적으로는 컨피그맵과 유사하지만, 데이터가 **Base64**로 인코딩되어 저장됨
- 노드에 저장될 때 암호화되어 저장되지는 않으며(기본 설정), 파드에 마운트될 때 메모리 기반 파일 시스템(tmpfs)을 사용해 디스크 노출을 최소화함

## 2) 생성 및 확인

### 생성
```bash
kubectl create secret generic my-secret --from-literal=username=admin --from-literal=password=1234
```

### 확인
```bash
kubectl get secret my-secret -o yaml
```
- 데이터 값이 인코딩되어 보임 (예: `password: MTIzNA==`)

### 디코딩 확인
```bash
echo 'MTIzNA==' | base64 --decode
```

## 3) 사용 방법
- 컨피그맵과 동일하게 환경 변수나 볼륨 마운트로 사용 가능

### 환경 변수 참조
```yaml
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: my-secret
      key: password
```
