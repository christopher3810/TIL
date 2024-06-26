
데이터를 이동시키는 작업에 더 적은 노력을 들일수록 핵심 비즈니스에 더욱 집중할 수 있다.
데이터를 어떻게 이동시키느냐의 문제는 데이터 그 자체만큼이나 중요한 것이다.

### 왜 이름이 카프카?

>Confluent 공동 창업자 Jay Kreps는 
>Kafka 라는 소프트웨어가 write 작업에 최적화 되어 있기 때문에 작가 이름을 사용하는 것이 옳다고 판단,
>대학시절 문학 수업때 프란츠 카프카의 작품을 좋아 했기 때문에 사용.


### 메시지?

>한 시스템에서 또 다른 시스템으로 process 되는 게 예상되어지는 information의 한 조각.
>Ex) Data Payload, File, Meta data 등등

> 카프카 입장에서는 키 라는 메타 데이터 값을 갖는 바이트 배열.

### Kafka 는 어떤 문제를 해결하기 위해 Design 되었나
---

```mermaid
graph TD
    subgraph Server
        A[Server 1]
        B[Server 2]
    end

    subgraph Metric Server
        C[Metric Server 1]
    end

    %% Direct connections between publishers and subscribers
    A -- "Metric 1" --> C
    B -- "Metric 2" --> C
```

#### Point to Point

```mermaid
graph TD
    subgraph Servers
        S1[Server 1]
        S2[Server 2]
        S3[Server 3]
        S4[Server 4]
        S5[Server 5]
    end

    subgraph MetricServers
        M1[Metric Server 1]
        M2[Metric Server 2]
    end

    subgraph OtherServices
        OS1[Service A]
        OS2[Service B]
        OS3[Service C]
        OS4[Service D]
        OS5[Service E]
    end

    %% Direct connections between servers and metric servers
    S1 -- "Metric 1" --> M1
    S1 -- "Metric 2" --> M2
    S2 -- "Metric 3" --> M1
    S2 -- "Metric 4" --> M2
    S3 -- "Metric 5" --> M1
    S4 -- "Metric 6" --> M2
    S5 -- "Metric 7" --> M1
    S5 -- "Metric 8" --> M2

    %% Additional connections from other services to servers
    OS1 -- "Function X" --> S1
    OS2 -- "Function Y" --> S2
    OS3 -- "Function Z" --> S3
    OS4 -- "Function W" --> S4
    OS5 -- "Function V" --> S5

```


연결을 추적하기 힘든 구조로 점점 변화해 갑니다.

해결 방법은 ?
가운데에 메시지 큐나 프로세스 간 통신 채널을 만드는 방법이 존재함

```mermaid
graph TD
    subgraph Servers
        S1[Server 1]
        S2[Server 2]
        S3[Server 3]
        S4[Server 4]
        S5[Server 5]
    end

    subgraph MetricServers
        M1[Metric Server 1]
        M2[Metric Server 2]
    end

    subgraph OtherServices
        OS1[Service A]
        OS2[Service B]
        OS3[Service C]
        OS4[Service D]
        OS5[Service E]
    end

    subgraph MessageQueue
        MQ[Message Queue_Pub/Sub]
    end

    %% Connections between servers and message queue
    S1 -- "Publish Metric 1, 2" --> MQ
    S2 -- "Publish Metric 3, 4" --> MQ
    S3 -- "Publish Metric 5" --> MQ
    S4 -- "Publish Metric 6" --> MQ
    S5 -- "Publish Metric 7, 8" --> MQ

    %% Metric servers subscribing to the message queue
    MQ -- "Subscribe Metrics" --> M1
    MQ -- "Subscribe Metrics" --> M2

    %% Additional connections from other services to servers
    OS1 -- "Function X" --> S1
    OS2 -- "Function Y" --> S2
    OS3 -- "Function Z" --> S3
    OS4 -- "Function W" --> S4
    OS5 -- "Function V" --> S5

```


