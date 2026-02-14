# Docker Compose와 Health Check

## 1. Docker Compose

### 1) docker-compose

#### 개요
- 분산 애플리케이션을 기준으로 보면 Dockerfile은 애플리케이션의 한 부분을 패키징하는 수단
- 애플리케이션을 개발하다보면 3-Tier(FrontEnd, BackEnd API, Persistent Store)로 많이 개발을 하는데 이렇게 만들어진 애플리케이션을 패키징하려면 각 컴포넌트에 하나씩 3개의 Dockerfile이 필요하고 순서대로 각각의 컨테이너를 생성해야 하는데 이런 수동 프로세스는 실수와 오류의 원천이 됨
- 직접 실행하는 과정에서 실수를 하게되면 애플리케이션이 정상적으로 동작하지 않거나 컨테이너 간 통신에 문제가 생길 수 있음
- 공통의 목적을 갖는 애플리케이션 스택을 yaml 코드로 정의해서 한 번에 서비스를 올리고 관리할 수 있는 도구가 필요한데 이 도구가 docker-compose
- docker compose 파일은 애플리케이션의 원하는 상태 즉 모든 컴포넌트가 실행 중일 때 어떤 상태여야 하는지를 기술하는 파일
- docker container run 명령으로 컨테이너를 실행할 때 지정하는 모든 옵션을 한데 모아놓은 형식의 파일
- docker-compose 파일을 작성하고 docker-compose 도구를 이용해서 애플리케이션을 수행
- docker-compose가 컨테이너, 네트워크, 볼륨 등 필요한 모든 docker 객체를 만들도록 docker API에 명령을 내림
- docker-compose로 실행된 컨테이너는 독립된 기능을 가지며 공통 네트워크로 구성되기 때문에 컨테이너 간 통신이 쉬움
- 하나의 네트워크로 묶을 수 있기 때문에 불필요하게 외부로 노출시킬 필요가 없습니다
- docker-compose는 테스트, 개발, 운영의 모든 환경에서 구성이 가능한 오케스트레이션 도구 중 하나이지만 다양한 관리 기능을 갖고 있지 않기 때문에 테스트와 개발 환경에 적합
- 실제 운영 환경은 많은 관리적 요소가 필요하기 때문에 도커 스웜이나 쿠버네티스 같은 오케스트레이션 도구가 가지고 있는 Auto Scaling, Monitoring, Recovery 등의 운영에 필요한 기능과 함께 사용하는 것을 권장
- yaml 포맷으로 기술된 설정 파일을 이용해서 여러 Container간의 실행이나 관계를 설정
- 시스템을 일괄 실행 또는 일괄 종료 및 삭제할 수 있는 도구
- 컨테이너나 볼륨을 어떠한 설정으로 만들지에 대한 항목이 기재되어 있는데 내용은 docker 명령어와 비슷하지만 docker 명령어는 아님
- up 명령어가 docker run 커맨드와 유사해서 정의 파일에 기재된 내용대로 이미지를 내려받고 컨테이너를 생성 및 실행하는데 정의 파일에는 네트워크가 볼륨에 대한 정의도 기재할 수 있어서 주변 환경을 한꺼번에 생성할 수 있음
- down 명령어는 컨테이너와 네트워크를 정지 및 삭제하는 명령인데 볼륨과 이미지는 삭제하지 않으며 컨테이너와 네트워크를 삭제하지 않고 종료만 할 때는 stop 명령을 사용

#### docker compose와 Dockerfile
- docker compose는 docker run 명령을 여러 개 모아놓은 것과 유사해서 컨테이너와 주변 환경을 생성하며 네트워크과 볼륨까지 함께 만들 수 있음
- Dockerfile 스크립트는 이미지를 만들기 위한 것으로 네트워크가 볼륨을 만들 수는 없음

### 2) 설치

#### Mac이나 Windows에서 Docker Desktop을 이용하는 경우
별도의 설치 과정이 없음

