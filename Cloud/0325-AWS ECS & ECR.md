**AWS**

# 1. AWS ECS & ECR

## 1) ECR (Amazon Elastic Container Registry)
- 안전하고 확장 가능하고 신뢰할 수 있는 AWS 관리형 컨테이너 이미지 레지스트리 서비스
- **Amazon S3**를 기반으로 도커 이미지를 관리하므로 고가용성을 보장함
- AWS IAM 인증을 통해 이미지 `push`/`pull` 권한을 세밀하게 제어 가능
- 프라이빗(Private) 및 퍼블릭(Public) 리포지토리 모두 사용 가능

## 2) ECS (Amazon Elastic Container Service)
- 도커 컨테이너를 지원하는 고도로 확장 가능하고 빠른 컨테이저 관리 서비스
- 자체 클러스터 관리 인프라를 설치, 운영 및 확장할 필요 없이 컨테이너 기반 애플리케이션 실행 가능

### 실행 모델
- **Fargate**: 서버리스(Serverless) 방식. 인프라 관리가 필요 없으며 컨테이너 단위로 실행 환경 제공
- **EC2**: 사용자가 직접 관리하는 EC2 인스턴스 클러스터에서 컨테이너 실행

### 주요 구성 요소
- **Task Definition**: 애플리케이션을 정의하는 JSON 템플릿 (이미지, CPU/Memory, 포트 매핑 등)
- **Task**: Task Definition을 실행한 실제 인스턴스 (쿠버네티스의 Pod와 유사)
- **Service**: 클러스터 내에서 지정된 수의 태스크를 유지하고 관리하는 서비스 (쿠버네티스의 Deployment와 유사)

## 3) GitHub Actions를 이용한 ECS 배포 자동화
- 소스 코드 변경 시 이미지를 빌드하여 ECR에 푸시하고, ECS 서비스의 태스크 정의를 업데이트하여 자동 배포 수행

### 워크플로우 예시
```yaml
- name: Build, tag, and push image to Amazon ECR
  id: build-image
  env:
    ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
    IMAGE_TAG: ${{ github.sha }}
  run: |
    docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
    docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
    echo "image=$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG" >> $GITHUB_OUTPUT

- name: Fill in the new image ID in the Amazon ECS task definition
  id: task-def
  uses: aws-actions/amazon-ecs-render-task-definition@v1
  with:
    task-definition: ${{ env.ECS_TASK_DEFINITION }}
    container-name: ${{ env.CONTAINER_NAME }}
    image: ${{ steps.build-image.outputs.image }}

- name: Deploy Amazon ECS task definition
  uses: aws-actions/amazon-ecs-deploy-task-definition@v1
  with:
    task-definition: ${{ steps.task-def.outputs.task-definition }}
    cluster: ${{ env.ECS_CLUSTER }}
    service: ${{ env.ECS_SERVICE }}
    wait-for-service-stability: true
```

## 4) 도메인 및 HTTPS 연결
- **Route 53**: 탄력적인 DNS 서비스 (도메인 등록 및 관리)
- **ACM (AWS Certificate Manager)**: SSL/TLS 인증서 발급 및 갱신 지원
- 로드밸런서(ALB)에 인증서를 등록하여 HTTPS(443) 리스너 추가 구성 가능
