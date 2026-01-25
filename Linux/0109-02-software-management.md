# Software Management

## 1. 소프트웨어 관리

### 1) 우분투 패키지

- **개요**:

- 리눅스 소프트웨어는 소스 코드 형식 또는 바로 설치해서 사용할 수 있는 패키지 형태로 배포
- 소스 코드로 배포하는 경우는 대부분 하나의 아카이브 파일로 묶어서 압축한 후 배포
- 바이너리 파일로 배포하는 경우 리눅스에서 주로 사용하는 패키지 형식은 RPM 과 deb 두 가진인데 우분투는 deb 형식을 사용하고 redhat 계열에서 RPM을 사용
- 우분투에서도 RPM을 사용은 할 수 있으나 별도의 명령을 설치해야 하기 때문에 특별한 경우가 아니면 사용하지 않음
- 우분투 16.04 버전 부터는 새로운 패키지 형식인 snap 도입되었는데 deb의 의존성 문제를 해결한 것으로 deb 와 호환이 됩니다.

- **특징**:

- 바이너리 파일로 구성되어 있어서 컴파일할 필요가 없음
- 패키지 내의 파일이 관련 디렉토리로 바로 설치됨
- 패키지 삭제할 때 관련 파일을 일괄적으로 삭제할 수 있음
- 기존에 설치된 패키지를 삭제하지 않고 업그레이드 가능
- 패키지의 설치 상태를 검증할 수 있음
- 패키지에 대한 정보 제공
- apt 명령을 이용해서 의존성이 있는 패키지를 자동으로 설치

- **카테고리**:

- main: 우분투에 의해 공식적으로 지원되며 자유롭게 배포할 수 있음
- restricted: 우분투에 의해 지원되나 완전한 자유 라이선스 소프트웨어는 아님
- universe: 자유 라이선스일 수 도 있고 아닐 수 도 있는데 기술적 지원을 보장하지 않음
- multiverse: 개인이 직접 라이선스를 확인해야 함

- **이름**: 패키지명_버전_데비안리비전_우분투리비전_아키텍쳐.deb

- 패키지 저장소

- 패키지에 대한 정보 와 패키지를 저장하고 있는 서버인 패키지 저장소라는 개념이 있음
- 패키지 저장소에서 내려받아 사용을 합니다.
- 패키지 저장소를 이용할려면 등록을 해야 하는데 우분투에서는 /etc/apt/sources.list.d/ubuntu.sources

### 2) apt 명령

- **apt-cache**:

- APT 캐시에 질의해서 여러가지 정보를 검색
- 통계정보 확인: apt-cache stats
- 캐시에서 패키지 확인: apt-cache search 패키지이름
- 캐시에서 패키지 정보 출력: apt-cache show 패키지이름
- 사용 가능한 패키지 이름 확인: apt-cache pkgnames

- apt-get 또는 apt

- 형식
apt [옵션] 서브 명령

- 옵션
d: 패키지를 내려받기만 수행
f: 의존성이 깨진 패키지를 수정하려고 시도
h: 도움말 출력
y: 설치 여부를 묻는 부분을 생략

- 서브 명령
update: 패키지 저장소에서 새로운 패키지 정보를 가져옴, 설치한 후 맨 처음 한 번 수행
upgrade: 현재 설치된 패키지 업그레이드
install 패키지명: 설치
remove 패키지명: 삭제
purge 패키지명: 삭제
download 패키지명: 다운로드
autoclean: 불완전하게 내려받았거나 오래된 패키지를 삭제
clean: 캐시되어 있는 패키지를 삭제해서 디스크 공간을 확보
check: 의존성이 깨진 패키지를 확인

- apt update
맨 처음 리눅스를 설치한 경우 수행
저장소 파일을 수정한 경우에도 수행(도커 나 쿠버네티스를 설치할 때 저장소를 수정합니다.)

- apt upgrade
기존 패키지의 새로운 버전이 나온 경우 적용하고자 하는 경우 사용

- apt install
패키지 설치 명령
xterm 패키지 설치 - sudo apt install -y xterm

- remove, purge(y 옵션 존재)
패키지를 삭제하는 명령
remove는 패키지 설정 파일은 남겨두지만 purge는 설정 파일도 제거

```bash
sudo apt remove xterm
```

- 패키지 자동 정리: sudo apt autoremove(y 옵션이 존재)

- 디스크 공간 정리: sudo apt clean

- 다운로드만 받고자 하는 경우: sudo apt download xterm

- 패키지의 소스 코드 관련 명령
소스 코드를 내려받기만 하고자 하는 경우: sudo apt --download only source 패키지이름

소스 코드를 내려받고 압축을 풀고자하는 경우: sudo apt source 패키지이름

