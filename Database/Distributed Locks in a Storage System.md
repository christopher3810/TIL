
독립 실행형 환경에서 공유 리소스가 여러 스레드 또는 프로세스가 동시에 공유 리소스를 읽고 쓰면서 발생하는 데이터 손상을 방지하기 위한 상호 제외 기능을 제공할 수 없는 경우, 시스템에는 타사에서 제공하는 상호 제외 기능이 필요.

상호 제외 기능을 제공하는 것은 커널 또는 클래스 라이브러리

![distributedLock](https://github.com/christopher3810/Distributed-Atomic-Transactions-in-Multi-threaded-Environment/assets/61622657/c828a57f-f457-415f-bd63-ae23231ced77)

위를 보면 프로세스가 커널 또는 클래스 라이브러리에서 exclusive lock 을 획득하는 것을 보여줌.

프로세스는 공유 리소스에 exclusive 하게 access가 가능해짐.

>[!NOTE]
>분산 환경내에서는 lock 을 제공하는 분산 서비스가 필요하면 lock을 획득할 경우에는 공유 리소스에 독점적으로 엑세스 가능. \
>이러한 종류의 서비스를 통칭해서 distributed lock service라고 말하고 lock 을 distributed locks 라고 말함.


>[!IMPORTANT]
>distributed locks 은 결국 동시성을 제어할 수 있어야 하고 exclusive한 state를 제공할 수 있어야 함. \
>Lock =  Resource + Concurrency Contro + Ownership Display \
>일반적인 Lock 예제.
>1. Spinlock = BOOL + CAS(Compare And Swap) (Optimistic Lock)
>2. Mutex = BOOL + CAS(Compare And Swap) + notification (Pessimistic Lock)


Spinlock 과 Mutex 모두 BOOL Resource 

Atomic CAS(Compare And Swap) 의 경우 Cuurent Value 가 0 일 경우 Value 에 1을 설정합니다.

