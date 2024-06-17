
데이터 는 생성된 곳에서 분석, 활용할 수 있는 곳으로 옮겨져야 함.
데이터를 이동시키는 작업에 더 적은 노력을 들일수록 핵심 비즈니스에 더 집중할 수 있음.
데이터를 어떻게 이동시킬지에 대한 문제는 데이터 그 자체 만큼이나 중요하다.

### 왜 이름이 카프카?

>Confluent 공동 창업자 Jay Kreps는 
>Kafka 라는 소프트웨어가 write 작업에 최적화 되어 있기 때문에 작가 이름을 사용하는 것이 옳다고 판단,
>대학시절 문학 수업때 프란츠 카프카의 작품을 좋아 했기 때문에 사용.


### 메시지?

>한 시스템에서 또 다른 시스템으로 process 되는 게 예상되어지는 information의 한 조각.
>Ex) Data Payload, File, Meta data 등등

> 카프카 입장에서는 키 라는 메타 데이터 값을 갖는 바이트 배열.

### 카프카가 필요 했었던 이유?
---

> Kafka 는 무엇을 위해 Design 되었을 까?


### 간단하게 보는 Message Queue 구조
---

#### Topic


### 카프카를 활용할 수 있는 방안 
---

1. 메시지 큐
   
   pub/ sub pattern Message Queue

<img width="720" alt="스크린샷 2024-06-17 오후 7 07 26" src="https://media.github.dop.admin.rnd.aws.kakaoinsure.net/user/441/files/b0e7e418-81dc-4004-bf8d-f792d8644342">
Topic 별로 consumer 가 topic을 consum 할 수 있음.

대응 되는 AWS Service : [AWS SQS](https://aws.amazon.com/ko/sqs/features/)

2. Event Broker

<img width="725" alt="스크린샷 2024-06-17 오후 7 07 03" src="https://media.github.dop.admin.rnd.aws.kakaoinsure.net/user/441/files/201d3c8a-2b65-4488-b644-efe290c1e7cb">
Event를 push 해줄 수 있음.

대응 되는 AWS Service : [AWS Event Bridge](https://aws.amazon.com/ko/eventbridge/)

3. 실시간 스트리밍 데이터 처리


### 현재 존재하는 Message Queue 들의 차이점
---

>Redis는 pub/sub 기반의 Message Queue로 활용이 가능 할까?


>RebbitMQ 는 영속성이 정말로 보장이 안될까?


> Kafka는 위 경쟁 재품들과 다른 어떤 메리트를 갖고 있는가?