#### Ubuntu Linux에서는 설치를 해야 함
```bash
# git hub에서 다운로드
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 권한 변경(실행 권한 부여)
sudo chmod +x /usr/local/bin/docker-compose

# 링크 생성
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

# 소유자 변경
sudo chown 계정 /usr/local/bin/docker-compose

# 확인
docker-compose --version
```

### 3) docker-compose 작성 및 실행

#### apache webserver 컨테이너 생성

**docker run 명령 사용**
```bash
docker run --name apachewebserver -d -p 8080:80 httpd
```

**docker compose로 컨테이너 생성**

`docker-compose.yaml` 파일 작성:
```yaml
version: "3.8"
services:
  apachewebserver:
    image: httpd
    ports:
      - 8081:80
    restart: always
```

```bash
# 컨테이너 생성
docker-compose up -d

# 확인
docker-compose ps

# 컨테이너 삭제
docker-compose down
```

#### maria db 컨테이너 생성

**docker run 명령으로 생성**
```bash
docker run -d --name mariadb-server -p 3306:3306 -e MYSQL_ROOT_PASSWORD=wnddkd -v ~/mariadb/data:/var/lib.mysql --restart always mariadb:10.4.6
```

**docker-compose로 생성**

`docker-compose.yaml`:
```yaml
version: "3.8"
services:
  mariadb:
    image: mariadb:10.4.6
    container_name: mariadb-server
    restart: always
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: wnddkd
      MYSQL_DATABASE: my_database
      MYSQL_USER: my_user
      MYSQL_PASSWORD: wnddkd
    volumes:
      # 데이터 연결
      - ./data:/var/lib/mysql
      # 초기 설정 파일이 있으면 연결
      - ./init:/docker-entrypoint-initdb.d
```

```bash
# 서비스 생성
docker-compose up -d

# 컨테이너 확인
docker-compose ps
docker ps
```

> **주의**: 볼륨을 연결할 때 연결하는 디렉토리에 파일이 존재하면 서비스가 생성되지 않음

```bash
# 컨테이너에 접속
docker exec -it mariadb-server bash

# mysql 클라이언트 실행
mysql -uroot -p

# 데이터베이스 확인
show databases;

# 데이터 삽입 작업 수행

# 컨테이너 안에서 데이터베이스 파일 확인
ls -l /var/lib/mysql/my_database

# 호스트 컴퓨터에서 확인
ls -l ./data/my_database

# 서비스 삭제
docker-compose down
```

### 4) 작성 방법

#### 파일 이름
- `docker-compose.yaml`
- `docker-compose.yml`
- `compose.yaml`
- `compose.yml`

#### 주항목
- version
- services
- networks
- volumes

#### service의 정의 항목
- `image`
- `networks`: `--net`
- `volumes`: `-v`
- `ports`: `-p`
- `environment`: `-e`
- `depends_on`: 다른 서비스에 대한 의존 관계
- `restart`: 컨테이너 종료시 재시작 여부(no, always, on-failure, unless-stopped)

#### 버전 정의
- yaml 파일의 첫 줄에 정의
- 최근에는 3.8 정도를 사용하면 됩니다

#### 서비스 정의
- docker compose를 통해 실행할 서비스를 정의
- docker compose는 컨테이너 대신 서비스 개념을 사용
- 상위의 version 명령과 동일 레벨로 작성되며 다중 컨테이너 서비스 실행을 목적으로 하기 때문에 복수형으로 작성되는 것에 대해 유의
- docker compose로 함께 실행된 모든 서비스는 docker compose 명령을 통해서 통합 관리
- services 하위에는 실행될 컨테이너 서비스를 작성하고 하위 레벨에 docker run 명령에서 사용했던 옵션들을 유사하게 작성
- 프로젝트에서 별도 이미지 개발없이 도커 허브에서 제공하는 이미지를 사용할 때는 `image: 이미지이름[:태그]`와 같이 작성
- Dockerfile이 있는 경우에는 build 속성에 파일의 경로를 설정하면 됩니다
- Dockerfile의 위치가 docker-compose와 다른 경우에는 context를 설정합니다

```yaml
build:
  context: .
  dockerfile: Dockerfile의 경로
```

- `container_name`: docker run 명령의 `--name`과 동일한 명령으로 생략 시 `디렉토리이름_서비스이름_숫자`로 자동으로 생성

