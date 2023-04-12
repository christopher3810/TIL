
Java Thread Synchronization는 여러 스레드가 공유 데이터를 안전하게 접근하고 변경할 수 있도록 보장하는 과정.
`wait()`, `notify()`, 그리고 `notifyAll()` 메소드는 동기화를 위한 핵심 도구로 사용되며, 이러한 메소드를 이용하면 멀티스레드 환경에서 공유 리소스에 대한 동기화된 액세스를 구현할 수 있음.

## Producer-Consumer 예시

간단한 Producer-Consumer 관계에서 생길 수 있는 문제.  
Producer는 데이터를 생성하고 공유 큐에 넣는 역할을 하며, Consumer는 큐에서 데이터를 가져와 소비하는 역할을 하게됨.
이 경우 `wait()`와 `notify()` 메소드를 사용하여 Producer와 Consumer 스레드 간의 동기화를 구현할 수 있음.

```java

class SharedQueue {
    private Queue<Integer> queue = new LinkedList<>();
    private int capacity;

    public SharedQueue(int capacity) {
        this.capacity = capacity;
    }

    public synchronized void produce(int value) throws InterruptedException {
        while (queue.size() == capacity) {
            wait();
        }
        queue.add(value);
        System.out.println("Producer produced: " + value);
        notify();
    }

    public synchronized int consume() throws InterruptedException {
        while (queue.isEmpty()) {
            wait();
        }
        int value = queue.poll();
        System.out.println("Consumer consumed: " + value);
        notify();
        return value;
    }
}

```


위의 예제에서는 `while` 문을 사용하여 큐가 가득 찼거나 비어 있는지를 확인. 

큐가 가득 찼을 때 생산자는 `wait()`를 호출하여 현재 스레드를 일시 중지하고 소비자에게 큐에서 요소를 제거할 시간을 줌. 

큐가 비어 있을 때 소비자는 `wait()`를 호출하여 생산자가 새 요소를 추가할 때까지 기다림.


### if 문 사용시?

이 예제를 `if` 문을 사용하여 조건을 확인하는 방식으로 수정한다면 다음과 같이 변경될 수 있음.

```java
class SharedQueue {
    private Queue<Integer> queue = new LinkedList<>();
    private int capacity;

    public SharedQueue(int capacity) {
        this.capacity = capacity;
    }

    public synchronized void produce(int value) throws InterruptedException {
        if (queue.size() == capacity) {
            wait();
        }
        queue.add(value);
        System.out.println("Producer produced: " + value);
        notify();
    }

    public synchronized int consume() throws InterruptedException {
        if (queue.isEmpty()) {
            wait();
        }
        int value = queue.poll();
        System.out.println("Consumer consumed: " + value);
        notify();
        return value;
    }
}

```

이 수정된 예제에서는 `while` 문을 사용하지 않고 `if` 문을 사용하여 조건을 확인. 

큐가 가득 찼거나 비어 있을 경우, `wait()` 메소드를 호출하여 현재 스레드를 대기 상태로 전환. 

그런 다음, `notify()` 메소드를 사용하여 대기 중인 다른 스레드를 깨움.

그러나 이 방식은 "spurious wakeup"이라고 불리는 문제에 대처하지 못할 수 있음. 

이는 스레드가 기다리고 있는 동안 운영 체제 또는 가상 머신의 특성으로 인해 대기 중인 스레드가 예기치 않게 깨어날 수 있는 현상. 

이러한 경우, 스레드가 잘못된 상태에서 작동하게 되어 예기치 않은 결과를 초래할 수 있음.

waits 은 loop 내부에 있는게 좋음.

### 참조
[java - Why should wait() always be called inside a loop - Stack Overflow](https://stackoverflow.com/questions/1038007/why-should-wait-always-be-called-inside-a-loop)
