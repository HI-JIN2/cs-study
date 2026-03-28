**Monitoring**

# 1. Prometheus & Grafana

## 1) Prometheus 개요
- 수집기(Collector), 시계열 데이터베이스(TSDB), 알람 엔진 등을 포함하는 모니터링 시스템
- **Pull 방식**: 대상(Target)으로부터 메트릭을 직접 가져오는 방식
- **Service Discovery**: 쿠버네티스 환경에서 동적으로 변하는 파드들을 자동으로 탐지하여 메트릭 수집 대상으로 등록

## 2) Prometheus 아키텍처
- **Prometheus Server**: 메트릭 수집 및 저장
- **Exporter**: 메트릭을 프로메테우스가 읽을 수 있는 형식으로 노출 (예: `Node Exporter`, `cAdvisor`)
- **Pushgateway**: 짧은 주기로 실행되는 배치 잡 등의 메트릭 수집을 위한 게이트웨이
- **Alertmanager**: 조건에 맞는 알람을 전송 (Slack, Email 등)

## 3) Grafana 개요
- 다양한 데이터 소스(Prometheus, InfluxDB, CloudWatch 등)를 시각화하는 오픈소스 대시보드 도구
- 프로메테우스에서 수집한 시계열 데이터를 보기 좋은 대시보드로 구성하여 모니터링 효율 극대화

## 4) 쿠버네티스에 Prometheus & Grafana 설치 (Helm)
```bash
# kube-prometheus-stack 리포지토리 추가
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# 네임스페이스 생성
kubectl create namespace monitoring

# 설치
helm install prometheus prometheus-community/kube-prometheus-stack -n monitoring
```

## 5) Grafana 대시보드 구성
- **Grafana 접속**: `kubectl port-forward`를 통해 로컬에서 접속
- **Data Source 등록**: Prometheus를 데이터 소스로 추가 (기본 설치 시 자동 등록됨)
- **대시보드 생성**: 직접 쿼리(`PromQL`)를 작성하거나, `grafana.com/dashboards`에서 미리 만들어진 대시보드 ID를 사용하여 임포트

## 6) PromQL (Prometheus Query Language)
- 프로메테우스 전용 쿼리 언어
- 예: `up{job="prometheus"}` (prometheus 작업의 가동 여부 확인)
- 예: `node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100` (메모리 사용률 계산)