- `ports`: `-p` 옵션과 동일합니다

```yaml
ports: [3306:3306, 1606:1506]

# 또는
ports:
  - 3306:3306
  - 1521:1521
```

- `expose`: 호스트 운영체제와 직접 연결하는 포트를 구성하지 않고 포트를 노출하는데 필요하면 링크로 연결된 서비스와 서비스간의 통신만 허용

- `networks`: 네트워크에 연결

- `volumes`: 볼륨에 연결

- `environment`: `-e` 옵션인데 여러개 작성이 가능한데 하위 항목에 설정을 하는데 배열이 아닙니다

- `command`: docker run의 마지막에 작성하는 명령어로 서비스가 구동된 이후 실행할 명령어를 작성

- `restart`: 재시작 옵션으로 no를 설정하면 수동으로 재시작하고 always를 설정하면 항상 재시작하고 on-failure를 설정하면 오류가 있을 때 재시작

- `depends_on`: 서비스 간의 종속성을 의미하는데 먼저 실행해야 하는 서비스를 지정해서 순서를 정하는데 이 옵션에 지정된 서비스가 먼저 시작됨

#### 네트워크 정의
- 다중 컨테이너들이 사용할 최상위 네트워크 키를 정의하고 이하 하위 서비스 단위로 네트워크를 선택할 수 있습니다
- networks 옵션을 지정하지 않으면 자체 기본 네트워크를 자동으로 생성해서 할당
- 최상위 레벨에 networks를 지정하면 해당 이름의 네트워크가 생성되고 대역은 172.x.x.x로 할당되면 기본 드라이버는 브릿지로 설정
- 이미 생성한 네트워크를 사용하고자 할 때는 external 하위 항목에 name을 설정

```yaml
services:

networks:
  default:
    external:
      name: vswitch-ap
```

#### 볼륨 정의
- 데이터의 지속성을 위해서 최상위 레벨에 볼륨을 정의하고 서비스 레벨에서 볼륨명과 서비스의 내부 디렉토리를 바인드해서 사용
- `docker volume ls` 명령으로 확인이 가능

### 5) docker 명령어와 docker compose 코드 비교

#### 데이터베이스는 mysql:8.0을 사용하고 웹은 wordpress:5.7 이미지를 이용한 웹 애플리케이션 서비스를 구성

**docker 명령어로 수행**

```bash
# 2개의 볼륨 생성
docker volume create mydb_data
docker volume create myweb_data
docker volume ls

# network를 생성
docker network create myapp-net
docker network ls
docker network inspect myapp-net

# 컨테이너 생성
docker run -dit --name=mysql_app -v mydb_data:/var/lib/mysql --restart=always -p 3306:3306 --net=myapp-net -e MYSQL_ROOT_PASSWORD=wnddkd -e MYSQL_DATABASE=adam -e MYSQL_USER=adam -e MYSQL_PASSWORD=wnddkd mysql:8.0

docker run -dit --name=wordpress_app -v myweb_data:/var/www/html -v ${PWD}/myweb-log:/var/log --restart=always -p 8888:80 --net=myapp-net -e WORDPRESS_DB_HOST=mysql_app:3306 -e WORDPRESS_DB_NAME=adam -e WORDPRESS_DB_USER=adam -e WORDPRESS_DB_PASSWORD=wnddkd --link mysql_app:mysql wordpress:5.7

# 컨테이너 확인
docker ps

# 웹 브라우저에서 확인: 8888번 포트

# 정리
# 컨테이너 정지
docker stop $(docker ps -aq)

# 컨테이너 삭제
docker rm $(docker ps -aq)

# 불필요한 자원 삭제
docker system prune
```

**docker compose 활용**

