# 사용자 관리

### 1) 사용자 계정 관리 파일

- **/etc/passwd**:

- 사용자 계정 정보가 들어있는 파일
- 초창기 유닉스에서는 암호도 파일에 저장했으나 해킹 위험이 증가하면서 암호는 /etc/shadow 파일에 별도로 저장
- root 계정으로 수정이 가능하지만 되도록이면 명령으로 변경하는 것을 권장
- 파일 구조: 콜론으로 구분된 7개의 항목

```text
adam:x:1000:1000:adam:/home/adam:/bin/bash
```

첫번째 항목은 로그인 ID인데 32자까지 가능하고 대문자, 소문자, 숫자, _, - 가능한데 -으로 시작은 안되고 숫자로만 구성해도 안됨

두번째 항목은 x 인데 이 항목은 초창기 유닉스에서 비밀번호 저장하던 위치인데 지금은 무조건 x

세번째 항목은 UID로 사용자를 구분하기 위한 번호
0~999번 과 65534번은 시스템 사용자를 위한 UID
시스템 사용자 계정은 시스템이 관리 업무를 위해 내부적으로 사용하려고 예약한 계정이므로 임의로 수정하면 안됨

네번째 항목은 GID로 그룹을 구분하기 위한 번호로 사용자는 반드시 한 개 이상의 그룹에 소속되어야 하고 사용자의 기본 그룹은 사용자를 만들 때 정해지는데 그룹을 지정하지 않으면 로그인 ID로 그룹을 생성해서 지정
이 내용은 /etc/group에 저장

다섯번째 항목은 설명

여섯번째 항목은 홈 디렉토리

일곱번째 항목은 로그인 쉘

- **/etc/shadow**:

- 암호 정보를 가진 파일
- /etc/passwd 파일은 누구나 읽을 수 있지만 /etc/shadow 파일의 경우는 root 계정만 읽고 쓰기가 가능합니다.
- 파일 구조

```text
adam:$6$xmVA1AAtDu8pomb.$MOIgk41QcxHBFvyuTM3jbe/UzcmpGvGxE09DTVF9.i/ktDEmjR2S89LhILY6gEj0E9bjpkgKTuLzcSay8jfoe1:20459:0:99999>sshd:!:20459::::::
```

첫번째 항목은 로그인 ID

두번째 항목은 비밀번호인데 일방향 암호화 됨
이 항목에 내용이 없으면 암호가 지정되지 않은 계정이며 시스템 계정은 *

세번째 항목은 마지막으로 변경한 날짜
1970년 1월 1일을 기준으로 지나온 날짜 수

네번째 항목은 암호를 사용할 수 있는 최소 기간

다섯번째 항목이 암호를 사용할 수 있는 최대 기간

여섯번째 항목은 암호가 만료되기 전에 경고를 시작하는 날수

일곱번째 항목은 암호가 만료되더라도 이 기간 동안은 사용 가능

여덟번째 항목은 사용자 계정이 만료되는 날로 이 기간이 지나면 로그인 불가

아홉번째 항목은 미래를 위해서 비워둔 항목

- /etc/login.defs

- 사용자 계정의 설정과 관련된 기본값을 정의한 파일
- 파일 내용
MAIL_DIR -> /var/mail: 기본 메일 디렉토리

패스워드 에이징 관련 값
PASS_MAX_DAYS -> 99999
PASS_MIN_DAYS -> 0
PASS_WARN_DAYS -> 7

UID_MIN, UID_MAX: 사용자 계정의 UID 범위(1000~60000)
SYS_UID_MIN, SYS_UID_MAX: 시스템 계정의 UID 범위

DEFAULT_HOME -> yes: 계정을 만들 대 홈 디렉토리 생성 여부

UMASK ->022: 파일이나 디렉토리를 생성할 때 기본 접근 권한

ENCRYPT_METHOD -> SHA512: 암호화 기법

USERGROUPS_ENAB -> yes: 사용자 계정 삭제 시 그룹 삭제 여부

- **/etc/group**:

- 그룹의 정보를 저장한 파일
- 파일 구조
lxd:x:101:adam

첫번째 항목은 그룹 이름
두번째 항목은 그룹의 비밀번호인데 지금은 /etc/gshadow로 이전
세번째 항목은 그룹의 ID(GID)
네번째 항목은 사용자 계정 이름인데 사용자의 2차 그룹을 의미

### 2) 사용자 계정 생성

- **useradd**:

- 기본 형식

```bash
useradd [옵션] [로그인 ID]
```

- 옵션
u uid: UID 지정
o: UID의 중복 허용
g gid
G gid: 2차 그룹의 GID
d 디렉토리이름: 홈 디렉토리 지정
s 쉘: 로그인 쉘 지정
c 설명: 설명 설정
D: 기본값을 설정하거나 출력
e 유효기간: EXPIRE 항목 설정
f 비활성일수
k 디렉토리: 계정 생성 시 복사할 초기 파일이나 디렉토리를 설정해 놓은 디렉토리 설정

