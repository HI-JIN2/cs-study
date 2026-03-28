**Jenkins**

# 1. Jenkins 설치 및 설정

## 1) 개요
- 지속적 통합(CI)과 지속적 배포(CD)를 자동화하기 위한 오픈소스 자동화 서버
- 수많은 플러그인을 통해 다양한 도구(Git, Docker, Kubernetes, Ansible 등)와 통합 가능

## 2) 도커를 이용한 Jenkins 설치
- 호스트의 도커 리소스를 사용하기 위해 도커 소켓을 마운트하는 방식 (DooD: Docker out of Docker)

### `docker-compose.yaml` 작성
```yaml
version: '3'
services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    user: root
    ports:
      - "8081:8080"
      - "50000:50000"
    volumes:
      - ./jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
```

### 실행
```bash
docker-compose up -d
```

### 초기 설정
- 브라우저에서 `localhost:8081` 접속
- 초기 비밀번호 확인: `docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword`
- "Install suggested plugins" 선택
- 관리자 계정 생성

### 필수 플러그인 설치
- **Manage Jenkins** -> **Manage Plugins** -> **Available**
- Pipeline
- Docker
- Docker Pipeline
- Kubernetes
- Blue Ocean (시각화 도구)

## 3) Jenkins 관리 및 자격 증명 (Credentials)
- 외부 서비스(GitHub, Docker Hub, Kubernetes 등)에 접근하기 위해 아이디/비밀번호나 토큰 등을 저장

### 등록 방법
- **Manage Jenkins** -> **Manage Credentials** -> **Global credentials** -> **Add Credentials**
- **Username with password**: 아이디/비밀번호 혹은 토큰
- **Secret text**: API 토큰 등

## 4) Blue Ocean
- 기존 젠킨스 UI의 복잡함을 개선한 현대적인 시각화 도구
- 파이프라인의 각 단계(Stage)와 성공/실패 여부를 직관적으로 확인 가능
- **Open Blue Ocean** 링크를 클릭하여 실행