`docker-compose.yaml` 파일 작성:
```yaml
version: "3.8"
services:
  mydb:
    image: mysql:8.0
    container_name: mysql_app
    volumes:
      - mydb_data:/var/lib/mysql
    restart: always
    ports:
      - "13306:3306"
    networks:
      - backend-net
    environment:
      MYSQL_ROOT_PASSWORD: wnddkd
      MYSQL_DATABASE: adam
      MYSQL_USER: adam
      MYSQL_PASSWORD: wnddkd
  myweb:
    # myweb 서비스를 만들기 전에 mydb 서비스를 생성하도록 설정
    depends_on:
      - mydb
    image: wordpress:5.7
    container_name: wordpress_app
    ports:
      - "8888:80"
    networks:
      - backend-net
      - frontend-net
    volumes:
      - myweb_data:/var/www/html
      - ${PWD}/myweb-log:/var/log
    restart: always
    environment:
      WORDPRESS_DB_HOST: mydb:3306
      WORDPRESS_DB_USER: adam
      WORDPRESS_DB_PASSWORD: wnddkd
      WORDPRESS_DB_NAME: adam

networks:
  frontend-net: {}
  backend-net: {}

volumes:
  mydb_data: {}
  myweb_data: {}
```

```bash
# 서비스 생성
docker-compose up -d

# 서비스 확인
docker-compose ps
docker ps

# 외부에서 확인: 8888번 포트로 접속

# 서비스 중지
docker-compose down
```

### 6) 컨테이너 간의 통신

#### 외부에서 도커 내부의 컨테이너에 접속
외부에서 도커 내부의 컨테이너에 접속하기 위해서는 도커 컨테이너를 생성할 때 port forwarding을 수행해서 접근

#### 하나의 도커 엔진에 만들어진 컨테이너 간의 접근
하나의 도커 엔진에 만들어진 컨테이너 간에는 도커 엔진에서 부여한 IP를 이용해서 접근 가능: `docker inspect network bridge` 명령으로 IP 확인 가능

#### 플라스크라는 웹 프레임워크로 웹 페이지를 만들고 redis 인메모리 데이터베이스에서 웹 페이지 액세스 카운트를 캐시해서 페이지에 출력하는 애플리케이션을 생성

```bash
# 레디스 컨테이너 실행
docker run --name myredis -d -p 6379:6379 redis

# 파이썬 가상환경 생성
python3 -m venv ./myvenv
source myvenv/bin/activate

# 패키지 설치
pip install flask
pip install redis

# 패키지 정보 내보내기
pip freeze > requirements.txt
```

`app.py` 파일 작성:
```python
import time
import redis
from flask import Flask

app = Flask(__name__)

def web_hit_cnt():
  with redis.StrictRedis(host='localhost', port=6379) as con:
    return con.incr("hits")

@app.route("/")
def hello():
  cnt = web_hit_cnt()
  return """<h1 style="text-align:center; color:balck;">docker-compose application:
            Flask & Redis</h1>
            <p>container service</p><p>Web access count:{}</p>""".format(cnt)

if __name__ == '__main__':
  app.run(host='0.0.0.0', port=9000, debug=True)
```

```bash
# 실행
python app.py

# 확인
curl localhost:9000
```

#### 만들어진 웹 애플리케이션을 도커 이미지로 만들기 위한 Dockerfile 생성해서 컨테이너로 실행

`Dockerfile` 작성:
```dockerfile
FROM python:3.10-slim
ENV LIBRARY_PATH=/lib:/usr/lib
ENV FLASK_APP=py_app
ENV FLASK_ENV=development

EXPOSE 9000
WORKDIR /py_app
COPY . .
RUN pip install -r requirements.txt
ENTRYPOINT ["python"]
CMD ["app.py"]
```

```bash
# 이미지 빌드
docker build -t flaskapp .

# 컨테이너 실행
docker run -dit -p 9000:9000 --name=flaskapp flaskapp
```

웹에서 접속하면 에러가 발생:
- 도커 컨테이너는 하나의 독립된 컴퓨터로 간주하는데 redis는 다른 컨테이너인데 localhost로 접근할려고 해서 에러가 발생
- 접속 위치를 수정해야 합니다
- `docker inspect network bridge`를 이용해서 IP를 수정해야만 정상 수행이 됩니다

#### docker-compose를 이용한 실행

```bash
# 정리
docker stop flaskapp
docker rm flaskapp
docker rmi flaskapp

docker stop myredis
docker rm myredis
docker rmi myredis
```

