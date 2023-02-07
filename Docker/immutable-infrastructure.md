# Immutable Infrastructure

<br />

### 🤔 Immutable Infrastructure란 무엇일까?

Immutable Infrastructure는 말그대로 mutable 의 반대 이다.\
Immutable Infrastructure는 말그대로 수정할 수 없다.\
특정 요구사항에 맞게 구성요소를 구축하며 앞으로 수정할 여지가 없다.

server를 변경해야 하는 경우 요구사항에 맞춰서 완전히 새로운 infrastructure가 설치 된다.\
infrastructure는 업데이트된 software와 configuration으로 구성된다.\
가상화 기술은 Immutable infrastructure를 고안하는데 중요한 역할을 해왔다.\
templates을 사용하여 많은 인스턴스를 구성할 수 있다.
public cloud에 있어서 중추로 작용한다.\
ex) Amazon Ec2의 경우 Amazon image AMI를 활용 EC2를 구성할 수 있다.

docker가 가벼운 image와 격리된 container를 활용하여 Immutable Infrastructure를 잘 적용하고 있다.

Immutable Infrastructure의 장단점이 무엇일까?

장점
1. 테스트 개발에서 테스트 반복까지 가능하다.
2. 구성 드리프트가 발생할 가능성이 없다.
3. 특정 서버 인스턴스의 문제가 발생할 시 문제의 원인이 되는 인스턴스를 삭제하고 다시 띄우는 것으로 에러 수정이 가능하며 지원 활동이 감소할 수 있다.
4. *SnowFlake 서버를 방지한다.


> *SnowFlakeServer : 실제 구성이 원래 구성된 서버와 조금씩 또는 크게 달라져 고유하고 관리하기 어려운 서버를 의미합니다.
눈송이를 보면 모양이 조금씩 다른것에서 의미를 따왔음.
SnowFlakes DB service 회사의 서버가아님.



단점
1. 구성이 변경될 때마다 서버를 재구성해야 할수도 있으며 시간이 오래걸리는 작업이 될 수 있다.

2. 데이터 저장소를 로컬화하는게 아니라 외부에 둬야 할 수도 있기 때문에 (에러시 해단 인스턴스를 제거하고 다시띄움) 스토리지에 따른 추가적인 리소스가 필요 할 수도 있다.

<br />

Immutable Infrastructure를 사용하는 Tool들

1. Docker
2. Kubernetes
3. Terraform
4. Spinnaker
5. Packer
6. [AWS Cloud Formation](https://aws.amazon.com/ko/blogs/mt/leveraging-immutable-infrastructure-nubank/)



<br/>

### 참조
[linked-in history-infrastructure-immutable-muttable-tools](https://www.linkedin.com/pulse/history-infrastructure-immutable-mutable-tools-achieve-amit-kalia)

[hasicorp-immutable-infrastructure](https://www.hashicorp.com/resources/what-is-mutable-vs-immutable-infrastructure)

[what is immutable infrastructure](https://www.digitalocean.com/community/tutorials/what-is-immutable-infrastructure)

[snowflake server](https://martinfowler.com/bliki/SnowflakeServer.html)