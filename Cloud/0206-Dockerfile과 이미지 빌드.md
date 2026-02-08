# Dockerfile과 이미지 빌드 (0206)

## 1. Dockerfile 개요

### 1) IaC (Infrastructure as Code)
*   **정의**: 인프라 구성을 코드로 관리하는 것.
*   **장점**:
    *   버전 관리(Git) 가능.
    *   반복적인 이미지 생성 자동화.
    *   누가 빌드하더라도 동일한 결과 보장 (일관성).

### 2) Dockerfile이란?
도커 이미지를 생성하기 위한 설정 파일(스크립트)입니다. 도커는 이 파일에 나열된 명령어를 순서대로 실행하여 이미지를 빌드합니다.

## 2. 주요 Dockerfile 명령어 (Instruction)

### 1) `FROM`
*   **기능**: 베이스 이미지 지정. 모든 Dockerfile의 시작.
*   **예시**: `FROM ubuntu:20.04`, `FROM python:3.9`

### 2) `RUN`
*   **기능**: 이미지를 생성하는 과정에서 쉘 명령어 실행 (패키지 설치, 파일 다운로드 등).
*   **예시**: `RUN apt-get update && apt-get install -y nginx`
*   **참고**: `RUN` 명령 하나당 하나의 레이어가 생성되므로, `&&`를 사용하여 명령어를 묶는 것이 이미지 크기 최적화에 유리합니다.

### 3) `CMD` vs `ENTRYPOINT`
*   **공통점**: 컨테이너가 시작될 때 실행할 명령어 지정.
*   **`CMD`**: `docker run` 시 인자가 전달되면 CMD 내용은 무시됨(덮어씌워짐). 기본값 설정용.
    *   예: `CMD ["python", "app.py"]`
*   **`ENTRYPOINT`**: `docker run` 시 인자가 전달되어도 실행되며, 인자는 명령 뒤에 추가됨. 항상 실행되어야 하는 프로세스용.
    *   예: `ENTRYPOINT ["nginx", "-g", "daemon off;"]`

### 4) `COPY` vs `ADD`
*   **기능**: 호스트의 파일을 이미지 내부로 복사.
*   **`COPY`**: 단순 파일/폴더 복사 (권장).
    *   `COPY src/ /app/src/`
*   **`ADD`**: URL 다운로드 및 압축 해제 기능 포함. 명확성을 위해 복잡한 작업 외에는 COPY 권장.

### 5) `ENV`
*   **기능**: 환경 변수 설정. 컨테이너 런타임에서도 유효.
*   **예시**: `ENV MYSQL_ROOT_PASSWORD=1234`

### 6) `EXPOSE`
*   **기능**: 컨테이너가 수신 대기할 포트 명시 (문서화 용도, 실제 포트 개방은 `docker run -p` 필요).
*   **예시**: `EXPOSE 80`

### 7) `WORKDIR`
*   **기능**: 작업 디렉토리 변경 (`cd`와 유사). 이후 명령어는 이 경로에서 실행됨.
*   **예시**: `WORKDIR /app`

## 3. 이미지 빌드 실습

### 1) 예제: 간단한 Python Flask 앱

**Dockerfile**:
```dockerfile
FROM python:3.8-slim

# 작업 디렉토리 생성
WORKDIR /app

# 의존성 파일 복사 및 설치
COPY requirements.txt .
RUN pip install -r requirements.txt

# 소스 코드 복사
COPY . .

# 환경 변수 설정
ENV FLASK_APP=app.py

# 포트 노출
EXPOSE 5000

# 실행 명령
CMD ["flask", "run", "--host=0.0.0.0"]
```

### 2) 빌드 명령
```bash
docker build -t my-flask-app:v1 .
```
*   `-t`: 이미지 이름과 태그 지정.
*   `.`: Dockerfile이 위치한 경로 (Context).

### 3) .dockerignore
*   Git의 `.gitignore`와 유사. 빌드 컨텍스트에서 제외할 파일 지정 (node_modules, .git 등).
*   **이점**: 빌드 속도 향상(전송 데이터 감소), 보안 강화(비밀 키 등 제외).