소스 코드를 내려받고 압축을 풀고 컴파일까지 하고자하는 경우: sudo apt --compile source 패키지이름

- python3 패키지가 있는지 확인해보고 없으면 설치하고 있으면 업그레이드를 수행
```bash
sudo apt search python3

sudo apt upgrade python3
```

### 3) dpkg

- **개요**:

- APT 명령은 fedora의 yum 이나 dnf 명령과 비슷하게 인터넷이 연결된 환경에서 패키지를 자동으로 설치하는데 fedora의 rpm 과 같은 명령어가 dpkg
- APT 도 내부적으로는 dpkg 명령을 사용
- 패키지를 설치할 때는 APT 명령을 사용하고 패키지를 확인할 때는 dpkg 명령을 사용

- **형식**:

dpkg [옵션] 파일명 또는 패키지이름

- 옵션
l: 목록을 출력
s 패키지이름: 패키지의 상세 정보를 출력
L 패키지명: 패키지를 설치할 때 사용한 파일의 목록을 출력
c .deb파일: 파일의 내용을 출력
i .deb파일: 해당 파일을 설치
r 패키지이름: 패키지 삭제
P 패키지이름: 패키지와 설정 파일 모두 지움
x .deb파일 디렉토리: 해당 파일을 디렉토리에 풀어놓기

### 4) aptitude

- 그래픽 화면에서 apt를 사용하는 효과

- 없으면 설치: sudo apt install aptitude

### 5) snap

- **개요**:

- 우분투가 새로 도입한 패키지 형식으로 샌드박스 형태의 패키지
- 패키지를 만들 때 프로그램이 사용하는 모든 라이브러리를 패키지 안에 포함하는 형식으로 외부에서 받은 파일을 그냥 실행하는 것이 아니라 보호된 영역에서 실행해보는 것으로 외부 파일이 내부 시스템에 악영향을 주는 것을 방지하는 보안 기술을 위한 것
- 기존 시스템 과 격리된 형태로 사용
- 단점은 패키지가 커지게 됨

- **명령**:

- 형식: snap [옵션] 명령

- 옵션
h: 도움말

- 명령
disable: 스냅 서비스 중지
download: 다운로드
enable: 스냅 서비스 와 실행 파일의 사용을 시작

```bash
find 스냅이름: 조회
```

info 스냅이름: 상세정보 출력
install 스냅이름: 설치
list: 목록 이름 출력
remove 스냅이름: 삭제

- **실습**:

- hello-world 스냅 검색

```bash
sudo snap find hello-world
```

- hello-world 스냅 설치
```bash
sudo snap install hello-world
```

- 목록 보기로 확인
```bash
sudo snap list
sudo snap info hello-world
```

### 6) 파일 압축

- tar(tape archive)

- 명령형식
tar 기능 [옵션] [아카이브파일] [파일이름]

- 기능
c: 새로운 tar 파일을 생성
t: tar 파일의 내용을 출력
x: 원본 파일을 추출
r: 새로운 파일을 추가
u: 수정된 파일을 업데이트

- 옵션
f: 아카이브 파일이나 테이프 장치를 지정하는데 파일명을 -로 지정하면 tar 파일 대신 표준 입력에서 읽어들임
v: 처리하고 있는 파일의 정보를 출력
h: 심볼릭 링크의 원본 파일을 포함
p: 파일을 복구 시 원래의 접근 권한을 유지
j: bzip2로 압축하거나 해제
z: gzip으로 압축하거나 해제

- 자주 사용하는 옵션
압축: cvf
압축 해제: xvf

내용 확인: tvf
업데이트: uvf
파일 추가: rvf 

- 압축 과 압축 해제
실습을 위해서 디렉토리 1개를 생성: mkdir ex_archive

생성한 디렉토리로 프롬프트를 이동: cd ex_archive

디렉토리를 3개 생성: mkdir sample1 sampe2 sampe3

sample1을 가지고 sample1.tar로 압축: tar cvf sample1.tar sample1

압축된 파일의 내용을 확인: tar tvf sample1.tar

sample1 디렉토리를 삭제: rmdir sample1 또는 rm -r sample1

현재 디렉토리에 sample1.tar 파일의 압축을 해제: tar xvf sample1.tar

압축 파일에 내용 추가: tar rvf sample1.tar sample2(에러 발생 - 디렉토리를 추가할 수 없음)

파일 생성: touch sample.txt

압축 파일에 내용 추가: tar rvf sample1.tar sample.txt

- 사이즈 줄이기
z 옵션을 추가해서 압축을 하면 압축 파일의 사이즈를 줄일 수 있습니다.

tar czvf sample1.tar.gz sample1 sample.txt

