# 사용자 계정을 생성하는 스크립트를 만들어서 실행

### 1) 필요한 정보

- 사용자 계정

- 사용자 비밀번호

- 관련 명령: useradd 와 passwd

### 2) 수행 과정

- 사용자 계정 과 패스워드를 입력받음

- 입력 정보가 없으면 에러 메시지를 출력하고 셸 스크립트를 종료

- 여러 명의 사용자 계정을 생성하는 경우는 for 문을 사용

- 기존 사용자 계정이 존재하는지 확인

- 사용자 계정이 없으면 생성하고 패스워드를 설정

- 사용자 계정이 존재하면 계정이 있다고 메시지를 출력

### 3) 스크립트 작성 - adduser-script.sh

```bash
#!/bin/bash

if [[ -n $1 ]] && [[ -n $2 ]]
then
  UserList=($1)
  Password=($2)

  for (( i=0; i < ${#UserList[@]}; i++ ))
  do
    if [[ $(cat /etc/passwd | grep ${UserList[$i]} | wc -l) == 0 ]]
    then
      sudo useradd ${UserList[$i]}
      echo ${Password[$i]} | passwd ${UserList[$i]} --stdin
    else
      echo "this user ${UserList[$i]} is existing"
    fi
  done
else
  echo -e 'Input User ID and Password\n"user01 user02" "pw01 pw02"'
fi
```

### 4) 실행

```bash
bash adduser-script.sh
```