`docker-compose.yaml` 파일 생성:
```yaml
version: "3.8"
services:
  redis:
    image: redis:latest
    ports:
      - 6379:6379
    restart: always

  flask:
    build: .
    ports:
      - 9000:9000
    depends_on:
      - redis
    restart: always
```

`app.py` 파일에서 redis 접속위치에서 host를 서비스 이름인 redis로 수정

```bash
# 실행
docker-compose up -d
```

### 7) docker-compose 명령

#### 명령어
- `build`: Dockerfile 빌드 또는 재빌드
- `config`: docker-compose 파일의 내용 확인
- `create`: 서비스 생성
- `down`: 일괄 정지 후 삭제
- `event`: 이벤트 정보 수신
- `exec`: 실행 중인 컨테이너에 명령 실행
- `images`: 이미지 정보
- `kill`: 컨테이너 강제 정지
- `logs`: 로그 정보 출력
- `pause`: 일시 정지
- `port`: 포트 출력
- `ps`: 실행 중인 서비스 출력
- `pull`: 이미지 가져오기
- `push`: 이미지 업로드
- `restart`: 컨테이너 서비스 재시작
- `rm`: 정지된 서비스 제거
- `run`: 실행 중인 컨테이너에 일회성 명령어 실행
- `scale`: 컨테이너 수를 설정
- `start`: 서비스 시작
- `stop`: 서비스 중지
- `top`: 실행 중인 프로세스 출력
- `unpause`: 정지된 컨테이너 서비스 재시작
- `up`: 컨테이너 서비스 생성과 시작
- `version`: 버전

#### 옵션
- `d`: 백그라운드 실행
- `f`: 파일 경로 설정
- `scale 서비스이름=개수`: 서비스 개수 설정

#### 컨테이너 생성

별도의 디렉토리에서 작업

`docker-compose.yaml` 작성:
```yaml
version: '3.8'
services:
  server_web:
    image: httpd:2
  server_db:
    image: redis
```

```bash
# 배포
docker-compose up -d

# 서비스 확인
docker-compose ps

# 서비스 중지 및 삭제
docker-compose down

# server_web과 server_db를 3개씩 배포
docker-compose up --scale server_web=3 --scale server_db=3 -d

# 서비스 확인
docker-compose ps
```

#### 서비스 삭제
```bash
docker-compose [-f 파일경로] down [옵션]
```

옵션:
- `--rmi 이미지`: 이미지도 삭제
- `--volumes 볼륨이름`: 볼륨도 삭제

### 8) Load Balancer

#### 개요
- 서버에 가해지는 부하를 분산해주는 장치 또는 기술
- 클라이언트와 서버 풀 사이에 위치하며 한 대의 서버로 부하가 집중되지 않도록 트래픽을 관리해 각각의 서버가 최적의 퍼포먼스를 보일 수 있도록 해주는 기술
- 리소스 활용도를 최적화하고 처리량을 최대화하며 지연 시간을 줄이고 내결함성 구성을 보장하기 때문에 안정적인 시스템 운영에 도움이 됩니다
- docker는 HaProxy, Nginx/Apache Load Balancer 등 외부 서비스와 컨테이너를 결합한 Load Balancing 기능 구현이 가능

#### 연결 알고리즘
- **Round Robin**: 순서대로
- **Least Connections**: 연결된 클라이언트 수가 가장 적은 곳으로
- **IP Hash**: IP를 기준으로 해싱해서 연결
- **General Hash**: 사용자가 키를 설정(IP, Port, URI 문자열 등)
- **Least Time**: 지연 시간이 낮은 것을 이용(헤더, 마지막 바이트, 전체 응답-에러를 포함 등)
- **Random**

#### 종류
- L4 LoadBalancer
- L7 LoadBalancer

