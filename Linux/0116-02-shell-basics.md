# 기본 문법

### 1) 생성 및 실행

- 스크립트 만들기

- 확장자는 sh를 사용
- 시작할 때는 #!/bin/bash(shebang)를 붙여서 해당 파일이 셸 스크립트라는 것을 알려줍니다.
- 실행하고자 하는 명령어를 입력하고 저장합니다.
- 파일을 작성: myshell.sh

```bash
#!/bin/bash

echo "Hello World"
```

- 실행

- 셸로 실행(파일에 읽기 권한만 있으면 됩니다)
  ```bash
  sh myshell.sh
  bash myshell.sh
  ```

- 파일 이름으로 직접 실행
  - 실행 권한을 부여
  - PATH에 없으면 `./`를 붙여서 실행
  ```bash
  chmod 751 myshell.sh
  ./myshell.sh
  ```

- 셸에서 직접 실행
  ```bash
  echo "Hello World"
  ```

### 2) 변수 사용

- 변수 선언

- 기본 변수 선언해서 문자열을 출력
변수명=값 의 형태로 선언하는데 변수가 존재하면 수정

변수를 사용할 때는 변수명 앞에 달러 기호를 붙여서 작성합니다.

- 소스 코드 수정

```bash
#!/bin/bash

language="Korean" #값을 할당할 때 = 다음에 공백을 주면 명령어로 인식
echo "I can speak $language"
```

- 실행

- Korea Newziland USA 디렉토리를 셸 스크립트를 이용해서 생성
```bash
#!/bin/bash

language="Korea Newziland USA"
mkdir $language
```

- 변수의 종류

- 함수
생성을 할 때는 function 이름() { 내용 }
내용을 작성할 때 파라미터를 사용하고자 하면 번호 형태의 파라미터를 사용하고, 함수를 호출할 때 넘긴 값이 순서대로 대입됩니다.
함수 호출을 할 때는 함수이름 파라미터 파라미터... 형식으로 합니다.

- 함수 생성 및 호출

```bash
#!/bin/bash

function print(){
        echo $1
}

print "I can speak Korean"
```

- 전역 변수
함수 외부에 선언해서 모든 영역에서 사용 가능한 변수

- 지역 변수
함수 내부에 선언해서 함수 내부에서만 사용 가능한 변수

- 환경 변수
시스템을 위해 사전에 예약해두고 시스템에서 사용하고 있는 변수
자주 사용하는 환경변수

- `HOME`: 사용자의 홈 디렉토리
- `PATH`: 실행 파일을 찾는 디렉토리 경로
- `PWD`: 현재 작업 중인 디렉토리
- `USER`: 현재 로그인한 사용자 이름
- `USERNAME`: 현재 로그인한 사용자 이름
- `HOSTNAME`: 현재 컴퓨터 이름

- 위치 매개변수
스크립트 수행 시 함께 넘어오는 파라미터

```text
$0: 스크립트 이름
$1, $2, ${10}: 파라미터 순서대로 번호가 부여되는데 10번째 부터는 { }로 감싸야 합니다.
$*: 전체 인자값(""를 사용하면 전체를 하나로 인식)
$@: 전체 인자값인데 쌍따옴표로 감싸면 다른 결과 나옴(""를 이용해도 각각으로 인식)
$#: 매개변수의 총 개수
```

- 위치 매개변수 활용
코드 작성

```bash
#!/bin/bash

echo "$0"
echo "$1 and $2"
echo "$*"
echo "$@"
echo "$#"
```

실행
```bash
./myshell.sh Korea Newziland
```

- 특수 매개변수
실행 중인 스크립트나 명령어의 PID를 확인하거나 바로 앞에서 실행한 명령어나 함수 또는 스크립트 실행이 정상적으로 수행되었는지 여부를 확인할 수 있는 변수

```text
$$: 현재 스크립트 또는 명령어의 PID
$?: 최근에 실행된 명령어/함수/스크립트의 종료 상태
$!: 최근에 실행된 백그라운드 명령의 PID
$-: 현재 옵션 플래그
```

이를 확인해서 현재 스크립트를 &를 이용해서 백그라운드로 수행할 지 결정할 수 있습니다.

- 매개변수 확장

- 기본 변수 사용
변수명을 사용하는데 뒤에 공백없이 문자열을 붙여야 하는 경우 `{ }`를 이용해서 변수명을 구분할 수 있습니다.

