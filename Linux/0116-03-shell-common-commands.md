# 스크립트에서 많이 사용하는 명령

### 1) 문자열을 조회하는데 사용하는 grep

- **기본 사용법**

```bash
grep [옵션] 패턴 [파일경로]

grep [옵션] [-e 패턴 | -f 파일] [파일]: e는 한꺼번에 여러 개의 정규식을 사용하겠다는 것이고 f 옵션은 패턴이 파일에 존재
```

- 예: `grep [옵션] [패턴 | -e 패턴]`

- **옵션**:

E: 확장 정규 표현식
F: 여러 줄 문자열을 검색

### 2) 파일을 찾을 수 있는 find

- 기본 사용법1

```bash
find [대상 경로][표현식]
```

#/etc 디렉토리에 이름이 chrony.conf 파일을 조회
```bash
find /etc chrony.conf
```

- 파일의 접근 권한이나 시간을 이용한 검색도 가능

### 3) 특정 인덱스 문자열을 출력할 수 있는 awk

- csv 형식으로 되어있는 파일에서 특정 열의 데이터만 추출하는 것이 가능

### 4) 찾은 문자열을 바꿀 수 있는 sed

- /etc/ssh/sshd_config 파일에서 #PermitRoot 를 찾아서 주석을 해제

```bash
sed 's/#PermitRoot/PermitRoot/'  /etc/ssh/sshd_config | grep '^#PermitRoot'
sed 's/#PermitRoot/PermitRoot/'  /etc/ssh/sshd_config | grep '^PermitRoot'
```

### 5) 날짜 와 시간을 알려주는 date

- **기본적인 사용법1**

```bash
date [옵션]
```

- date: 현재 날짜를 설정된 로케일 형식으로 보여줌
- date -d yesterday: 어제 시간을 보여줌

- **기본적인 사용법2**

```bash
date +포맷
```

- date '+%Y-%m-%d %I:%M %p'

