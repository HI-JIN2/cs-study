# Process Management

## 1. 프로세스 관리

### 1) 작업 제어

- 작업 전환

CTRL+z: 포그라운드 작업을 중지(종료가 아니고 중단)
bg %작업번호: 작업 번호가 지시하는 작업을 백그라운드로 전환
fg %작업번호: 작업 번호가 지시하는 작업을 포그라운드로 전환

- 실습
현재 작업 확인: jobs

포그라운드 작업 수행: sleep 200

포그라운드 작업을 일시 중지: CTRL + z

현재 작업 확인: jobs

백그라운드 작업으로 전환: bg %1

포그라운드 작업으로 전환: fg %1

- 작업 종료

- 포그라운드 작업은 CTRL + c를 입력하면 대부분 종료되는데 인터럽트 시그널을 포그라운드 프로세스에 전달하며 인터럽트를 받으면 프로세스가 종료되는데 프로그램에서 무시하도록 설정한 경우에는 종료되지 않습니다.
- 다른 터미널에서 해당 프로세스의 PID를 찾아서 강제로 종료
- kill을 이용해서 작업번호를 이용한 백그라운드 작업 종료도 가능
- 실습
sleep 100 #이 작업은 CTRL + c로 작업 종료

sleep 100 &

kill %1 #백그라운드 작업은 kill 명령으로 작업 종료

jobs

- 로그아웃 후에도 계속 작업하기

nohup 명령 &

### 2) 작업 예약

- 정해진 시간에 한 번 만 수행

- 형식: at [옵션] [시각]

- 옵션
l: 현재 실행 대기 중인 명령의 전체 목록을 출력
r 작업번호: 현재 실행 대기 주인 명령 중 해당 작업 번호를 삭제
m: 출력 결과가 없더라도 작업이 완료되면 사용자에게 메일로 알려줌
f 파일: 표준 입력 대신 실행할 명령을 파일로 지정

- 시간 설정
at 4pm + 3days: 3일 후 오후 4시
at 10am Jul 31: 7월 31일 오전 10시
at 1am tomorrow: 내일 오전 1시
at 1am today: 오늘 오전 1시

- 작업 확인
/var/spool/cron/atjobs 파일에 작업이 기록되는데 수행할 명령이 없으면 삭제됨

at -l

atq

- 작업 삭제
at -r 작업번호

atrm 작업번호

- 설치가 안된 경우

```bash
sudo apt install at
```

- 실습
at 12:46 am

/usr/bin/ls > ~adam/at.out(다 작성한 후 CTRL + d)

#3분 후에 현재 시간 및 날짜를 at.out에 출력
at now + 3minutes

```bash
date > at.out
```

CTRL + d를 눌러서 작업 작성 종료

- 파일에 작성해서 수행
```bash
vim at.sh

date > ~adam/test.txt
ls >> ~adam/test.txt
```

at -f ~adam/at.sh 1:23

- 작업 확인
```bash
sudo ls -l /var/spool/cron/atjobs
```

at -l

- 사용 제한
시스템 관리자는 at 명령을 사용하도록 허용하거나 사용하지 못하도록 제한할 수 있는데 이 와 관련된 파일이 /etc/at.allow 와 /etc/at.deny
한 줄에 하나씩 계정을 입력하면 됩니다.
at.allow가 있으면 at.deny 는 무시
at.allow가 없으면 at.deny 사용자를 제외한 모든 사용자가 at을 사용 가능
둘 다 없으면 root 만 사용 가능
초기 설정은 at.deny만 있는데 비어있는 상태라서 모든 사용자가 at 사용 가능

- 주기적인 반복 작업

- crontab
- 형식: crontab [-u 사용자ID] [옵션] [파일명]
- 옵션
e: crontab 파일 편집
ㅣ: crontab 파일의 목록을 출력
r: crontab 파일을 삭제
- 이 명령은 사용자별로 별도의 파일로 생성됨
- crontab 파일에는 여러 개의 작업을 저장할 수 있는데 한 줄에 하나의 작업을 설정해야 합니다.

- 1시 42분이 되면 ls 명령을 수행해서 cron.out으로 출력하도록 생성
crontab -e #편집기를 선택하는 화면이 출력됩니다.
#편집기를 선택하고 작성
42 1 * * * ls -l > ~adam/cron.out

- crontab 관련 파일
/var/spool/cron 디렉토리: 작업 관련된 파일로 계정당 하나씩 파일을 소유

```bash
sudo ls /var/spool/cron/crontabs
```

- crontab 로그
우분투에서는 기본적으로 cron 로그 기록이 비활성화되어 있는데 cron.log를 활성화하고자 하는 경우 sudo service rsyslog restart 명령을 수행하면 됩니다.
