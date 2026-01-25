# Server 생성

### 1) 원격 접속 서버

- **Telnet**:

- 개요
Telnet은 Telecommunication Network의 약자

인터넷이나 로컬 영역 네트워크(LAN) 연결에 사용하는 프로토콜

멀리 떨어진 컴퓨터에 가상으로 접속해서 마치 그 컴퓨터 앞에 앉아있는 것처럼 명령어를 실행할 수 있게 해줌

- 특징
텍스트 기반의 인터페이스(CLI)를 통해 원격 호스트를 제어

기본적으로 TCP 23번 포트를 사용하여 통신

플랫폼 독립적

NVT(Network Virtual Terminal): 서로 다른 시스템 환경에서도 명령어를 인식할 수 있도록 가상의 단말기 개념을 사용

- 장점
설치가 간편하고 사용법이 매우 쉬움

시스템 리소스를 적게 차지

오래된 장비(Legacy System - PC 보다는 라우터 나 스위치 같은 장비에 많이 사용) 와도 호스트 호환성이 뛰어남

- 단점
데이터가 암호화되지 않은 평문(PlainText)으로 전송되어 패킷 스니핑을 통해 정보가 유출될 위험이 큼

- 보안 문제 때문에 실제 원격 제어용으로는 SSH(Secure Shell)가 텔넷을 완전히 대체했지만 오늘날에도 엔지니어들은 가끔 사용
포트 개방 확인: 특정 서버의 특정 포트가 열려있는지 확인할 대 유용(telnet 1.2.3.4:80 형식으로 사용)

애플리케이션 테스트: HTTP, SMTP 등 텍스트 기반 프로토콜의 응답을 직접 확인하기 위해 수동으로 명령을 보낼 때 사용

- 우분투에는 클라이언트가 이미 설치되어 있음
dpkg -l | grep telnet

- **SSH**:

- 개요
SSH(Secure Sheel)는 네트워크 상의 다른 컴퓨터에 로그인하거나 원격 시스템에서 명령을 실행할 수 있게 해주는 보안 네트워크 프로토콜

Telnet 이나 FTP 등은 데이터를 평문으로 전송하기 때문에 해킹에 매우 취약하지만 SSH는 모든 통신 내용을 암호화해서 보안성을 획기적으로 높임

- 특징
강력한 암호화: 패스워드 뿐 아니라 전송되는 데이터 전체를 암호화
인증(Authentication): 접속하려는 사용자가 올바른 사용자 인지 확인
무결성(Integrity): 전송된 데이터가 중간에 위변조되지 않았음을 보장
압축: 데이터를 압축하여 전송하기 때문에 네트워크 효율이 좋음

- 동작원리
클라이언트 - 서버 모델로 동작하며 표준 포트는 22번
가장 권장되는 인증 방식은 비밀번호가 아니라 SSH Key(공개키/비밀키)를 사용하는 방식
  - public key(공개키): 서버에 저장, 누구나 봐도 무방, 데이터 암호화에 사용
  - private key(개인키): 클라이언트 보관, 유출 금지, 암호화된 데이터 복호화에 사용

- SSH의 주요 활용 사례
원격 터미널 접속: 리눅스 서버 등에 접속해서 명령어를 입력하고 관리
SFTP/SCPSSH: 보안 채널을 이용한 안전한 파일 전송
SSH 터널링: 보안이 취약한 다른 프로토콜을 SSH 안에 넣어 안전하게 전달
Git 원격 저장소: GitHub, GitLab 등에서 코드를 push 할 때 인증수단으로 사용

- SSH 서버 설치

```bash
sudo apt install -y openssh-server
```

- 서비스(ssh) 구동 및 확인
서비스 시작: sudo systemctl start ssh

리눅스 구동 과 동시에 시작: sudo systemctl enable ssh

서비스 상태 확인: sudo systemctl status ssh

- 방화벽에서 해제
```bash
sudo ufw allow 22/tcp
```

- SSH 서버에 처음 접속하는 경우 메시지가 출력되는 경우가 있음
본인이 관리하는 서버가 맞는 확인하는 절차

- SSH 키 방식 접속 설정 
접속하고자 하는 컴퓨터에서 SSH Key Pair 생성: ssh-keygen -t rsa -b 4096 -C "itstudy@kakao.com"
홈 디렉토리의 .ssh 디렉토리에 키 파일이 생성됩니다.
id_rsa 파일이 private key 파일이고 id_rsa.pub 파일이 public key 파일 입니다.

공개키를 서버에 전송: sudo apt install openssh-client
```bash
ssh-copy-id -p 포트번호 [사용자명]@[IP주소]
ssh-copy-id -p 10001 adam@192.168.201.16 명령을 입력하면 처음 한 번은 서버의 비밀번호를 입력하라고 합니다.
```

- SSH로 접속할 때 비밀번호를 묻지 않습니다.

- 접속 방식 설정 시 파일 전송을 하지 않고 설정 가능
클라이언트의 id_rsa.pub 파일의 내용을 서버의 ~/.ssh/authorized_keys 파일의 뒤에 붙여넣기 하면 됩니다.

- scp(Secure Copy)

- SSH가 설정된 경우 SSH 프로토콜을 기반으로 네트워크를 통해 파일을 안전하게 전송하는 명령어