#### nginx Load Balancing 관련 파라미터
- `weight`: 가중치로 기본값은 1
- `max_conns`, `queue`: 최대 클라이언트 연결 수 지정
- `max_fails`: 최대 실패 횟수를 설정하는 것인데 임계치 도달하면 해당 서버를 분배 대상에서 제외(drain)
- `fail_timeout`: 응답 최소 시간 설정
- `backup`: 이 서버는 평상시에는 동작하지 않고 모든 서버가 동작하지 않을 때 사용
- `down`: 기본적으로 사용하지 않지만 ip_hash인 경우 사용

#### nginx를 이용한 Load Balancer 구축

```bash
# 디렉토리 생성
mkdir alb
cd alb
mkdir nginx_alb pyfla_app1 pyfla_app2 pyfla_app3
```

**nginx 설정**

디렉토리 이동:
```bash
cd nginx_alb
```

`Dockerfile` 작성:
```dockerfile
FROM nginx
RUN rm /etc/nginx/conf.d/default.conf  # 기본 설정 파일을 삭제
COPY nginx.conf /etc/nginx/conf.d/default.conf  # 직접 작성한 설정 파일을 nginx 설정 파일로 사용
```

nginx 설정 파일 작성: `nginx.conf`
```nginx
upstream web-alb {
  server 172.17.0.1:5001;
  server 172.17.0.1:5002;
  server 172.17.0.1:5003;
}

server {
  listen 80;
  location / {
    proxy_pass http://web-alb;
  }
}
```

**첫번째 애플리케이션 작성**

디렉토리 이동:
```bash
cd ../pyfla_app1
```

파이썬 코드 작성: `pyfla_app.py`
```python
from flask import request, Flask
import json
app = Flask(__name__)
@app.route("/")
def index():
  return "Web Application [1]" + "\n"

if __name__ == '__main__':
  app.run(debug=True, host='0.0.0.0')
```

`Dockerfile` 작성:
```dockerfile
FROM python:3
WORKDIR /
COPY . .
RUN pip install flask
ENTRYPOINT ["python3"]
CMD ["pyfla_app.py"]
```

**두번째 애플리케이션 작성**

```bash
# 2개의 파일 복사
cp pyfla_app.py Dockerfile ../pyfla_app2

# 디렉토리 이동
cd ../pyfla_app2
```

웹 파일 수정: `nano pyfla_app.py`
```python
from flask import request, Flask
import json
app = Flask(__name__)
@app.route("/")
def index():
  return "Web Application [2]" + "\n"

if __name__ == '__main__':
  app.run(debug=True, host='0.0.0.0')
```

**세번째 애플리케이션 작성**

```bash
# 2개의 파일 복사
cp pyfla_app.py Dockerfile ../pyfla_app3

# 디렉토리 이동
cd ../pyfla_app3
```

웹 파일 수정: `nano pyfla_app.py`
```python
from flask import request, Flask
import json
app = Flask(__name__)
@app.route("/")
def index():
  return "Web Application [3]" + "\n"

if __name__ == '__main__':
  app.run(debug=True, host='0.0.0.0')
```

**docker-compose로 배포**

디렉토리 이동:
```bash
cd ../
```

`docker-compose.yaml` 파일 작성:
```yaml
version: "3.8"
services:
  pyfla_app1:
    build: ./pyfla_app1
    ports:
      - "5001:5000"
  pyfla_app2:
    build: ./pyfla_app2
    ports:
      - "5002:5000"
  pyfla_app3:
    build: ./pyfla_app3
    ports:
      - "5003:5000"

  nginx:
    build: ./nginx_alb
    ports:
      - "8080:80"
    depends_on:
      - pyfla_app1
      - pyfla_app2
      - pyfla_app3
```

```bash
# 서비스 생성
docker-compose up --build -d

# 확인
curl http://localhost:8080
```

요청을 여러 번 전송하면 순서대로 접속을 합니다.

### 9) 환경 변수 삽입 및 관리

#### 환경 변수를 다루는 방법
- compose 파일에 직접 입력
- 쉘 환경 변수로 등록
- 환경 변수 파일로 구성
- Dockerfile에 환경 변수를 직접 삽입

#### compose 파일에 직접 입력하는 방식
environment 속성에 `변수명: 값` 또는 `변수명=값`의 형식으로 입력