AUTH_URL 이라는 변수에 www.example.com/을 저장해두고 https://변수내용login.html 로 출력하고자 하는 경우

```bash
AUTH_URL="www.example.com/"

echo "https://$AUTH_URLlogin.html" # 변수명을 인식하지 못해 https://.html 이 출력됩니다
echo "https://${AUTH_URL}login.html" # 변수명을 인식해서 https://www.example.com/login.html 로 출력됩니다
```

- 변수 초기화하기 위한 확장 변경자

```text
${변수-문자열}: 변수가 없으면 문자열로 치환
${변수:-문자열}: 변수가 없거나 NULL이면 문자열로 치환
${변수=문자열}: 변수가 없으면 문자열을 변수에 저장하고 변수 치환
${변수:=문자열}: 변수가 없거나 NULL이면 문자열을 변수에 저장하고 변수 치환
${변수+문자열}: 변수가 있는 경우 문자열로 변수 치환
${변수:+문자열}: 변수가 설정되고 NULL이 아닌 경우 문자열로 변수 치환
${변수?에러메세지}: 변수가 설정된 변수를 사용하고 설정되지 않은 경우 표준오류 출력으로 에러 메시지를 출력
${변수:?에러메세지}: 변수가 설정되고 값이 NULL이 아니면 변수를 사용하고 설정되지 않은 경우 표준오류 출력으로 에러 메시지를 출력
${변수:시작위치}: 변수 값이 문자열일 경우 시작 위치부터 문자열 길이 끝까지 출력
${변수:시작위치:길}: 변수 값이 문자열일 경우 시작 위치부터 문자열 길이까지 출력
```

OS_TYPE=redhat
```bash
echo ${OS_TYPE-ubuntu} #OS_TYPE이 존재하므로 redhat
```

unset OS_TYPE #변수 삭제
```bash
echo ${OS_TYPE-ubuntu} #OS_TYPE이 존재하지 않으므로 ubuntu를 출력하지만 OS_TYPE에 값이 대입되지는 않음
echo ${OS_TYPE}
```

unset OS_TYPE #변수 삭제
```bash
echo ${OS_TYPE=ubuntu} #OS_TYPE이 존재하지 않으므로 ubuntu를 출력하고 OS_TYPE에 값을 대입
echo ${OS_TYPE}
```

OS_TYPE="redhat"
```bash
echo ${OS_TYPE:?null or net set} #변수에 값이 있으므로 변수의 값을 출력
```

OS_TYPE=""
```bash
echo ${OS_TYPE:?null or net set} #변수에 값이 없으므로 메시지 출력, 비정상 종료
echo $?
```

OS_TYPE="Redhat Fedora  Debian Ubuntu" 
```bash
echo ${OS_TYPE:14}
echo ${OS_TYPE:14:6}
echo ${OS_TYPE:(-6)}
```

- 치환

```text
${변수#패턴}: 패턴 앞의 모든 문자열 제거
${변수##패턴}: 패턴을 찾아서 가장 마지막 패턴의 앞에 있는 모든 문자열을 제거
${변수%패턴}: 패턴 뒤의 모든 문자열 제거
${변수%%패턴}: 패턴을 찾아서 가장 마지막 패턴의 뒤에 있는 모든 문자열을 제거
${#변수}: 문자열의 길이
${변수/찾을문자열/바꿀문자열}: 첫번째 패턴을 뒤의 문자열로 치환
${변수/#찾을문자열/바꿀문자열}: 시작 문자열이 일치하면 치환
${변수/%찾을문자열/바꿀문자열}: 마지막 문자열이 일치하면 치환
```

FILE_NAME="myvm_container-repo.tar.gz"

#_앞의 문자열을 모두 제거
```bash
echo ${FILE_NAME#*_}
```

#마지막으로 찾은 . 앞의 문자열 모두 제거
```bash
echo ${FILE_NAME##*.}
```

#_뒤의 문자열을 모두 제거
```bash
echo ${FILE_NAME%_*}
```

FILE_PATH="/etc/nova/nova.conf"
#디렉토리 경로 출력
```bash
echo ${FILE_PATH%/*}
```

#파일 이름만 출력
```bash
echo ${FILE_PATH##*/}
```

OS_TYPE="Redhat Linux Ubuntu Linux Fedora Linux"

