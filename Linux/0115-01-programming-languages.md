# Programming Language 사용

### 1) C Programming

- **GCC**:

- 오픈 소스 개발자들이 리눅스 용 컴파일러를 개발했는데 이 컴파일러가 GCC
- 우분투에는 기본 gcc 컴파일러가 설치되어 있음 - 없으면 설치(sudo apt install gcc)

- 소스 코드 작성 및 실행

- vim helloworld.c

```c
#include <stdio.h>

int main() {
        printf("Hello World\n");
        return 0;
}
```

- 컴파일 및 실행
```bash
gcc -o helloworld helloworld.c
./helloworld
```

- **make**:

- 실제 애플리케이션은 많은 파일로 복잡하게 구성되어 있기 때문에 gcc로 일일이 컴파일해서 하나의 실행 파일로 만드는 것은 매우 번거로운 작업인데 이를 해결하기 위한 것이 make
- makefile에 설정된 정보를 읽어서 여러 소스 파일을 컴파일하고 링크해서 최종 실행 파일을 생성
- 소스 코드로 배포되는 많은 오픈 소스 소프트웨어는 소스 코드 와 makefile을 같이 배포
- 설치: sudo apt install make
- 소스 코드 작성
one.c

```c
#include <stdio.h>

extern int two();

int main() {
    printf("Go to Module Two---\n");
    two();
    printf("End of Module One\n");
    return 0;
}
```

two.c

```c
#include <stdio.h>

int two() {
    printf("In Module Two ---\n");
    return 0;
}
```

- makefile 작성: vim makefile
```makefile
TARGET=one
OBJECTS=one.o two.o

${TARGET} : ${OBJECTS}
    gcc -o ${TARGET} ${OBJECTS}

one.o : one.c
    gcc -c one.c

two.o : two.c
    gcc -c two.c
```

```bash
# 실행 파일 생성

make

# 실행

./one
```

### 2) Java Programming

- **설치**

```bash
sudo apt update
sudo apt install openjdk-21-jdk
```

- **설치 확인**

```bash
javac -version
java -version
```

- vim HelloWorld.java

```java
public class HelloWorld{
    public static void main(String [] args){
        System.out.println("Hello Java\n");
    }
}
```

- 컴파일 및 실행

```bash
javac HelloWorld.java
java HelloWorld
```

### 3) Python 프로그래밍

- **설치**:

- 확인: python3 --version
- 업데이트: sudo apt upgrade python3
- venv 모듈 설치: sudo apt install python3-venv

- 코드 작성 및 실행

```bash
mkdir pythontest
cd pythontest
python3 -m venv myvenv
source myvenv/bin/activate
nano main.py
python3 main.py
pip freeze > requirements.txt
```

```python
print("Hello Python")
```
파이썬은 외부 패키지를 파이썬 환경에 설치해서 사용을 합니다.
다른 곳에서 실행을 하려면 사용한 모든 패키지가 설치되어 있어야 합니다.
수동으로 직접 설치하는 것은 번거롭고 개발자의 실수를 유발할 수 있습니다.
패키지 정보를 텍스트 파일로 내보내서 텍스트 파일의 내용을 읽어서 설치하는 것을 권장합니다.
이미지를 만들 때나 소스 코드를 업로드할 때 requirements.txt 파일을 반드시 같이 업로드 해야 합니다.

### 4) node.js

- **설치**:

- 설치 스크립트 다운로드

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

- 현재 쉘에 NVM 적용
source ~/.bashrc

- 최신 버전 설치
nvm install --lts

- 설치 확인
node -v
npm -v

- 프로젝트 생성 및 실행

- 디렉토리 생성 및 이동
```bash
mkdir hello-node
cd hello-node
```

- 프로젝트 생성(node는 모듈을 프로젝트 안에 설치하고 모듈 정보는 package.json에 존재, 
노드 프로젝트는 modules 디렉토리에 모듈을 설치하는데 이 디렉토리의 내용은 이미지를 만들 때 또는 코드 업로드 사이트에 업로드 할 필요가 없고 package.json 만 업로드해서 이 파일의 내용을 실행하면 됩니다.)
```bash
npm init -y
```

- app.js 작성
```js
const http = require('http')

const hostname = '127.0.0.1'
const port = 3000

const server = http.createServer((req, res) => {
  res.statusCode = 200
  res.setHeader('Content-Type', 'text/plain; charset=utf-8')
  res.end('Hello Node.js Server')
})

server.listen(port, hostname, () => {
  console.log('Server Running')
})
```

- 실행
```bash
node app.js
```

- 확인
다른 터미널을 실행시켜서 리눅스에 접속한 후 curl http://127.0.0.1:3000 으로 확인

### 5) Go(도커 와 쿠버네티스를 개발한 언어)

- 설치(압축된 파일을 다운로드 받아서 압축을 해제한 후 PATH에 추가해서 사용)

```bash
curl -OL https://golang.org/dl/go1.22.0.linux-amd64.tar.gz
```

- 압축 해제

```bash
sudo tar -C /usr/local -xzf go1.22.0.linux-amd64.tar.gz
```

- go 관련 명령어(/usr/local/go/bin)를 어디서든 사용하기 위해서 PATH에 등록

```bash
echo 'export PATH=$PATH:경로' >> ~/.profile

echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.profile
echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> ~/.profile
```

- 환경 설정 파일(~/.profile)을 수정한 후 바로 적용하기

source ~/.profile

- **설치 확인**: go version

- 프로젝트 생성 및 실행

- 작업 디렉토리 생성 및 이동
mkdir hello-go
cd hello-go

- hello-go 라는 이름으로 모듈을 시작
go mod init hello-go

- main.go 파일 작성

```bash
import "fmt"
```

func main(){
  fmt.Println("Hello Go World")

}

- 실행
go run main.go

