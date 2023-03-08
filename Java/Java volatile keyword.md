***


![volatile_cpu](https://user-images.githubusercontent.com/61622657/223710320-2b336127-54f9-436d-91eb-772af66dfd45.png)


CPU는 초당 많은 명령을 수행할 수 있기 때문에 일일히 RAM 에서 가져오지 않고 
프로세서는 Out of Order Execution, Branch Prediction, Speculative Execution, and, of course, Caching.과 같은 트릭을 사용하고 있습니다.\
바로 이 부분에서 다음과 같은 메모리 계층 구조가 작동합니다.

`volatile` 키워드는 Java 변수를 메인 메모리에 직접 저장하도록 명시하는 것으로, 변수를 읽을 때마다 CPU 캐시가 아닌 메인 메모리에서 값을 읽는 것을 보장합니다. \
또한 변수 값을 쓸 때마다 메인 메모리에도 기록되도록 합니다. 

이러한 특징 때문에 `volatile` 키워드는 스레드 간 공유되는 변수에서 사용됩니다. 

그러나 CPU 캐시 대신 메인 메모리에 접근하는 것은 성능 저하를 일으키기 때문에, 
여러 스레드에서 동시에 변수에 접근하는 멀티스레드 환경에서 변수의 값 변경 사항이 즉시 모든 스레드에서 보이도록 하여 일관성 있는 값 유지가 필요한 경우에만 사용하는 것이 좋습니다.

### 예제 코드

```java
public class beforeStop {  
  
    private static boolean stop = false;  
  
    public static void main(String[] args) throws InterruptedException {  
        new Thread(() -> {  
            while(!stop) {}  
            System.out.println("Thread stopped");  
        }).start();  
  
        Thread.sleep(1000);  
        stop = true;  
    }  
  
}
```

메인 스레드는 stop의 값을 true로 설정하고, 람다에서 생성한 스레드는 stop의 값이 true가 될 때까지 계속 확인합니다.\
하지만 stop 변수가 volatile이 아니기 때문에 스래드는 무한루프에 빠질 수 있습니다.

```java
public class AfterStop {  
  
    private static volatile boolean stop = false;  
  
    public static void main(String[] args) throws InterruptedException {  
        new Thread(() -> {  
            while(!stop) {}  
            System.out.println("Thread stopped");  
        }).start();  
  
        Thread.sleep(1000);  
        stop = true;  
    }  
  
}
```

이제 stop 변수는 모든 스레드에 표시되고 생성된 thread는 while을 break 할 수 있습니다.



### 참조
https://www.baeldung.com/java-volatile \
https://www.javatpoint.com/volatile-keyword-in-java \
https://www.geeksforgeeks.org/volatile-keyword-in-java/