tar cjvf sample1.tar.bz sample1 sample.txt

- **gzip/gunzip**:

- 압축률이 좋은 유틸
- 지정한 파일의 크기를 줄여서 저장하는데 확장자는 일반적으로 gz 사용
- 형식
gzip [옵션] [파일명]
- 옵션
d: 해제
l: 정보 출력
r: 하위 디렉토리를 탐색하여 압축
t: 압축 파일 검사
v: 압축 정보를 화면에 출력
g: 최대한 압축
- 압축 해제
gunzip sample1.tar.gz

- bzip2/bunzip2

- 압축률은 gzip보다 좋은데 속도가 약간 느림
d: 해제
l: 정보 출력
t: 압축 파일 검사
v: 압축 정보를 화면에 출력
--best: 최대한 압축

-  sample1.tar 파일의 사이즈 줄이기
 bzip2 sample1.tar 

- 압축 해제
bunzip2 sample1.tar.bz2

- **xz**:

- 가장 최근에 등장한 압축 방식으로 압축률이 가장 뛰어남

- **zip/unzip**:

- windows 와의 호환성 때문에 제공

### 7) 다운로드

- **wget**:

- 개요
web get의 약어로 웹 상의 파일을 다운로드 받을 때 사용하는 명령어

비상호작용 네트워크 다운로더

HTTP, HTTPS, FTP 프로토콜을 지원하고 HTTP proxy에서 데이터를 가져올 수 있음

비상호작용 네트워크 다운로더라서 사용자가 로그인하지 않은 상태 동안에도 백그라운드에서 동작할 수 있음

HTML 과 XHTML, CSS 페이지를 다운로드 받아서 웹 사이트의 로컬 버전을 만들 수 있고 본래 사이트의 디렉토리 구조를 만들 수 있고 recuresive downloading을 지원해서 사이트 전체를 쉽게 내려받을 수 있음

느리거나 불안정한 네트워크 환경에서도 매우 잘 작동하는 견고한 프로그램으로 네트워크 환경이 불안정해서 도중에 연결이 끊겼다면 연결이 끊어진 시점에서 다운로드 가능

- 기본 다운로드: wget https://example.com/file.zip
- 파일 이름 변경: wget -O 파일명 https://example.com/file.zip
- 다운로드 이어서 받기(중단된 경우): wget -c https://example.com/file.zip
- 백그라운드에서 다운로드: wget -b https://example.com/file.zip
- 속도 제한: wget --limit-rate  https://example.com/file.zip

- 웹 사이트 미러링

```bash
wget -m https://example.com
```

-r: 하위 디렉토리까지 따라가면 다운로드
-np: 상위 디렉토리로 이동하지 않도록 제한
-k: 로컬에서 오프라인으로 보기 편하도록 링크를 변환

- **curl**:

- 개요
터미널 환경에서 다양한 프로토콜을 사용해서 데이터를 전송하거나 가져오는 도구
서버 와의 데이터 통신 및 API 테스트에 최적화되어 있어 개발자들이 거의 필수적으로 사용하는 도구

- 기본 문법

```bash
curl [옵션] [URL]
```

- 웹 페이지 소스 보기: curl https://example.com
- 파일로 저장
```bash
curl -o 파일이름 https://example.com
curl -O https://example.com
```

- HTTP 헤더 확인: curl -l https://example.com
- 리다이렉트 자동 추적: curl -L http://google.com

- REST API 테스트 할 때 많이 사용하는 옵션
-X: 사용할 HTTP 메서드(GET, POST, PUT, DELETE 등)
-d: 전송할 데이터
-H: 헤더 정보를 추가

- 자주 사용하는 옵션: -fsSL
f: HTTP 오류가 발생하면 아무것도 출력하지 말고 실패 처리
s: 에러 메시지 표시하지 않음
S: s 와 함께 사용하는데 진짜 에러가 난 경우에만 메시지를 출력
L: 리다이렉트된 경우 추적해서 데이터를 가져옴

sh 나 bash로 연결된 스크립트 파일 실행할 때 사용

사용하는 이유
출력이 깔끔
안정성: 404 에러가 발생했을 때 불필요하게 에러코드가 쉘에게 전송되는 것을 막기 위해서
유연성

- wget 과 curl의 차이점

| 항목 | wget | curl |
| --- | --- | --- |
| 주요 목적 | 단순 파일/사이트 다운로드 | 데이터 전송 및 API 테스트 |
| 재시도 | 재시도 가능 | 재시도하지 않음 |
| 재귀적 다운로드 | 가능 | 불가 |
| 복잡성 | 단순하고 직관적 | 다양한 프로토콜/옵션 제공 |