- 옵션 없이 생성
```bash
sudo useradd user2
```

계정 정보 확인: tail -1 /etc/passwd
user2:x:1001:1001::/home/user2:/bin/sh

비밀번호 확인(비밀번호를 설정하지 않아서 !로 보임): sudo tail -1 /etc/shadow

기본 설정 확인: useradd -D

기본 설정 확인: cat /etc/default/useradd 

- 기본값 수정: 파일을 직접 수정하는 것 보다는 명령으로 수정하는 것을 권장
EXPIRE 값 수정: sudo useradd -D -e 2026-12-31

- /etc/skel 디렉토리
사용자 계정을 생성할 때 시스템 운영 정책에 따라 사용자 계정의 홈 디렉토리에 공통으로 배포해야 할 파일이 있을 때 이 디렉토리에 만들어 놓으면 됩니다.

```bash
ls -a /etc/skel
```

- 옵션을 지정해서 유저를 생성
```bash
sudo useradd -s /bin/bash -m -d /home/user3 -u 2000 -g 1000 -G 3 user3
```

- **adduser**:

- 형식
adduser [옵션] 로그인ID

- 옵션
uid
gid
home
shell
gecos 설명

- 기본 설정에 따라 사용자 계정을 등록한 후 암호 및 기타 정보를 입력하도록 함

```bash
sudo adduser user4

sudo adduser --uid 2002 user5
```

- 기본 설정 값
```bash
sudo cat /etc/adduser.conf
```

### 3) 사용자 계정 수정

- **usermod**:

- 기본 형식

```bash
usermod [옵션] [로그인ID]
```

- 옵션
u: uid 수정
o: UID의 중복을 허용
g: 기본 그룹 수정
G: 2차 그룹 수정
d: 홈 디렉토리를 수정
s: 로그인 쉘 수정
c: 부가적인 설명 수정
f: 계정 비 활성화 날짜를 수정
e: 계정 만료 날짜를 수정
l: 계정 이름을 변경

- user3의 홈 디렉토리 변경
```bash
sudo usermod -d /home/adam user3
```

- 패스워드 에이징 수정

| 항목 | passwd | useradd/usermod | chage |
| --- | --- | --- | --- |
| MIN | `passwd -n <days>` | - | `chage -m <days>` |
| MAX | `passwd -x <days>` | - | `chage -M <days>` |
| WARNING | `passwd -w <days>` | - | `chage -W <days>` |
| INACTIVE | - | `useradd -f <days>`, `usermod -f <days>` | `chage -U <days>` |
| EXPIRE | - | `useradd -e <date>`, `usermod -e <date>` | `chage -E <date>` |

```bash
sudo passwd -n 3 -x 100 -w 5 user3
sudo chage -m 2 -M 200 -W 5 -I 100 -E 2026-12-31 user3
sudo grep user3 /etc/shadow
```

### 4) 그룹 생성

- **groupadd**:

- 형식: groupadd [옵션] [그룹명]
- 옵션
g: 그룹 아이디 설정
o: 중복 허용

- **addgroup**:

- 형식: addgroup [옵션] [그룹명]
- 옵션
gid: 그룹 아이디 설정

### 5) 그룹 정보 수정

- **groupmod**:

- 형식: groupmod [옵션] [그룹명]
- 옵션
g: 그룹 아이디 설정
o: 중복 허용
n 그룹명: 그룹 이름 변경

### 6) 그룹 삭제

- groupdel

### 7) 그룹 암호 설정과 유저 추가

- **gpasswd**:

- 형식: gpasswd [옵션] [그룹이름]

- 옵션
a: 사용자 계정을 그룹에 추가
d: 사용자 계정을 그룹에서 제거
r: 그룹 암호 설정

- 실습

```bash
sudo groupadd gtest1

sudo useradd test1
sudo useradd test2

sudo gpasswd -a test1 gtest1  
sudo gpasswd -a test2 gtest1

grep gtest1 /etc/group
```

### 8) 사용자 정보 관리 명령

- UID 와 EUID

- UID는 RUID라고도 하는데 로그인 할 때 사용한 계정의 ID 이고 EUID는 현재 명령을 수행하는 주체의 UID 
- UID 와 RUID가 달라지는 경우
su 명령으로 계정을 전환 한 경우에는 로그인 한 ID가 UID가 되고 계정을 전환한 경우의 EUID는 전환된 계정의 UID

setuid가 설정된 경우

- 사용자 확인 명령

- 사용자의 로그인 정보 확인 명령: who
- 형식: who [옵션]
- 옵션
q: 사용자 이름만 출력
H: 헤더를 같이 출력
b: 마지막 재부팅 시간 출력
m: 현재 사용자의 정보를 출력
r: 현재 런레벨을 출력