Publisher - Subscriber


>추가 적으로 Message 처리 해야 하는 상황이 늘어 난다면?
>결국 복잡해지는것 아닌가요?

1. Metric
2. Log
3. Notification
4. Supply Chain
5. Real-TIme Analytics

```mermaid
graph TD
    subgraph Publishers
        FS1[Front Server 1]
        FS2[Front Server 2]
        DBS1[Database Server 1]
        DBS2[Database Server 2]
        CS1[Chat Server]
        BES1[Backend Server]
    end

    subgraph MetricServers
        M1[Metric Server 1]
        M2[Metric Server 2]
    end

    subgraph RealTimeAnalytics
        A1[Real-time Analytics]
    end

    subgraph Notifications
        N1[Notification System]
    end

    subgraph SupplyChain
        SC1[Supply Chain Management]
    end

    subgraph LogProcessing
        L1[Log Processor 1]
        L2[Log Processor 2]
    end

    subgraph MessageQueues
        MQ1[Metrics Queue]
        MQ2[Logs Queue]
        MQ3[Analytics Queue]
        MQ4[Notifications Queue]
        MQ5[Supply Chain Queue]
    end

    %% Connections between all servers and common message queues
    FS1 -- "Publish Metrics" --> MQ1
    FS2 -- "Publish Metrics" --> MQ1
    DBS1 -- "Publish Metrics" --> MQ1
    DBS2 -- "Publish Metrics" --> MQ1
    CS1 -- "Publish Metrics" --> MQ1
    BES1 -- "Publish Metrics" --> MQ1

    FS1 -- "Publish Logs" --> MQ2
    FS2 -- "Publish Logs" --> MQ2
    DBS1 -- "Publish Logs" --> MQ2
    DBS2 -- "Publish Logs" --> MQ2
    CS1 -- "Publish Logs" --> MQ2
    BES1 -- "Publish Logs" --> MQ2

    FS1 -- "Publish Analytics Data" --> MQ3
    FS2 -- "Publish Analytics Data" --> MQ3
    DBS1 -- "Publish Analytics Data" --> MQ3
    DBS2 -- "Publish Analytics Data" --> MQ3
    CS1 -- "Publish Analytics Data" --> MQ3
    BES1 -- "Publish Analytics Data" --> MQ3

    %% Connections between specific servers and related message queues
    FS1 -- "Publish Notifications" --> MQ4
    FS2 -- "Publish Notifications" --> MQ4
    CS1 -- "Publish Notifications" --> MQ4

    DBS1 -- "Publish Supply Chain Data" --> MQ5
    DBS2 -- "Publish Supply Chain Data" --> MQ5
    BES1 -- "Publish Supply Chain Data" --> MQ5

    %% Metric servers subscribing to the Metrics Queue
    MQ1 -- "Subscribe Metrics" --> M1
    MQ1 -- "Subscribe Metrics" --> M2

    %% Log processors subscribing to the Logs Queue
    MQ2 -- "Subscribe Logs" --> L1
    MQ2 -- "Subscribe Logs" --> L2

    %% New use cases subscribing to their respective message queue
    MQ3 -- "Subscribe Analytics Data" --> A1
    MQ4 -- "Subscribe Notifications" --> N1
    MQ5 -- "Subscribe Supply Chain Data" --> SC1

    %% Additional connections from other services to various publishers
    subgraph OtherServices
        OS1[Service A]
        OS2[Service B]
        OS3[Service C]
        OS4[Service D]
        OS5[Service E]
    end

    OS1 -- "Function X" --> FS1
    OS2 -- "Function Y" --> DBS1
    OS3 -- "Function Z" --> CS1
    OS4 -- "Function W" --> BES1
    OS5 -- "Function V" --> FS2

```

결론은 맞습니다.
비즈니스가 확장됨에 따라서 복잡해지는 위와 같은 상황은 많은 문제를 야기할 수 있습니다.

