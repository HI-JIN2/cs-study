**AWS**

# 1. AWS IAM & S3

## 1) IAM (Identity and Access Management)
- AWS 리소스에 대한 접근 권한을 안전하게 제어하는 서비스
- **사용자 (User)**: 실제 사람이나 서비스 (Access Key, Secret Key 부여 가능)
- **그룹 (Group)**: 사용자들의 집합 (권한 관리 용이)
- **역할 (Role)**: 특정 서비스나 사용자에게 임시로 권한을 부여 (예: EC2 인스턴스가 S3에 접근할 때 사용)
- **정책 (Policy)**: 권한을 정의하는 JSON 문서 (Allow/Deny, Action, Resource)

## 2) S3 (Simple Storage Service)
- 업계 최고 수준의 확장성, 데이터 가용성, 보안 및 성능을 제공하는 객체 스토리지 서비스
- **Bucket**: 객체를 저장하는 최상위 컨테이너 (이름은 전 세계에서 유일해야 함)
- **Object**: 저장되는 데이터 (ID, Value, Metadata로 구성)

## 3) S3 스토리지 클래스
- **Standard**: 범용 (자주 접근하는 데이터)
- **Intelligent-Tiering**: 데이터 접근 패턴에 따라 비용 자동 최적화
- **Standard-IA**: 자주 접근하지 않지만 필요 시 빠른 액세스 (Standard-Infrequent Access)
- **Glacier**: 아카이빙용 (비용이 매우 저렴하지만 가져오는 데 시간 소요)

## 4) 실습: S3 버킷 생성 및 정적 웹 사이트 호스팅
```bash
# AWS CLI를 이용한 파일 업로드
aws s3 cp index.html s3://my-unique-bucket-name/
```
- **정적 웹 사이트 호스팅**: S3 버킷을 웹 서버처럼 사용하여 HTML, CSS, JS 파일을 서빙 가능
- **권한 설정**: 외부에서 접근 가능하도록 버킷 정책 설정 필요