- 결과
adam     pts/0        2026-01-13 00:06 (192.168.201.16)

pts/0 은 가상 터미널: telnet 이나 ssh로 접속했을 때

- 특정 사용자의 로그인 정보 확인 명령

- w
- 형식: w [사용자ID]
- ID를 생략하면 현재 사용자 로그인 정보를 출력

- **last**:

- 사용자명과 로그인한 시간, 로그아웃 한 시간, 터미널 번호 와 IP 주소를 출력

- UID 와 EUID 확인 명령

- whaami, who am i, id 명령

- 소속 그룹 확인 명령

- groups [계정명]

### 9) root 권한 사용

- root 권한을 사용하는 방법

- su 명령을 사용해서 root 계정으로 전환해서 사용
권장하지 않음
일반 사용자가 모든 시스템 관리 권한을 소유하기 때문에 위험
- sudo 명령으로 제한적인 권한 부여를 해서 사용하는 방법

- sudo 권한 설정

- 일반 사용자가 sudo 명령으로 root 권한을 사용하려면 특정 권한을 부여받아야 함
- 권한은 /etc/sudoers 파일에 설정해야 하는데 root 계정으로만 수정할 수 있음
- /etc/sudoers 파일은 직접 수정 가능하지만 visudo 명령을 이용하는 것을 권장
- visudo 명령은 파일을 수정한 후 문법이 맞는지 확인하고 저장
- 설정 형식
계정 ALL=명령어절대경로 나열

모든 권한 부여
adam ALL=(ALL) ALL
adam ALL=/usr/sbin/useradd, /usr/sbin/userdel...
- 리눅스 설치 한 후 만든 계정에서 sudo 명령이 안되면 이 파일을 수정하면 됩니다.

- passwd 명령

- 사용자 계정의 암호를 수정하는 명령
- 암호를 잠그면 로그인 할 수 없는데 로그인 중에 암호를 잠그게 되면 sudo 명령은 사용할 수 없지만 로그인한 세션에는 영향이 없음
- 형식: passwd [옵션][사용자 계정]
- 옵션
l: 지정한 계정의 암호를 잠금
u: 잠금 해제
d: 암호를 삭제

### 10) 파일의 소유자 와 소유자 그룹 변경

- chown: 파일의 소유자 변경

- 형식
chown [옵션][사용자계정][파일 또는 디렉토리 경로]

- 옵션
R: 서브 디렉토리의 소유자 와 소유 그룹도 변경

- 실습 
- 유저 생성 및 비밀번호 변경

```bash
sudo adduser user1
```

su user1 # 계정 변경 가능

```bash
sudo passwd -l user1 #계정 잠금
```

su user1 #계정이 잠겨서 로그인 되지 않음

```bash
sudo passwd -u user1 #계정 잠금 해제
```

su user1 #로그인 가능

- 디렉토리를 생성한 후 소유권 변경
mkdir linux_ex

cd linux_ex

mkdir usermanagement

cd usermanagement

mkdir temp

```bash
cp /etc/hosts .
cp /etc/services temp
```

#소유자 확인
```bash
ls -l
```

#hosts 파일의 소유자를 user1으로 변경
```bash
sudo chown user1 hosts
```

- chgrp: 파일의 소유자 그룹을 변경

- 형식: chgrp [옵션] [그룹이름] [파일 경로 및 디렉토리 경로]

### 11) 디스크 사용량 설정

- **개요**:

- 리눅스에서는 사용자에게 디스크 용량을 제한하거나 생성할 수 있는 파일의 개수를 제한하는 명령이 존재
- 하드 리미트 와 소프트 리미트
하드 리미트: 사용자가 절대로 넘을 수 없는 최대치를 명시한 값
소프트 리미트: 일정 시간내에는 넘을 수 있는 한계치
시간이 지나면 소프트 리미트도 하드 리미트 값이 적용됨

- 패키지 설치: quota

- 디스크 공간 확인: df -h

- 사용자 생성: sudo useradd -m -d /home/qtest1 qtest1

- 파티션 확인

```bash
sudo fdisk -l
```

- 쿼터 속성 설정

```bash
sudo vi /etc/fstab
```

- 아래처럼 설정하면 qtes1 에게 쿼터 설정 가능
/dev/sda3 /home ext4 defaults,usrquota 1 1

- 마운트를 다시 수행
```bash
sudo systemctl daemon-reload
sudo mount -o remount /home
```

- 쿼터 데이터베이스 파일 생성

```bash
sudo quotacheck -ugvm /home
```

- 쿼터 활성화

```bash
sudo quotaon -uv /home
```

- 쿼터 설정

```bash
sudo edquota -u qtest1
```

- 쿼터 확인

```bash
sudo quota qtest1

sudo repquota -a
```

