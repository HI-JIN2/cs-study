**GitOps**

# 1. ArgoCD & GitOps

## 1) GitOps 개요
- 애플리케이션의 설정과 인프라 구성을 Git 레포지토리에서 관리하는 방식
- **신뢰할 수 있는 단일 소스 (Source of Truth)**: Git에 정의된 상태가 클러스터에 반영되어야 함
- **변경 관리**: Git의 커밋 이력을 통해 인프라와 앱의 모든 변경 사항을 추적 가능

## 2) ArgoCD
- 쿠버네티스를 위한 선언적 GitOps 지속적 배포(CD) 도구
- Git 레포지토리의 상태와 클러스터의 상태를 지속적으로 모니터링하고, 차이가 발생하면 이를 감지하여 동기화함

## 3) 설치 및 설정 (Helm)
```bash
# ArgoCD 리포지토리 추가
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update

# 네임스페이스 생성 및 설치
kubectl create namespace argocd
helm install argocd argo/argo-cd -n argocd
```

## 4) 애플리케이션 배포 및 동기화 (Sync)
- **Application 생성**: ArgoCD에서 감시할 Git 레포지토리와 대상 클러스터, 네임스페이스를 정의
- **Auto Sync**: Git에 코드가 푸시되면 ArgoCD가 자동으로 변경 사항을 클러스터에 반영
- **Drift Detection**: 클러스터 상태가 임의로 변경되었을 때(예: `kubectl edit`), Git 상태와 다름을 감지하고 경고하거나 자동으로 되돌림 (Self-Healing)

## 5) ArgoCD UI 활용
- **Dashboard**: 배포된 리소스들의 관계와 상태를 시각적으로 확인 가능
- **History & Rollback**: 배포 이력을 확인하고, 클릭 한 번으로 이전 상태로 롤백 가능

## 6) Kustomize & Helm 통합
- ArgoCD는 Kustomize와 Helm을 기본적으로 지원함
- Git 레포지토리에 `kustomization.yaml`이나 `values.yaml`이 있으면 이를 인식하여 적절하게 배포 처리
