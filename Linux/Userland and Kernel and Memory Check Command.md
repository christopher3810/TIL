****
linux를 포함한 대부분의 modern operating systems 에서는 메모리는 사용자 영역과 커널의 두 가지 주요 영역으로 나뉨. \
사용자 영역은 사용자 공간 프로세스에 할당되는 메모리 공간이고 커널은 운영 체제 및 커널 공간 프로세스에 할당되는 메모리 공간임.

<br>

### 사용자 영역 메모리 
사용자 공간 메모리는 사용자 공간 애플리케이션, 라이브러리 및 사용자 수준 시스템 데몬에서 사용하며 `Userland or UserSpace` 라고 부름.\
여기에는 실행 중인 모든 프로그램, 애플리케이션 및 사용자 수준 서비스가 포함됨. \
사용자 영역의 메모리 사용량은 운영 체제의 메모리 관리자에 의해 제어되며, 메모리 관리자는 필요에 따라 다른 사용자 공간 프로세스에 메모리를 할당 및 할당 해제함. \
일반적으로 사용자 영역 메모리 사용량은 시스템 힙에서 메모리를 할당하는 C 표준 라이브러리 및 기타 사용자 공간 라이브러리에 의해 관리됨. \
사용자 영역의 프로세스는 system call을 통해서 커널 스페이스로 접근할 수 있음.

<br>

### 커널 메모리 
커널 메모리는 운영 체제 및 커널 공간 프로세스에서 사용함. \
여기에는 커널 자체, 커널 수준 데몬, 장치 드라이버 및 시스템 수준 서비스가 포함됨. \
커널 메모리 사용량은 커널의 자체 메모리 관리자가 관리하며, 이 메모리 관리자는 필요에 따라 다른 커널 공간 프로세스에 메모리를 할당하고 할당 해제함. \
커널 메모리 공간은 일반적으로 사용자 프로세스가 커널의 중요한 데이터 구조에 액세스하거나 손상시키지 못하도록 사용자 프로세스로부터 보호되고 격리됨.

<br>

### 분리 하는 것의 장점

`보안` \
사용자 영역 프로세스가 커널에서 분리되어 악성 프로그램이나 버그가 있는 프로그램이 중요한 시스템 리소스를 손상시킬 위험이 줄어들게됨. \
사용자 프로세스는 시스템 호출과 같은 제어된 인터페이스를 통해서만 커널 서비스 및 리소스에 액세스할 수 있음.

`안정성` \
사용자 프로세스와 커널 프로세스는 실행 컨텍스트와 메모리 공간이 다르므로 한 프로세스가 전체 시스템을 중단시킬 위험이 줄어들게됨. \
사용자 프로세스가 충돌하거나 응답하지 않는 경우 커널은 다른 프로세스나 운영 체제 자체에 영향을 주지 않고 해당 프로세스를 종료하거나 다시 시작할 수 있게됨.

<br>

### 구조

application 구조도

![userlandAndKernelArch](https://user-images.githubusercontent.com/61622657/224852563-c45b25fb-ab34-416c-91df-9aa83570733f.png)
출처 : https://www.educative.io/answers/what-is-the-difference-between-the-kernel-and-user-spaces

Userland 와 Kernel의 구조도.

![userlandAndKernel](https://user-images.githubusercontent.com/61622657/224851852-87bf1087-3ca0-4caa-b32a-02c2d88cc1c1.png)
출처 : wiki

<br>

### 메모리

커널 메모리 사용량은 시스템의 특정 구성과 활성 커널 공간 프로세스의 수에 따라 달라지게 됨. \
시스템 관리자가 메모리 사용량을 모니터링하고 관리하는 데 도움이 되는 몇 가지 도구와 메커니즘을 제공함.

### top

top 명령은 총 메모리 양, 사용된 메모리, 사용 가능한 메모리, 개별 프로세스별 메모리 사용량 등 시스템의 메모리 사용량에 대한 실시간 정보를 표시하는 일반적으로 사용되는 명령

```linux
top
```

## ps

ps 명령은 메모리 사용량을 포함하여 실행 중인 프로세스에 대한 정보를 표시하는 또 다른 유용한 명령임. \
개별 프로세스의 메모리 사용량을 표시하려면 다음 명령을 사용.

```linux
ps aux --sort=-%mem
```

- `ps`는 실행 중인 프로세스에 대한 정보를 표시하는 명령
- `aux`는 출력 형식을 지정하는 옵션.
	- ``-a`` 옵션은 현재 터미널 세션과 관련된 프로세스뿐만 아니라 모든 프로세스에 대한 정보를 표시.
	- ``-u`` 옵션은 프로세스를 소유한 사용자, 사용 중인 CPU 및 메모리 양을 포함하여 각 프로세스에 대한 자세한 정보를 표시.

- ``--sort=-%mem`` 은 각 프로세스가 사용하는 메모리 비율에 따라 출력을 정렬하는 옵션으로, 가장 많은 메모리를 사용하는 프로세스가 먼저 나열. \
  mem 앞의 -는 내림차순을 나타내므로 가장 많은 메모리를 사용하는 프로세스가 먼저 표시.


### 참조
https://en.wikipedia.org/wiki/User_space_and_kernel_space \
https://www.educative.io/answers/what-is-the-difference-between-the-kernel-and-user-spaces
