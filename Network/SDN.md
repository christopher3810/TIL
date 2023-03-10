***

## Software-Defined Networking(SDN)이란?

Software-Defined Networking (SDN)은 API로 구동할 수 있는 소프트웨어 컨트롤러를 사용하여 하드웨어 인프라와 통신하여 네트워크 트래픽을 전달하는 네트워킹 접근 방식. 

![sdn](https://user-images.githubusercontent.com/61622657/224517757-1dcc4bd9-7ef2-433b-a500-1f1e10aba85d.png) 

출처 : https://news.sktelecom.com/98995

전용 하드웨어 장치(예: 라우터 및 스위치)를 사용하여 네트워크 트래픽을 제어하는 기존 네트워크와는 다름

네트워크 장비의 Control Plane(제어부)와 Data Plane(전송부)을 분리시키고 하나로 중앙 집중화를함.

라우터와 같은 네트워크 장비들은 전송부만을 가지고 데이터 전송 기능에 집중시킴.

<br>

## 작동 방식

![sdn_arch](https://user-images.githubusercontent.com/61622657/224517810-c8836573-89c0-41f8-b892-74e72794cd00.png)

출처 : Unveil the Myths About SDN Switch FS Community

- 애플리케이션 - 네트워크에 대한 정보를 전달하거나 특정 리소스 가용성 또는 할당에 대한 요청을 처리

- SDN 컨트롤러 - 데이터 패킷의 대상을 결정하기 위해 앱과의 통신을 처리합니다. 컨트롤러는 SDN 내의 로드 밸런서.
	- SDN 컨트롤러는 패킷을 전송할 위치를 스위치에 알려줌.\
	  경우에 따라 소프트웨어 또는 하드웨어에 내장된 가상 스위치가 물리적 스위치를 대체하기도 함.\
	  이렇게 하면 데이터 패킷과 가상 머신의 목적지를 확인하여 패킷을 이동하기 전에 문제가 없는지 확인할 수 있는 단일 지능형 스위치로 기능이 통합됨.

- 네트워킹 장치 - 패킷 라우팅 방법에 관한 컨트롤러의 지시를 받음.

- Open-source technologies  - OpenFlow와 같은 프로그래밍 가능한 네트워킹 프로토콜은 SDN 네트워크의 네트워크 디바이스 간에 트래픽을 전달함. 


이러한 구성 요소를 결합함으로써 네트워크를 보다 단순하고 중앙 집중화된 방식으로 관리할 수 있음.\
SDN 네트워크의 두뇌라고 할 수 있는 컨트롤러를 구현하여 클라우드 또는 온프레미스의 네트워크 하드웨어 위에 계층화함.\
이를 통해 자동화의 일종인 정책 기반 관리를 사용하여 네트워크 제어를 직접 관리할 수 있음. 



## SDN의 장점

네트워크 제어 및 관리의 중앙 집중화로 인해 많은 이점을 제공합니다

- 가시성- 기존 환경에서는 클라우드에 있든 물리적으로 데이터센터에 있든 라우터나 스위치는 옆에 있는 네트워크 디바이스의 상태만 파악할 수 있었음.\
  라우터와 스위치와 같은 네트워킹 디바이스에는 트래픽 전달 방법에 대한 결정을 내리는 자체 내부 로직이 있음.\
  하지만 SDN 네트워크에서는 중앙 집중식 컨트롤러가 트래픽의 흐름을 관리하고 트래픽 전달 방법에 대한 결정을 내림.\
  네트워크 리소스를 보다 효율적으로 사용할 수 있을 뿐만 아니라 네트워크 트래픽에 대한 제어 및 사용자 지정이 더욱 용이해짐.

- 네트워크 제어의 용이성 - 패킷 포워딩 기능을 데이터 플레인에서 분리하면 직접 프로그래밍하고 네트워크를 더 간단하게 제어할 수 있음. \
  여기에는 이더넷이나 방화벽과 같은 네트워크 서비스를 실시간으로 구성하거나 가상 네트워크 리소스를 신속하게 할당하여 중앙 집중식 한 곳에서 네트워크 인프라를 변경하는 것이 포함될 수 있음.

- 민첩성(Agility) - SDN은 동적 로드 밸런싱을 통해 필요와 사용량 변동에 따라 트래픽 흐름을 관리할 수 있으므로 지연 시간을 줄여 네트워크의 효율성을 높일 수 있음.

- 유연성(Flexibility) - 소프트웨어 기반 제어 계층을 통해 네트워크 운영자는 네트워크를 제어하고, 구성 설정을 변경하고, 리소스를 프로비저닝하고, 네트워크 용량을 늘릴 수 있는 유연성을 확보할 수 있음. 

- 네트워크 보안에 대한 제어 강화 - SDN을 사용하면 네트워크 관리자가 한 곳에서 정책을 설정하여 워크로드 유형별 또는 네트워크 세그먼트별로 네트워크 전반의 액세스 제어 및 보안 조치를 결정할 수 있음. 

- 네트워크 설계 및 운영 간소화 - 관리자는 단일 프로토콜을 사용하여 중앙 컨트롤러를 통해 다양한 하드웨어 장치와 통신할 수 있음.


### 참조
https://www.ibm.com/topics/sdn \
https://www.vmware.com/topics/glossary/content/software-defined-networking.html \
https://www.cisco.com/c/en/us/solutions/software-defined-networking/overview.html#~resources \
https://www.ciena.com/insights/what-is/What-Is-SDN.html \
https://news.sktelecom.com/98995