1. 리소스도 버그도 각기 다른 메시지 큐를 각각 어떻게 관리 할 것 인가?
2. 추가로 메시지 사용을 필요로 하는 상황이 생긴다면 어떻게 처리할 것 인가?
3. 수많은 각기 다른 데이터는 어떻게 관리 해야 하는가?


>[!Note]
>데이터를 일반화 하여 중앙 집권 화 하여 관리하는 방식을 고려해 볼 수 있습니다. 

Kafka Cluster 내에서 Broker들을 관리하는 방식으로 접근 가능합니다.
데이터는 Topic 이라는 방식으로 일반화 되어 Consumer들이 소비하게 됩니다.

![kafka_cluster](https://github.com/christopher3810/TIL/assets/61622657/3a2dfea3-9f4f-4d91-842c-3a4be14247ed)


Kafka는 infomation 의 Producer와 information 의 Consumer 간의 원활한 통합을 제공하는 솔루션 중 하나로, information 의 Producer를 차단하지 않으면서도 Producer가 최종 Consumer 가 누구인지 알 필요 없이 작동합니다.

Cluster 와 내부 Broker 구조를 가리고 보면 하나의 데이터 Back Bone으로도 볼 수 있습니다.


**왜 Kafka는 Zookeeper 라는 별도의 Server 가 필요 한 걸까?** 

Kafka는 기본적으로 분산 처리 시스템.

1. **Cluster membership** : kafka Cluster 내에서 어떤 Broker 가 살고 죽는지를 알 필요가 있습니다.
2. **Electing a controller** : 컨트롤러 선출하기(파티션의 리더/팔로워 관계를 유지하는 역할을 담당)
3. Topic Management :  어떤 토픽이 있는지, 각각 몇 개의 파티션이 있는지, 복제본은 어디에 있는지

#### Topic?

Kafka에서 토픽은 데이터를 카테고리별로 나누어 관리하기 위한 논리적 단위입니다.
Kafka는 데이터를 디스크에 저장할 때 각 토픽과 그 파티션을 디렉토리 구조로 관리합니다.
즉 Topic은 Message, Event를 관리하는 일종의 폴더 입니다.


#### Partition

분산처리 시스템인 kafka에서 생성한 topic이 클러스터 내에서 하나의 machine, 또는 하나의 node에서만 사용된다면??

**topic이 배포할 수 있는 cluster내에 작동하는 node의 개수보다 많아질 수 없기 때문에 Kafka를 스케일링하는 것에 대한 근본적인 한계로 작용할 수 있습니다.**

병렬 처리, 대규모 메시지 처리를 할 수 있도록 **토픽 하나를 여러 개로 나눈 것이 파티션**이라고 합니다.

#### Segment?

Producer에 의해서 브로커로 전송된 메시지는 토픽의 파티션에 저장되며, **각 메시지들은 세그먼트라는 로그 파일의 형태로 브로커의 로컬 디스크에 저장됩니다.**
각 partition 마다 n개의 segment들이 존재합니다.

![스크린샷 2024-06-17 오후 11 36 08](https://github.com/christopher3810/TIL/assets/61622657/7e976104-c3be-4a80-ae6c-ff21117594f3)


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


#### [Kafka Need No Keeper](https://confluent.io/ko-kr/blog/removing-zookeeper-dependency-in-kafka/) Kafka는 더이상 Zookeeper를 사용하지 않습니다.
---

Kafka 3.7 이 Zookeeper를 사용하는 마지막 버전.
Kafka 4.0 부터 [Kraft](https://developer.confluent.io/learn/kraft/) 를 활용하기 시작.

[그럼에도 왜 Kafka 는 동물원을 탈출 했을까?](https://www.confluent.io/blog/why-replace-zookeeper-with-kafka-raft-the-log-of-all-logs/)
