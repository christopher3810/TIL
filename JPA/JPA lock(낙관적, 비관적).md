***

### 동시성 문제상황 예제 코드

Counter Entity

```java
@Entity
public class Counter {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private int value;

    @Version
    private int version;
}

//db에 value가 10이 들어있다고 가정.

```

두개의 트랜잭션 T1 과 T2

```java
// T1
Counter counterT1 = counterRepository.findById(1L).orElseThrow();
counterT1.setValue(counterT1.getValue() + 30);
counterRepository.save(counterT1);

// T2
Counter counterT2 = counterRepository.findById(1L).orElseThrow();
counterT2.setValue(counterT2.getValue() + 20);
counterRepository.save(counterT2);

```

유저 1과 유저2가 T1과 T2의 서로다른 수정 화면에 진입 했고 Value값을 서로 다르게 입력해서 저장하려고 한다고 가정하자. \
서로 다른 트랜잭션에서 findyById를 활용한 Select 지점은 같으나 Update 시점에서 `T2`기 `T1` 보다 나중에 실행되는 경우 DB에 남아 있는 값은 30 (10 + 20)이 되며 T1에서 넣은 변경 내용은 사라지게 된다.

<br>

### 낙관적 락 (Optimistic Locking)

낙관적 락은 충돌이 발생하지 않을 것이라고 가정하고, 충돌이 발생할 경우 충돌을 해결하는 방식입니다. \
JPA에서는 `@Version` 어노테이션을 사용하여 낙관적 락을 구현할 수 있습니다. 

`Counter` 엔티티가 변경될 때 마다 `version` 이 자동으로 하나씩 증가한다. \
그리고 엔티티를 수정할 때, 엔티티를 조회한 시점의 버전과 수정한 시점의 버전이 일치하지 않으면 예외가 발생한다.

즉 최초로 수정한 커밋만 인정하는 정책을 취할 수 있다.

```java
// T1
Counter counterT1 = counterRepository.findById(1L).orElseThrow();
counterT1.setValue(counterT1.getValue() + 30);
counterRepository.save(counterT1);

// T2
Counter counterT2 = counterRepository.findById(1L).orElseThrow();
counterT2.setValue(counterT2.getValue() + 20);
try {
    counterRepository.save(counterT2);
} catch (OptimisticLockingFailureException e) {
    // 충돌 발생 시, 다시 시도 또는 에러 처리
}
```

<br>

### 비관적 락 (Pessimistic Locking)

비관적 락은 충돌이 발생할 것으로 예상하고, 먼저 충돌을 방지하는 방식입니다. \
JPA에서는 `LockModeType`를 사용하여 비관적 락을 구현할 수 있습니다.

```java
// CounterRepository.java
@Lock(LockModeType.PESSIMISTIC_WRITE)
@Query("SELECT c FROM Counter c WHERE c.id = :id")
Optional<Counter> findByIdWithPessimisticLock(@Param("id") Long id);

// T1
Counter counterT1 = counterRepository.findByIdWithPessimisticLock(1L).orElseThrow();
counterT1.setValue(counterT1.getValue() + 30);
counterRepository.save(counterT1);

// T2
Counter counterT2 = counterRepository.findByIdWithPessimisticLock(1L).orElseThrow();
counterT2.setValue(counterT2.getValue() + 20);
counterRepository.save(counterT2);

```

`PESSIMISTIC_READ` \
공유 락(Shared Lock)이라고도 불리며, 여러 트랜잭션이 동시에 같은 데이터를 읽을 수 있도록 허용합니다. \
그러나 데이터를 수정하려는 트랜잭션은 락이 해제될 때까지 기다려야 합니다.  \
이 락은 읽기 작업을 보호하고 동시성 문제를 방지하기 위한 것입니다.

`PESSIMISTIC_WRITE` \
Exclusive Lock 이라고도 불리며, 데이터를 읽거나 수정하려는 트랜잭션은 락이 해제될 때까지 기다려야 합니다. \
이 락은 데이터를 수정하는 작업을 보호하고 동시성 문제를 방지하기 위한 것입니다.

`PESSIMISTIC_FORCE_INCREMENT`
PESSIMISTIC_WRITE와 유사하게 작동하지만 `@Version` 어노테이션이 지정된 Enntity와 같이 사용되기 위해 만들어 졌습니다. \
엔티티의 버전 필드 값을 증가시키는 동시에 Exclusive Lock을 적용합니다. \
엔티티를 수정하려는 트랜잭션은 락이 해제될 때까지 기다려야 하며, 락이 해제되면 버전 값이 자동으로 증가합니다.