#처음처럼 찾은 Linux를 OS로 변경
```bash
echo ${OS_TYPE/Linux/OS}
```

#모든 Linux를 OS로 변경
```bash
echo ${OS_TYPE//Linux/OS}
```

#Linux를 삭제
```bash
echo ${OS_TYPE//Linux}
```

#Redhat로 시작하는 단어를 Unknown으로 변경
```bash
echo ${OS_TYPE/#Redhat/Unknown}
```

#tu로 끝나는 단어를 Debian으로 변경
```bash
echo ${OS_TYPE/%tu/Debian}
```

### 3) 조건문 - if, switch-case

- **if**:

- 기본 형식
```bash
if [ 첫번째 조건식 ]
then
  수행문
elif [ 두번째 조건식 ]
then
  수행문
else
  수행문
fi
```

- 실습
```bash
value1=10
value2=20

if [ "$value1" = "$value2" ]
then
  echo True
else
  echo False
fi
```

- -z 를 이용하면 문자열의 길이가 0인지 확인
```bash
value=""

if [ -z "$value" ]
then
  echo True
else
  echo False
fi
```

- 크다 와 작다는 -gt, -lt로 표현

- **case**:

- 기본형식
```bash
case $변수 in
  조건값)
    수행할문장
    ;;
  조건값)
    수행할문장
    ;;
  ...
  *)
    수행할문장
    ;;
esac
```

- 실습
```bash
case $1 in
  start)
    echo "Start"
    ;;
  restart)
    echo "Restart"
    ;;
  *)
    echo "Please Sub Command"
    ;;
esac
```

### 4) 반복문

- **for**:

```bash
for 변수 in [범위(리스트 또는 배열, 묶음)]
do
  수행할 내용
done
```

-  사용
#직접 범위를 설정
```bash
for num in 1 2 3
do
  echo "$num"
done
```

done

```bash
# 범위를 변수에 설정
numbers="1 2 3"
for num in $numbers
do
  echo "$num"
done

# 디렉토리 경로로 설정 - 디렉토리 내의 파일이나 디렉토리를 순회
for file in "$HOME"/*
do
  echo "$file"
done

# { }를 이용하면 중간 숫자를 생략하는 것이 가능(중간에 .. 사용)
for num in {1..5..2}
do
  echo "$num"
done

# 배열 사용
array=("apple" "banana" "pineapple")
for fruit in "${array[@]}"
do
  echo "$fruit"
done

# C언어처럼 사용
for ((num=0; num<3; num++))
do
  echo "$num"
done
```

- **while**:

```bash
while [ $변수 연산자 $변수2 ]
do
  반복할문장
done
```

- 실습
```bash
num=0

while [ $num -lt 3 ]
do
  echo "$num"
  num=$((num+1))
done
```

### 5) 연산자

- 문자열 연산자

-z: 문자열의 길이가 0이면 참
-n: 문자열의 길이가 0이 아니면 참

- 숫자 비교 연산자

부등호 사용 가능: ( ) 안에서 사용
-eq, -ne, -gt, -lt, -ge, -le 도 사용 가능

- 문자열 비교 연산자

=, ==, !=, <, >

- 논리 연산자

-a: and 연산자
-o: or 연산자
&&: and 인데 이 연산자를 사용할 때는 각 조건식을 [ ] 나 [[ ]]를 사용
||: or 인데 이 연산자를 사용할 때는 각 조건식을 [ ] 나 [[ ]]를 사용

```bash
VAR1=10
VAR2=20
VAR3=30

if [ "$VAR1" -lt "$VAR2" -a "$VAR2" -gt "$VAR3" ]
then
  echo True
else
  echo False
fi
```

- 디렉토리 연산자

-d: 변수 유형이 디렉토리라면 참
-e: 변수 유형이 디렉토리 나 파일이라면 참

- 파일 연산자

-f: 파일 여부 확인
-L: 파일이면서 심볼릭 링크 여부 확인
-r: 파일이거나 디렉토리 이면서 읽기 권한이 있으면 참
-w
-x
-s: 파일이거나 디렉토리이면서 사이즈가  0보다 크면 참
-O: 파일이거나 디렉토리이면서 스크립트 실행 소유자 와 동일하면 참
-G: 파일이거나 디렉토리이면서 스크립트 실행 그룹이 동일하면 참

- 파일 여부 체크

