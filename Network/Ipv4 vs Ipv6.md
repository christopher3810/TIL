***

### IPv4 vs. IPv6 ?.

`IPv4(Internet Protocol version 4)`는 점이 있는 십진법 표기법을 사용하는 32비트 주소 체계. 

각 IPv4 주소는 0에서 255 사이의 네 개의 십진수로 구성되며, 점으로 구분. 

예를 들어, 192.168.1.1은 IPv4 주소.

IPv4는 이론적으로 최대 2^32 (약 43억)개의 고유한 주소를 가질 수 있음. 

그러나 주소 블록의 비효율적인 할당과 인터넷의 빠른 성장으로 인해 사용 가능한 IPv4 주소 공간은 거의 소진되었음.

`IPv6(Internet Protocol version 6)`는 16진법 표기법을 사용하는 128비트 주소 체계. 

각 IPv6 주소는 콜론으로 구분된 네 개의 16진수로 구성된 여덟 개의 그룹으로 구성. 

예를 들어, 2001:0db8:85a3:0000:0000:8a2e:0370:7334는 IPv6 주소.

IPv6는 이론적으로 최대 2^128 (약 3.4 x 10^38)개의 고유한 주소를 가질 수 있어, IPv4보다 훨씬 큰 주소 공간을 제공. 

이 방대한 주소 풀은 주소 고갈 위험을 없애고 주소 블록의 효율적인 할당을 가능.
![]()
### IPv4 vs. IPv6  Header

![ipv4andipv6](https://user-images.githubusercontent.com/61622657/231443786-67f749b0-c7ef-4306-b8c8-85c1c1a63cfe.png)

`Pv4의 헤더 구조`는 가변 길이로 설계. 

이는 Options 필드를 통해 추가적인 기능을 확장할 수 있는 유연성을 제공하기 때문. 

그러나 이러한 가변 길이 헤더 구조는 라우터에서 패킷 처리 과정이 복잡해지고, 라우팅 성능이 저하될 수 있음.

`IPv6는 헤더 구조`를 간소화하고 고정 길이로 설계. 

이를 통해 라우터에서 패킷 처리 과정이 단순화되어 성능 향상을 기대할 수 있음. 

또한, IPv6는 필요한 추가 기능을 Next Header 필드를 통해 확장 헤더로 구현하여 유연성을 제공.

이러한 설계 방식은 헤더 처리의 복잡성을 줄이고, 효율적인 패킷 처리를 가능하게 함.

<br>

### NAT(Network Address Translation) 측면

IPv4에서는 주소 공간이 부족하기 때문에 NAT(Network Address Translation)가 널리 사용. 

NAT는 사설 주소와 공인 주소 간에 IP 주소를 변환하여 여러 기기가 동일한 공인 IP 주소를 공유할 수 있게 해줌. 

반면에 IPv6에서는 거의 무한한 주소 공간이 제공되므로, NAT를 사용할 필요가 크게 줄어듬.

<br>

### Packet Fragment 측면

Fragmentation은 패킷이 네트워크를 통해 전송될 때, 최대 전송 단위(Maximum Transmission Unit, MTU)보다 큰 패킷을 작은 조각으로 나누는 과정. 

이렇게 나눈 조각들은 목적지에 도착한 후 다시 원래의 크기로 재조립됨.

IPv4에서는 발신자와 중간 라우터 모두 fragmentation을 수행할 수 있습니다. 그러나 이는 중간 라우터에 부하를 줄 수 있으며, 재조립 과정에서 문제가 발생할 가능성이 있음.

IPv6에서는 발신자만이 fragmentation을 수행할 수 있음. 

이는 중간 라우터의 처리 부하를 줄이고, 패킷의 전송 효율을 높이는 데 도움이 됨. 

발신자는 Path MTU Discovery 프로토콜을 사용하여 경로 상의 최소 MTU를 파악하고, 이를 기반으로 패킷을 적절한 크기로 나누어 전송.

<br>

### ipv4 vs ipv6 비교 테이블

| 항목 (Item) | IPv4 | IPv6 |
| --- | --- | --- |
| 주소 (Address) 길이 | 32비트 (4바이트) | 128비트 (16바이트) |
| 주소 (Address) 공간 | 약 43억 개 | 약 3.4 x 10^38 개 |
| 주소 (Address) 표기 | 점표기법 (Dotted Decimal Notation) | 콜론표기법 (Colon Hexadecimal Notation) |
| 자동 구성 (Autoconfiguration) | 제한적이고 복잡하다 (DHCP 필요) | 네트워크 자동 구성 가능 |
| NAT (Network Address Translation) | 널리 사용됨 | 거의 사용되지 않음 |
| 보안 (Security) | IPsec는 선택 사항 | IPsec는 내장 |
| 헤더 구조 (Header Structure) | 복잡하고 가변적인 길이 | 간소화되고 고정된 길이 |
| 프래그먼트(Fragmentation) | 송신자와 중간 라우터에서 수행 가능 | 오직 송신자에서만 수행 가능 |