MySQL의 MYSQL_ROOT_PASSWORD 설정:
```yaml
services:
  mysql:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: 비밀번호
```

#### 쉘 환경 변수 설정

환경 변수를 설정:
```bash
export MYSQL_ROOT_PASSWORD=wnddkd
```

사용:
```yaml
version: "3.9"
services:
  mysql:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
```

확인:
```bash
docker-compose config
```

#### compose 파일이 위치한 곳에 .env 파일을 만들어서 환경 변수를 작성하고 docker-compose up 할 때 사용

이전에 만든 환경 변수 삭제:
```bash
unset MYSQL_ROOT_PASSWORD
```

`.env` 파일을 생성하고 작성:
```
MYSQL_ROOT_PASSWORD=password
```

## 2. 헬스 체크

### 1) 개요
- 도커는 컨테이너를 시작할 때 애플리케이션의 기본적인 상태를 확인
- 컨테이너를 실행하면 내부에서 애플리케이션 실행 파일이나 자바 혹은 닷넷 런타임 또는 쉘 스크립트 같은 특정한 프로세스가 실행되는데 도커가 확인하는 것은 이 프로세스의 실행 상태인데 만약 이 프로세스가 종료되었다면 컨테이너도 종료 상태가 되는데 이것만으로도 어느 정도의 헬스 체크는 가능
- 해당 프로세스가 비정상 종료되었거나 컨테이너가 종료되었다면 개발자도 애플리케이션의 상태가 비정상임을 알 수 있음
- 클러스터 환경에서는 플랫폼이 종료된 컨테이너를 재시작하거나 새 컨테이너로 교체하는 작업을 대신 수행
- 웹 애플리케이션의 경우 실행 중인 컨테이너에서 처리 용량을 뛰어넘는 수의 요청의 들어오는 순간 웹 애플리케이션은 503 에러를 발생시키지만 컨테이너 프로세스는 여전히 정상적으로 실행 중인 상태가 됨

### 2) 헬스 체크

헬스 체크를 위한 파이썬 애플리케이션을 작성(`app.py`):
```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
  return "Hello Docker"

# 헬스체크용 End Point
@app.route("/health")
def health_check():
  return "OK", 200

if __name__ == '__main__':
  app.run(host='0.0.0.0', port=8080)
```

Health Check를 위한 `Dockerfile`을 생성:
```dockerfile
FROM python:3.9-slim

RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY app.py .
RUN pip install flask

HEALTHCHECK --interval=10s --timeout=3s --start-period=5s --retries=3 CMD curl http://localhost:8080/health || exit 1

EXPOSE 8080
CMD ["python", "app.py"]
```

```bash
# 이미지 빌드
docker build -t my-health-app .

# 컨테이너 생성
docker run -d --name health-test my-health-app

# 확인
docker ps
```

STATUS 항목 확인:
- `starting`: 헬스 체크를 기다리는 중
- `healthy`: 정상 작동 중
- `unhealthy`: 설정된 횟수만큼 헬스 체크가 실패

### 3) 헬스 체크가 중요한 이유

#### 자동 복구
Docker Swarm이나 Kubernetes 같은 오케스트레이션 도구는 unhealthy 상태의 컨테이너를 자동으로 재시작하거나 교체

#### 무중단 배포
로드밸런서와 연동할 때 새 컨테이너가 healthy가 될 때까지 트래픽을 보내지 않도록 제어할 수 있음

### 4) Docker Compose 헬스 체크

#### 기본 형식
```yaml
services:
  web:
    image: my-health-app
    ports:
      - "8080:8080"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 10s
      timeout: 5s
      retries: 3
      start_period: 5s
```

#### DB 준비 후 웹 서버를 실행하는 docker-compose.yaml
```yaml
version: "3.8"

services:
  db:
    image: postgres:15
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d mydb"]
      interval: 5s
      timeout: 5s
      retries: 5
    networks:
      - my-network

  web:
    build: .
    ports:
      - "8080:8080"
    environment:
      DB_HOST: db
    depends_on:
      db:
        condition: service_healthy
    networks:
      - my-network

networks:
  my-network:
```

실행:
```bash
docker-compose up -d
```