```bash
ls -l /etc/localtime     #/usr/share/zoneinfo/Etc/UTC 의 심볼링 링크

ls -l /usr/share/zoneinfo/Etc/UTC 
```

```bash
FILE=/etc/localtime # 변수에 심볼릭 파일 링크 저장

# 현재 경로가 파일 경로가 맞는지 확인
if [ -f "$FILE" ]; then echo True; else echo False; fi

# 현재 경로가 심볼릭 링크 파일 경로가 맞는지 확인
if [ -L "$FILE" ]; then echo True; else echo False; fi

# 파일에 내용이 있는지 없는지 확인
touch sample.txt # 파일 크기가 0인 파일을 생성
FILE=sample.txt
if [ -s "$FILE" ]; then echo True; else echo False; fi # 파일의 크기가 0이 아니면 True, 0이면 False
```

- 파일 비교 연산자

-nt: 앞의 파일이 뒤의 파일보다 최신 파일이면 참
-ot: 이전 파일이면 참
-ef: 동일 파일이면 참

### 6) 정규 표현식

- **개요**:

- 리눅스 나 유닉스의 특별한 특징을 부여하는 문자들과 메타 문자들의 집합
- 정규 표현식을 알면 검색을 할 때 편리

- **메타문자**:

- `.`: 줄바꿈을 제외한 한 개의 문자와 일치
- `?`: 자신 앞에 나오는 정규 표현식이 없거나 하나가 일치하는 것을 찾는데 대부분 한 개의 문자와 매칭할 때 사용
- `*`: 바로 앞 문자열이나 정규 표현식에서 한 번 이상 반복되는 문자
- `+`: `*`과 유사하지만 반드시 하나 이상의 문자
- `{숫자}`: 정확하게 N번
- `{숫자,}`: N번 이상
- `{숫자1, 숫자2}`: 숫자1 이상 숫자2 이하
- `-`: 범위
- `^`: 라인의 시작(또는 문자 클래스에서 부정)
- 라인의 끝: 달러 기호
- 빈 줄: 시작과 끝이 바로 붙는 형태
- `[ ]`: 문자들의 집합
- `\`: 특수 문자를 원래의 문자 의미대로 해석
- `\b`: 단어
- `\B`: 줄

- 문자 클래스

[:alnum]: 알파벳이나 숫자
[:alpha:]: 알파벳
[:blank:]: 스페이스나 탭
[:cntrl:]: 제어문자
[:digit:]: 숫자
[:graph:]: 출력 가능한 문자
[:lower:]: 소문자
[:print:]: 출력 가능한 문자인데 스페이스 포함
[:punct:]: 문장 부호
[:space:]: 뉴라인 줄바꿈, 스페이스, 탭
[:upper:]: 대문자
[:xdigit:]: 숫자와 16진수 문자

- 정규 표현식 실습을 위한 파일을 다운로드 받아서 가상머신의 리눅스에 전송

- 텍스트 파일 다운로드(정규 표현식 연습을 위한 텍스트 파일): ggangpae1.tistory.com

- 터미널을 실행시켜서 파일을 SSH를 통해서 전송

```bash
scp -P 포트번호 파일경로 계정@IP:저장할경로
```

- **실습**:

- C로 시작하고 U로 끝나는 3글자를 조회

```bash
grep 'C.U' expression.txt
```

- C로 시작해서 e로 끝나는 4글자를 조회
```bash
grep 'C..e' expression.txt
```

-  q로 시작해서 ?로 끝나는 단어이어야 하며 중간에는 영문 소문자만 존재하는 단어 
```bash
grep -E 'q[[:lower:]]*\?' expression.txt
```

: ?는 메타문자라서 \와 함께 사용해야 함
grep에서 클래스는 [[ ]] 안에 표현
메타문자 나 확장 클래스를 사용할 때 E 옵션을 이용하는 것이 좋습니다.

- - 다음에 2가 1번 이상 나타나고 그 뒤에 -를 포함하는 문자열을 조회
```bash
grep -E '\-2+\-' expression.txt
```

- 알파벳 5글자로 시작하고 알파벳 뒤에 :으로 끝나는 단어가 있는 라인을 검색
```bash
grep -E '^[[:alpha:]]{5}:' expression.txt
```

- 라인 시작 시 알파벳 5글자 이상이며 뒤에 공백을 가진 단어가 있는 라인을 조회
```bash
grep -E '^[[:alpha:]]{5,}[[:space:]]' expression.txt
```

