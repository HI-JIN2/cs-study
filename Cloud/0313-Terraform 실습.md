**IaC**

# 1. Terraform 실습

## 1) 실습 준비
- 테라폼 코드 파일(`main.tf`) 작성
- AWS Access Key / Secret Key 설정 (환경 변수나 공유 자격 증명 파일 이용)

## 2) VPC 리소스 생성 실습
- **VPC**: 가상의 독립된 네트워크 공간
- **Subnet**: VPC 네트워크의 부분 집합
- **Internet Gateway**: 외부 인터넷과 통신하기 위한 관문
- **Route Table**: 트래픽을 어디로 보낼지 결정하는 표

### 테라폼 코드 작성: `main.tf`
```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "main-vpc"
  }
}

resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"
  tags = {
    Name = "public-subnet"
  }
}
```

## 3) 테라폼 실행 및 관리
```bash
# 초기화
terraform init

# 실행 계획 확인
terraform plan

# 인프라 생성 적용
terraform apply -auto-approve

# 상태 확인
terraform show
```

## 4) State 관리 및 Backend
- 테라폼은 로컬의 `terraform.tfstate` 파일에 상태를 저장함
- 팀 협업 시에는 중앙 저장소(S3, Terraform Cloud 등)를 사용하여 상태를 공유함 (Backend 설정)

## 5) Variable & Output 활용
- `variables.tf`: 변수 정의
- `outputs.tf`: 출력값 정의
- `terraform.tfvars`: 변수에 실제 값을 할당하는 파일
