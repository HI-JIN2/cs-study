# Booting

## 1. 리눅스 부팅 시스템

### 1) 리눅스 부팅

- **개요**:

- 리눅스의 부팅은 PC 전원을 켜는 순간부터 리눅스가 완전히 동작해서 로그인 프롬프트가 출력될 때 까지를 의미
- 부팅에 필요한 서비스가 시작되도록 설정해야 하고 부팅 과정에서 문제가 발생한 경우 이를 해결하기 때문에 이해를 해야 함
- 부팅 과정

전원ON -> BIOS 단계 -> Boot Loader -> Kernel Initialize -> systemd service -> login prompt

여기서 첫번째 와 두번째 단계는 리눅스와는 무관

- 부팅 과정

- BIOS(Basic Input Output System) 단계
ROM에 저장되어 있어서 ROM BIOS 라고도 함
컴퓨터에 장착된 키보드, 디스크 상태를 확인하고 부팅할 장치를 선택해서 부팅 디스크의 섹터에서 512B를 로딩하는데 이를 MBR(Master Boot Record)라고 하며 여기에 디스크의 어느 파티션에 부팅 프로그램(Boot Loader)이 있는지에 대한 정보를 저장하고 부트 로더를 찾아서 메모리에 로딩

- 부트 로더
운영체제 중에서 부팅할 운영체제나 부팅 방법을 선택할 수 있도록 메뉴를 제공

우분투에서는 부트 로더로 GRUB를 사용하는데 기본적으로 멀티 부팅이 아니라면 GRUB 메뉴를 출력하지 않고 바로 부팅 작업을 진행
부팅할 GRUB 메뉴를 출력하고자 하면 /etc/default/grub 파일을 수정하면 되는데 GRUB_TIMEOUT_STYLE=hidden 으로 되어 있어서 부팅 메뉴가 출력되지 않는데 이 부분을 주석으로 처리하면 부팅 메뉴가 화면에 출력되고 GRUB_TIMEOUT=0 부분의 0을 수정해서 값을 설정하면 그 값이 메뉴가 출력되는 시간입니다.
이 파일을 수정한 경우 sudo update-grub 명령을 수행

리눅스 커널을 메모리에 로딩하는 역할을 수행
리눅스 커널은 /boot 디렉토리에 vmlinuz-버전명으로 제공
우분투를 업데이트하면 새로운 버전의 커널이 추가로 생성

- 커널 초기화 단계
시스템에 연결된 하드웨어를 사용할 준비

- systemd 서비스 시작
소프트웨어 사용할 준비

부팅 끝

GUI를 사용하면 부팅 과정에서 보여지는 메시지가 출력되지 않고 부트 스플래시라는 이미지가 출력됩니다.
GUI 환경에서 부팅 메시지를 출력하고자 하는 경우에는 /etc/defatult/grup 파일에서 GRUB_CMDLINE_LINUX_DEFAULT="quiet splash" 부분의 quiet를 삭제한 후 sudo update-grub를 수행하면 됩니다.
이 설정은 GUI 리눅스에서 부팅 실패하는 경우 설정해서 확인합니다.

부팅한 후 이 메시지를 확인하고자 하면 /var/log/dmsg 나 /var/log/bootstrap.log
