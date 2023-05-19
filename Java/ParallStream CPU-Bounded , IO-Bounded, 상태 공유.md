병렬 스트림은 제공된 작업을 여러 개로 나누고 이를 다른 스레드에서 실행함으로써 여러 프로세서의 장점을 취함. 

그러나 항상 빠르고 효율적이지 않음. 

### 병렬 스트림이란 무엇인가?

병렬 스트림은 Java 스트림의 변형인데, 이는 작업을 작은 조각들로 나누어 다중 코어 프로세서의 장점을 최대한 활용하며 이를 병렬로 실행함으로써 이를 가능하게함.

이것은 Java 7에서 도입된 **Fork/Join 프레임워크**를 활용하는 방식으로 작동. 

스트림을 병렬로 전환하면, 데이터가 여러 청크로 나누어지고 Fork/Join 프레임워크가 이러한 청크를 다른 스레드에 할당.

**CPU-bound 작업**, 즉, I/O 작업보다 상당한 계산이 필요한 작업. 데이터베이스나 파일 시스템에서 읽는 등의 I/O-bound 작업의 경우, 병렬 스트림을 사용하면 성능이 저하될 수 있음.
    
**독립적인 작업**이 있는 경우, 즉 한 작업의 결과가 다른 작업에 의존하지 않는 경우.

작업들이 공유 상태를 가지고 있으면, 이를 병렬화하면 스레드 안전성 문제를 초래할 수 있음

### 예시를 통한 이해


#### Case 1: CPU-Bound 작업과 대량의 데이터

예를 들어, 우리는 1백만개의 무작위 정수 리스트가 있고, 각각의 제곱근을 찾아야 한다고 가정.

이것은 대량의 데이터 세트를 가진 CPU-bound 작업이므로 병렬 스트림에 완벽한 시나리오.

```java
List<Integer> numbers = new Random().ints(1, 100).limit(1_000_000).boxed().collect(Collectors.toList());

// 순차 스트림 사용
long startTime = System.nanoTime();
List<Double> squares1 = numbers.stream()
    .map(Math::sqrt)
    .collect(Collectors.toList());
long elapsedTime = System.nanoTime() - startTime;
System.out.println("순차 스트림 시간: " + elapsedTime);

// 병렬 스트림 사용
startTime = System.nanoTime();
List<Double> squares2 = numbers.parallelStream()
    .map(Math::sqrt)
    .collect(Collectors.toList());
elapsedTime = System.nanoTime() - startTime;
System.out.println("병렬 스트림 시간: " + elapsedTime);

// 결과가 동일한지 체크
System.out.println("결과 동일: " + squares1.equals(squares2));

```

#### Case 2: I/O-Bound 작업

이제 10,000개의 사용자 ID 목록에 대한 외부 서비스로부터 데이터를 가져와야 한다고 가정해 봅시다. 이것은 I/O-bound 작업이며, 다음과 같이 보일 수 있습니다:

```java
List<String> userIds = ... // 10,000개의 사용자 ID 목록이라고 가정합니다.
UserService userService = new UserService();  // 외부 서비스 호출을 담당하는 서비스입니다.

// 순차 스트림 사용
long startTime = System.nanoTime();
List<User> users1 = userIds.stream()
    .map(userService::fetchUser)  // I/O 작업입니다. 
    .collect(Collectors.toList());
long elapsedTime = System.nanoTime() - startTime;
System.out.println("순차 스트림 시간: " + elapsedTime);

// 병렬 스트림 사용
startTime = System.nanoTime();
List<User> users2 = userIds.parallelStream()
    .map(userService::fetchUser)  // I/O 작업입니다. 
    .collect(Collectors.toList());
elapsedTime = System.nanoTime() - startTime;
System.out.println("병렬 스트림 시간: " + elapsedTime);

// 결과가 동일한지 체크
System.out.println("결과 동일: " + users1.equals(users2));

```

이 경우에는, 병렬 스트림을 사용하는 것이 순차 스트림에 비해 별다른 성능 향상을 가져오지 못하며, 심지어 더 느릴 수도 있음. 

이는 외부 서비스 호출이 I/O-bound 작업이기 때문. 

I/O-bound 작업은 대부분의 시간이 네트워크 또는 디스크 I/O에 기다리는 데 사용되므로, 이런 작업을 더 많은 스레드로 분할하더라도 효율성이 크게 개선되지 않음.

#### Case 3: 공유된 상태

마지막으로, 공유된 상태를 가지는 작업에 대해 병렬 스트림을 사용하는 것이 어떤 문제를 일으킬 수 있는지 살펴보겠음.

예를 들어, 리스트의 모든 요소를 합산하는 간단한 작업을 가정.

```java
List<Integer> numbers = new Random().ints(1, 100).limit(1_000_000).boxed().collect(Collectors.toList());

// 순차 스트림 사용
long startTime = System.nanoTime();
int sum1 = numbers.stream().reduce(0, Integer::sum);
long elapsedTime = System.nanoTime() - startTime;
System.out.println("순차 스트림 시간: " + elapsedTime);

// 병렬 스트림 사용 - 잘못된 사용
startTime = System.nanoTime();
int[] sum2 = {0};
numbers.parallelStream().forEach(num -> sum2[0] += num);
elapsedTime = System.nanoTime() - startTime;
System.out.println("병렬 스트림 시간: " + elapsedTime);

// 결과가 동일한지 체크
System.out.println("결과 동일: " + (sum1 == sum2[0]));

```

이 경우에는, 공유 상태 (즉, `sum2` 변수)에 대해 여러 스레드가 동시에 액세스하려고하게 되므로, "race condition" 발생. 

이는 여러 스레드가 공유 자원에 대해 동시에 변경을 시도하게 되어, 그 결과가 예측 불가능해지는 상황. 

예제에서 볼 수 있듯이, 병렬 스트림이 순차 스트림보다 훨씬 빠르지만, 합계 결과는 동일하지 않음. 

이는 병렬 처리 중에 `sum2[0] += num;` 연산이 모든 스레드에 의해 정확하게 수행되지 않기 때문.

이와 같은 상황을 해결하기 위해 Java는 atomic 연산을 제공. 

atomic 연산은 여러 스레드에 의해 동시에 수행되더라도 항상 일관된 결과를 보장하는 연산.

```java
// 병렬 스트림 사용 - 올바른 사용
startTime = System.nanoTime();
AtomicInteger sum3 = new AtomicInteger();
numbers.parallelStream().forEach(num -> sum3.addAndGet(num));
elapsedTime = System.nanoTime() - startTime;
System.out.println("Atomic 병렬 스트림 시간: " + elapsedTime);

// 결과가 동일한지 체크
System.out.println("결과 동일: " + (sum1 == sum3.get()));

```

이번에는 `AtomicInteger`를 사용하여 공유 상태를 보호하고, 결과는 올바르게 나옴. 

그러나, 병렬 스트림의 속도가 순차 스트림보다 빠르지 않음 

이는 atomic 연산이 내부적으로 동기화를 사용하기 때문. 

동기화는 병렬 처리의 효율성을 크게 떨어뜨리며, 이 경우에는 순차 스트림이 더 효율적일 수 있음.

그렇기 때문에 `독립적인 연산일 경우에 병렬 스트림의 효과가 올라감.`

