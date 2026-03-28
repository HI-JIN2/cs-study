**Logging**

# 1. Logging & Grafana Loki

## 1) Logging 개요
- 시스템이나 애플리케이션에서 발생하는 이벤트를 기록하고 추적하는 과정
- 쿠버네티스 환경에서는 파드가 수시로 생성되고 사라지기 때문에 로그를 중앙 집중화하여 저장하고 분석하는 것이 필수

## 2) Logging 아키텍처 (EFK Stack)
- **Elasticsearch**: 로그를 저장하고 검색하는 엔진
- **Fluentd/Fluent Bit**: 로그를 수집하여 Elasticsearch로 전송하는 에이전트
- **Kibana**: 저장된 로그를 시각화하고 분석하는 도구

## 3) Grafana Loki
- 프로메테우스와 유사한 방식으로 설계된 로그 수집 및 분석 시스템
- **특징**: 전체 로그를 인덱싱하지 않고 가벼운 메타데이터(레이블)만 인덱싱하여 리소스 효율이 좋음
- **구성 요소**:
  - **Loki**: 로그 저장 및 쿼리 처리
  - **Promtail**: 각 노드에서 로그를 수집하여 Loki로 전송하는 에이전트
  - **Grafana**: Loki를 데이터 소스로 사용하여 로그 시각화

## 4) 쿠버네티스에 Grafana Loki 설치 (Helm)
```bash
# Grafana 리포지토리 추가
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# 설치
helm install loki-stack grafana/loki-stack -n monitoring --set promtail.enabled=true,loki.persistence.enabled=true,loki.persistence.size=10Gi
```

## 5) Grafana에서 Loki 활용
- **Data Source**: Loki를 데이터 소스로 추가 (기본 URL: `http://loki-stack:3100`)
- **Explore**: `LogQL`을 사용하여 로그를 조회하고 대시보드에 추가 가능

## 6) LogQL (Log Query Language)
- Loki 전용 쿼리 언어
- 예: `{app="my-app"}` (특정 레이블의 로그 조회)
- 예: `{app="my-app"} |= "error"` (특정 키워드 필터링)
