**Jenkins**

# 1. Jenkins Pipeline

## 1) 개요
- CI/CD의 전 과정을 하나의 스크립트로 정의하는 방식
- 선언형(Declarative)과 스크립트형(Scripted) 두 가지 문법이 있으나, 선언형 파이프라인이 주로 사용됨
- 파이프라인 스크립트는 `Jenkinsfile`이라는 이름의 파일로 저장되어 코드 관리 도구(Git 등)에서 함께 관리됨 (Pipeline as Code)

## 2) 파이프라인 구조 (Declarative Pipeline)

- `pipeline`: 전체 파이프라인을 감싸는 블록
- `agent`: 파이프라인이 실행될 노드나 환경 (예: `any`, `docker`, `label`)
- `stages`: 여러 `stage`를 포함하는 그룹
- `stage`: 파이프라인의 논리적인 단계 (예: `Build`, `Test`, `Deploy`)
- `steps`: 각 `stage`에서 실행될 실제 명령들의 집합
- `post`: 파이프라인이나 각 단계 완료 후 실행할 후속 작업 (예: `always`, `success`, `failure`)

### 기본 예제
```groovy
pipeline {
    agent any
    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```

## 3) 파이프라인 구축 실습

### GitHub 레포지토리 연결
- 신규 아이템 생성 -> **Pipeline** 선택
- **Pipeline** 섹션 -> **Definition**을 **Pipeline script from SCM**으로 선택
- **SCM** -> **Git** 선택
- **Repository URL** 입력 및 **Credentials** 선택

### Docker와 통합된 파이프라인
- 빌드 환경을 Docker 컨테이너 내에서 실행하거나, 애플리케이션을 Docker 이미지로 빌드하여 레지스트리에 푸시

```groovy
pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-id')
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
        stage('Docker Build & Push') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub-id') {
                        def app = docker.build("myuser/myapp:${env.BUILD_NUMBER}")
                        app.push()
                        app.push("latest")
                    }
                }
            }
        }
    }
}
```

## 4) 공유 라이브러리 (Shared Library)
- 여러 파이프라인에서 공통으로 사용되는 코드를 라이브러리화하여 재사용성을 높임
- `vars` 디렉토리에 전역 변수나 헬퍼 함수를 정의하여 사용

## 5) 환경 변수 사용 및 파라미터화된 빌드
- 파이프라인 내에서 사용할 환경 변수 정의 (`environment` 블록)
- 빌드 시 사용자로부터 입력을 받는 파라미터 정의 (`parameters` 블록)
