
>병렬처리 및 스레드 관리시 중요한 요소 이기 때문에 java.util.concurrent 패키지 내의 Executors 클래스에 내부 구현체들과 실제 사용 예제들에 대해서 정리해보도록 하겠습니다. 

---

## Executors Class

Executors 클래스는 병렬 처리 작업을 처리하기 위한 유틸리티 클래스.

다양한 종류의 스레드 풀을 생성할 수 있게 해주는 여러 가지 static method 를 제공.

주요 메서드를 살펴 보고 이를 어떻게 사용하는지에 대한 실 예제를 정리 하겠음.

---

### newFixedThreadPool()

newFixedThreadPool() 메서드는 고정된 크기의 스레드 풀을 생성. 

인자로 스레드 풀의 크기를 지정하게 되는데, 이 크기는 스레드 풀이 동시에 실행할 수 있는 최대 스레드의 수를 의미. 

고정된 크기의 스레드 풀은 `병렬 처리 작업을 수행하면서 동시에 실행되는 스레드의 수를 제한하고 싶을 때 사용`.

```java
ExecutorService executorService = Executors.newFixedThreadPool(5);
IntStream.range(0, 10).forEach(i -> 
    executorService.submit(() -> {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Task " + i + " is completed.");
    })
);

```


위의 코드는 고정 크기 스레드 풀을 이용하여 10개의 작업을 병렬로 처리하는 예제.

각 작업은 1초 동안 sleep하고, 작업이 완료되면 완료 메시지를 출력.

스레드 풀의 크기가 5이므로, 한 번에 최대 5개의 작업이 동시에 처리됨.

---

### newSingleThreadExecutor()

newSingleThreadExecutor() 메서드는 하나의 작업 스레드만을 가지는 스레드 풀을 생성. 

한 번에 하나의 작업만을 처리하고 싶을 때 사용합니다. 이 스레드 풀은 작업을 FIFO(First In First Out) 순서로 처리.

```java
ExecutorService executorService = Executors.newSingleThreadExecutor();
IntStream.range(0, 10).forEach(i -> 
    executorService.submit(() -> {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Task " + i + " is completed.");
    })
);
```

위의 코드는 단일 스레드 실행자를 이용하여 10개의 작업을 순차적으로 처리하는 예제. 

각 작업은 1초 동안 sleep하고, 작업이 완료되면 완료 메시지를 출력.

newSingleThreadExecutor()를 사용하므로, 한 번에 하나의 작업만이 처리.

---

## newCachedThreadPool()

newCachedThreadPool() 메서드는 초기에는 아무런 스레드도 포함하지 않지만, 필요에 따라 새로운 스레드를 생성하거나 이전에 생성된 스레드를 재사용하는 스레드 풀을 생성. 

이 방식은 작업의 수가 동적으로 변할 때 유용하게 사용.

```java
ExecutorService executorService = Executors.newCachedThreadPool();
IntStream.range(0, 10).forEach(i -> 
    executorService.submit(() -> {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Task " + i + " is completed.");
    })
);
```

위의 코드는 캐시된 스레드 풀을 이용하여 10개의 작업을 병렬로 처리하는 예제.

각 작업은 1초 동안 sleep하고, 작업이 완료되면 완료 메시지를 출력.

newCachedThreadPool()를 사용하므로, 모든 작업이 가능한 한 동시에 처리.

---

## newWorkStealingPool()

이 메서드는 자바 8에 추가된 것으로, 작업을 훔쳐가며 병렬 처리를 수행하는 ForkJoinPool을 생성. 

스레드의 수는 기본적으로 사용 가능한 프로세서의 수와 같음.

병렬 스트림 작업에 특히 유용.

```java
ExecutorService executorService = Executors.newWorkStealingPool();
IntStream.range(0, 10).forEach(i ->
    executorService.submit(() -> {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Task " + i + " is completed.");
    })
);
```

위의 코드는 WorkStealingPool을 이용하여 10개의 작업을 병렬로 처리하는 예제.

각 작업은 1초 동안 sleep하고, 작업이 완료되면 완료 메시지를 출력.

newWorkStealingPool()를 사용하므로, 모든 작업이 가능한 한 동시에 처리.

---

## newScheduledThreadPool(int corePoolSize)

이 메서드는 스케줄링 기능이 추가된 스레드 풀을 생성.

이를 사용하면, 작업을 일정 시간 이후에 실행하거나 주기적으로 실행하는 등의 스케줄링 작업을 처리할 수 있음.

```java
ScheduledExecutorService executorService = Executors.newScheduledThreadPool(5);
executorService.schedule(() -> {
    System.out.println("Task is completed.");
}, 1, TimeUnit.SECONDS);

```

위의 코드는 ScheduledThreadPool을 이용하여 1초 후에 작업을 실행하는 예제입니다. 작업이 완료되면 완료 메시지를 출력

---

### unconfigurableExecutorService(ExecutorService executor || ScheduledExecutorService executor)

기존의 ExecutorService를 래핑하여 수정이 불가능하게 만드는데 사용.

`ExecutorService의 수명주기를 제어하거나 풀의 크기를 조절하는 것을 방지`하고 싶을 때 사용

특정 서비스 클래스가 작업을 병렬로 처리해야 하는 상황이 있다고 가정한다면. 

이 때, ExecutorService를 사용하여 병렬로 처리할 수 있음.

하지만, 이 서비스 클래스를 사용하는 다른 개발자가 실수로 ExecutorService를 종료(shutdown)하거나 풀의 크기를 변경하는 경우, 어플리케이션이 예상치 못한 동작을 할 수 있음.

이럴 때 `unconfigurableExecutorService`를 사용하면 ExecutorService를 안전하게 사용

`unconfigurableScheduledExecutorService(ScheduledExecutorService executor)`

의 경우  ScheduledExecutorService를 래핑한것임.

### 예제

사용자의 요청에 대해 독립적으로 처리해야 하는 상황

예를 들어, 사용자가 특정 작업을 요청하고 서버는 해당 작업을 병렬로 처리하고자 할 때 `newFixedThreadPool`를 활용하면 좋음.

이 때, 다른 개발자가 실수로 ExecutorService를 종료하거나 풀의 크기를 변경하는 것을 방지하기 위해 `unconfigurableExecutorService`를 사용.


```java
@Service
public class DataUpdateService {
    private final ScheduledExecutorService scheduledExecutorService;

    public DataUpdateService() {
        this.scheduledExecutorService = Executors.unconfigurableScheduledExecutorService(Executors.newScheduledThreadPool(2));
    }

    @PostConstruct
    public void scheduleDataUpdateTasks() {
        scheduledExecutorService.scheduleAtFixedRate(this::updateData, 0, 10, TimeUnit.SECONDS);
    }

    private void updateData() {
        // get data from external API and update database
    }
}
```
