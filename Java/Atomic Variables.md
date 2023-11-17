
```java
public class Counter {
    int counter; 
 
    public void increment() {
        counter++;
    }
}
```

단일 스레드 환경에서는 문제가 없지만 둘 이상의 스레드가 쓰기를 할 수 있으면 일관성 없는 결과를 얻기 시작함.

```java
counter ++;
```

>[!NOTE]
>위 연산은 값을 얻고 -> 증가 시키고 -> 업데이트된 값을 다시 쓰는 세가지 연산의 조합임. \
>두 쓰레드가 동시에 값을 얻고 업데이트하려고 하면, 업데이트가 손실될 수 있음.

관리 하는 방법 중 하나는 synchronized 키워드를 사용하는 것.

```java
public class SafeCounterWithLock {
    private int counter;
 
    public synchronized void increment() {
        counter++;
    }
}
```


락을 사용하면 해결은 되나 나머지 스레드는 차단되거나 일시 중단됨.

스레드를 일시 중단하고 다시 시작하는 과정은 매우 비용이 많이 들며 시스템의 전반적인 효율성에 영향을 미침.

compare-and-swap (CAS) 연산을 통해서 보장 할 수있음.

전형적인 CAS 연산은 세 개의 피연산자를 사용.

1. 작업할 메모리 위치 (M)
2. 변수의 기존 예상 값 (A)
3. 설정해야 할 새로운 값 (B)


>[!NOTE]
>CAS 연산은 M의 값을 B로 원자적으로 업데이트하지만, M의 기존 값이 A와 일치하는 경우에만 수행, 그렇지 않으면 아무런 조치도 취하지 않음.