- 기본 문법

```bash
scp [옵션] [원본 경로] [대상 경로]
```

- 클라이언트의 파일을 서버로 전송
```bash
scp -P 포트번호 파일경로 유저이름@IP주소:서버의경로
```

- 클라이언트의 디렉토리 전송
```bash
scp -P 포트번호  -r 디렉토리 유저이름@IP주소:서버의경로
```

- 서버의 파일을 다운로드
```bash
scp -P 포트번호  유저이름@IP주소:서버의경로 로컬경로

scp -P 10001 adam@192.168.201.16:/home/adam/go1.22.0.linux-amd64.tar.gz .
```

### 2) Database Server

- Maria DB

- 설치: sudo apt install mariadb-server

- 서비스(mariadb) 활성화 및 확인

```bash
sudo systemctl start mariadb
sudo systemctl enable mariadb

sudo systemctl status mariadb
```

- 로컬에서 접속
```bash
sudo mysql
```

빠져나갈때는 exit

- mysqladmin 명령: MariaDB 서버를 관리하는 명령으로 version, status, password 명령을 수행할 수 있습니다.

- root 계정 비밀번호 생성
```bash
sudo mysqladmin -u root password '비밀번호'
```

- 변경된 비밀번호로 접속
```bash
sudo mysql -u root -p
```

- 유저 생성
CREATE USER '계정'@'%' IDENTIFIED BY '비밀번호';
% 대신에 IP를 설정하면 IP에서만 접속이 가능합니다.
root의 경우는 127.0.0.1 로만 접속하도록 합니다.

CREATE USER 'itstudy'@'%' IDENTIFIED BY 'wnddkd';

- 권한 설정
GRANT ALL PRIVILEGES ON *.* TO '계정'@'접속위치';
FLUSH PRIVILEGES;
*.* 대신에 데이터베이스 이름을 작성하면 데이터베이스만 사용 가능

GRANT ALL PRIVILEGES ON *.* TO 'itstudy'@'%';
FLUSH PRIVILEGES;

```bash
sudo mysql -u itstudy -p
```

- 외부 접속이 가능하도록 설정
설정 파일에서 바인딩 부분을 수정: sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
#bind-address            = 127.0.0.1
bind-address = 0.0.0.0

mariadb 서비스 재시작: sudo systemctl restart mariadb

방화벽에서 3306번 포트를 외부에서 접속 가능하도록 설정: sudo ufw allow 3306/tcp

- 외부 접속 확인

- 백업
mysqldump -u [사용자계정] -p [비밀번호] [원본데이터베이스이름] > 파일경로

- 복원
mysql -u [사용자계정] -p [비밀번호] [복원할 데이터베이스 이름] < 파일경로

- Mongo DB

- 설치
패키지 업데이트

```bash
sudo apt update && sudo apt install gnupg curl
```

MongoDB 공개 GPG 키 등록
```bash
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | \
sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
```

--dearmor

저장소 리스트 추가
```bash
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```

설치
```bash
sudo apt update
sudo apt install -y mongodb-org
```

- 서비스(mongod) 활성화 및 확인
```bash
sudo systemctl start mongod
sudo systemctl enable mongod

sudo systemctl status mongod
```

- 접속 확인
mongosh

- 외부 접속 허용
```bash
sudo nano /etc/mongod.conf 명령을 수행해서 bindIp를 0.0.0.0 으로 수정

sudo systemctl restart mongod 명령으로 서비스 재시작

sudo ufw allow 27017/tcp 명령으로 외부에서 접속 가능하도록 방화벽에 추가
```

- 외부에서 접속
mongodb://IP주소:포트번호

- 계정없이 외부에서 접속하도록 만들면 보안이 취약해지므로 계정을 생성해서 계정 정보를 포함해서 접속하도록 설정
mongosh 로 접속해서 자바스크립트 코드를 수행

use admin
db.createUser({user:"adminUser", pwd:"password123", roles:[{role:"userAdminAnyDatabase", db:"admin"}, "readWriteAnyDatabase"]})

인증 모드를 수정: sudo nano /etc/mongod.conf 파일을 열어서 아래 내용을 추가

security:
  authorization: enabled

서비스 재시작: sudo systemctl restart mongod

- 외부 접속 URL: mongodb://계정:비밀번호@서버주소:포트번호/
mongodb://adminUser:password123@192.168.201.16:37017/

- Redis DB

- 설치

```bash
sudo apt install redis-server
```

- 서비스(redis-server) 활성화 및 확인

- redis 접속
redis-cli

- 외부 접속 가능하도록 설정: /etc/redis/redis.conf
bind 0.0.0.0

requirepass 비밀번호

- 서비스 재시작
```bash
sudo systemctl restart redis-server
```

- 방화벽
```bash
sudo ufw allow 6379/tcp
```

- redis-cli를 실행하고 AUTH 비밀번호 를 해야만 명령어를 사용할 수 있습니다.
외부 접속 URL: redis-cli -h 서버IP -p 포트번호 -a 암호

- redis에서는 URL에 비밀번호를 직접 입력해서 접속하는 것은 권장하지 않고 접속한 후 AUTH 명령으로 비밀번호를 입력하기를 권장하며 포트 번호를 변경해서 사용하는 것을 권장합니다.
