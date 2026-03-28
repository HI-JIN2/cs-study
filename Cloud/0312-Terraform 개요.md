**IaC**

# 1. Terraform 개요

## 1) IaC (Infrastructure as Code)
- 코드를 통해 인프라를 구축, 관리, 프로비저닝하는 방식
- **장점**: 자동화, 버전 관리 가능, 재사용성, 휴먼 에러 방지, 가시성 확보

## 2) Terraform 특징
- HashiCorp에서 만든 오픈소스 IaC 도구
- **선언적 언어 (HCL)**: 원하는 최종 상태를 정의하면 테라폼이 이를 달성하기 위한 계획을 세움
- **이종 클라우드 지원**: AWS, Azure, GCP 등 다양한 프로바이더 지원
- **상태 관리 (State)**: 현재 인프라의 상태를 `terraform.tfstate` 파일에 기록하여 관리

## 3) Terraform 구성 요소
- **Provider**: 테라폼이 협업할 대상 (예: `aws`, `google`)
- **Resource**: 관리할 인프라 객체 (예: `aws_instance`, `google_compute_instance`)
- **Data Source**: 기존에 생성된 인프라 정보를 가져올 때 사용
- **Variable**: 재사용성을 높이기 위한 변수 정의
- **Output**: 실행 후 결과값을 출력 (예: 인스턴스의 IP 주소)

## 4) Terraform 실행 생명주기
- **terraform init**: 초기화 (프로바이더 플러그인 다운로드)
- **terraform plan**: 실행 계획 확인 (변경 사항 예측)
- **terraform apply**: 실제 인프라 생성 및 변경 적용
- **terraform destroy**: 관리 중인 모든 인프라 삭제

## 5) 설치
- 공식 홈페이지에서 바이너리 다운로드 후 경로 설정
- 또는 패키지 매니저 사용 (`brew install terraform` 등)
