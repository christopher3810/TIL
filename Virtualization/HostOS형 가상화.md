***

## HostOS형 가상화
****

Host OS 형 가상화는 물리 서버에 OS가 설치되어 가상 서버를 실행하는 기술이다.
물리 서버 OS에 Host OS형 가상화 소프트웨어를 설치합니다.

Type 2 Hypervisor 형 가상화라고도 합니다.

즉 Hypervisor를 OS의 형태로 가상화 하는 것이 아닌 Host Os위에서 일종의 Application의 형식으로 가상화를 한다고 생각하면 됩니다.

![](https://user-images.githubusercontent.com/61622657/218713091-165238b4-eba4-4edb-9dfe-c8531e48ba20.png)
출처 : https://www.nakivo.com/blog/hyper-v-virtualbox-one-choose-infrastructure/


Host os형 가상화 소프트웨어로는

1. VMware Horizon, VMwareFusion
2. Virtual Box
3. Paralles Desktop
4. 윈도우 위에서 돌아가는 Hyper-V

등이 있습니다.


### 장점
***
사용이 편하다
기본적으로 host os 위에서 소프트웨어 형식으로 돌아가기 때문에 설정이나 사용이 간편합니다.
데스크톱, 노트북을 포함한 광범위한 컴퓨터 시스템에서 사용이 가능합니다.

비용
특수한 하드웨어가 필요 없이 os 위에서 돌아가기 때문에 비용적인 이점이 존재합니다.

### 단점
***
성능
하이퍼바이저가 호스트 운영 체제와 리소스를 공유해야 하므로 호스팅된 가상화는 성능에 영향을 미칠 수 있습니다. 이로 인해 성능이 저하되고 전체 시스템 리소스가 저하될 수 있습니다.

제한된 확장성
호스팅된 가상화는 호스트 운영 체제의 리소스에 종속되기 때문에 다른 유형의 가상화보다 확장성이 낮습니다. 따라서 리소스를 많이 사용하는 대규모 애플리케이션이나 워크로드를 실행하는 데 적합하지 않을 수 있습니다.


### 참조
***
https://www.ibm.com/topics/hypervisors
https://selfish-developer.com/entry/%EA%B0%80%EC%83%81%ED%99%94-%EA%B8%B0%EC%88%A0%EC%9D%98-%EC%9C%A0%ED%98%95