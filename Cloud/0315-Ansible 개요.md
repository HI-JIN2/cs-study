**IaC**

# 1. Ansible 개요

## 1) Ansible 특징
- **Agentless**: 대상 서버에 에이전트를 설치할 필요 없이 SSH를 통해 명령 수행
- **Idempotency (멱등성)**: 동일한 설정을 여러 번 적용해도 최종 상태가 동일하게 유지됨
- **간단함 (YAML)**: 사람이 읽기 쉬운 YAML 언어를 사용하여 설정 정의

## 2) Ansible 구성 요소
- **Control Node**: 앤서블이 설치되어 명령을 내리는 머신 (Linux 기반)
- **Managed Node**: 관리 대상 머신
- **Inventory**: 관리 대상 서버들의 리스트와 그룹 정의 파일 (`/etc/ansible/hosts`)
- **Module**: 실제 작업을 수행하는 단위 (예: `apt`, `copy`, `service`, `shell`)
- **Playbook**: 여러 태스크(Task)를 묶어서 실행하는 시나리오 파일

## 3) Ansible Ad-hoc Command
- 한 번의 명령으로 간단한 작업을 수행할 때 사용
```bash
# 모든 서버에 핑 확인
ansible all -m ping

# 특정 그룹의 서버에 패키지 설치
ansible webservers -m apt -a "name=nginx state=present" --become
```

## 4) Ansible Playbook
- `ansible-playbook` 명령을 통해 실행

### 플레이북 예제: `webserver.yaml`
```yaml
- name: Install and Start Nginx
  hosts: webservers
  become: yes
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: latest
    - name: Start Nginx Service
      service:
        name: nginx
        state: started
        enabled: yes
```

### 실행
```bash
ansible-playbook webserver.yaml
```

## 5) Variable & Template (Jinja2)
- 환경에 따라 다른 설정을 적용하기 위해 변수를 사용
- `template` 모듈과 Jinja2 파일(`.j2`)을 사용하여 동적인 설정 파일 생성 가능
