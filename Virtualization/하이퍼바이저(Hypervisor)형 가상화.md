***

### 하이퍼바이저형 가상화 or Type 1 Hypervisor
***
host os에 Hypervisor를 설치하지 않고 Hypervisor 위에 guest os가 바로 탑재되는 형태를 의미 합니다.
즉 Hypervisor를 OS의 형태로 올리고 가상화를 진행합니다.
Sever 컴퓨터에 OS형태로 바로 Hypervisor 가 설치 되며 그위에 vm 들이 올라갑니다.

Type 1 Hypervisor는 중간 운영 체제 계층 없이 호스트 시스템의 하드웨어와 직접 상호 작용하기 때문에
어떠한 소프트웨어도 담겨 있지 않은 하드웨어를 가리키는 Bare Metal 이라는 표현을 앞에 붙여
Bare Metal Hypervisor라고도 합니다.


![](https://user-images.githubusercontent.com/61622657/218713091-165238b4-eba4-4edb-9dfe-c8531e48ba20.png)
출처 : https://www.nakivo.com/blog/hyper-v-virtualbox-one-choose-infrastructure/

### 장점
***

성능
Type 1 Hypervisor는 기본 하드웨어에 직접 엑세스 하기 때문에 Host OS를 거치는 Type 2 보다 성능적 이점을 갖게 됩니다

보안
Type 1 Hypervisor는 하드웨어에 직접 실행 되므로 Type 2 보다 공격할 측면이 상대적으로 적고 codebase도 적어지기 때문에 보안상 노출될 지점이 적어져 좀더 유리한 면을 가져갑니다.


### 단점
***

비용
Type 1의 경우 엔터프라이즈급 구축의 경우 비용이 많이 들 수도 있습니다.

하드웨어 호완성
특정 하이퍼바이저의 경우 하드웨어 요구사항이 엄격하여 일부 환경에서 사용이 제한될 수 있습니다.

복잡성
하드웨어 자체에 OS 형태로 올리는 것이기 때문에 설정 및 관리가 복잡하여 전문 지식과 기술을 필요로 할 수 있습니다.