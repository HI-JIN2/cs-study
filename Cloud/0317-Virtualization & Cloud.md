**Cloud**

# 1. Virtualization & Cloud

## 1) 가상화 (Virtualization)
- 물리적 하드웨어 리소스를 논리적으로 분할하여 효율성을 극대화하는 기술
- 하나의 서버에서 여러 개의 운영체제나 애플리케이션을 동시에 실행 가능
- **하이퍼바이저 (Hypervisor)**: 가상 머신(VM)을 생성하고 관리하는 소프트웨어 계층 (예: `KVM`, `Xen`, `VMware`)

## 2) 클라우드 컴퓨팅 개요
- IT 리소스를 필요할 때마다 인터넷을 통해 온디맨드로 제공하고 사용한 만큼 비용을 지불하는 방식
- **특징**: 민첩성, 탄력성, 비용 절감, 전 세계에 배포 가능

## 3) 클라우드 서비스 모델
- **IaaS (Infrastructure as Service)**: 인프라 수준의 서비스 (서버, 스토리지, 네트워크)
- **PaaS (Platform as Service)**: 플랫폼 수준의 서비스 (런타임, 미들웨어, OS 포함)
- **SaaS (Software as Service)**: 완성된 소프트웨어 형태의 서비스

## 4) 클라우드 배포 모델
- **Public Cloud**: 불특정 다수에게 서비스 제공 (AWS, Azure, GCP)
- **Private Cloud**: 특정 기업이나 기관 내부에서만 사용
- **Hybrid Cloud**: Public과 Private을 혼합하여 사용

## 5) AWS (Amazon Web Services)
- 전 세계에서 가장 널리 사용되는 선도적인 클라우드 플랫폼
- 수많은 서비스(EC2, S3, RDS, VPC 등)를 통해 강력한 인프라 제공
- **Region**: 지리적으로 분리된 구역 (서울 리전 등)
- **Availability Zone (AZ)**: 리전 내에 물리적으로 분리된 데이터 센터 집합
