
SLF4J(Simple Logging Facade for Java) 는 다양한 로깅 프레임워크를 사용할 수 있는 간단한 파사드 또는 추상화를 제공.

## 로그 메시지 생성 기존 방식과의 차이점

자바에서 일반적으로 사용되는 로그 메시지 생성 방식은 문자열 연산을 사용하는 것. 

```java
logger.info("User " + user.getName() + " logged in at " + currentTime);
```

위 코드는 메시지를 생성하기 위해 여러 개의 String 객체를 만들어냄. 

`문자열은 불변 객체`이므로,  `+ 연산자` 를 사용하여 문자열을 `결합할 때마다 새로운 String 객체가 생성`되게 됨. 

이는 불필요하게 CPU 와 메모리를 사용하게 됨.

## SLF4J의 `{}` Placeholder 사용의 이점과 예제

### 성능 최적화

로그 메시지를 생성할 때 `{} placeholder`를 사용하면, 문자열 연산은 실제로 로그 레벨이 로그 메시지를 기록하기에 충분히 높을 때만 발생. 

이는 문자열 연산, 특히 로그 메시지가 복잡하거나 많은 정보를 포함해야 할 때 불필요한 문자열 연산을 줄이는 것 과 메모리적 이점을 가져갈 수 있음.

아래는 3가지 실제 케이스를 비교한 예제

1. **기본 문자열 연산 사용**

    ```java
	String userName = "user1"; 
	String action = "logged in"; 
	String time = "10:00";  
	// String 연산을 사용한 로그 메시지 생성 
	logger.info("User " + userName + " " + action + " at " + time);
	```
    
    위 코드에서는 각 문자열` 연산마다 새로운 String 객체가 생성`되므로, `총 6개의 String 객체가 생성`됨.
    
2. **SLF4J의 `{} Placeholder` 사용**
    ```java
	String userName = "user1"; 
	String action = "logged in"; 
	String time = "10:00";  
	// SLF4J의 {} placeholder를 사용한 로그 메시지 생성 
	logger.info("User {} {} at {}", userName, action, time);`
	``` 
    
    이 경우, 실제 `로그 메시지가 필요한 시점에만 하나의 String 객체가 생성`됩니다.
    

### **특정 상황에서 로그 생성**

Spring Boot에서는 `application.properties` 또는 `application.yml`과 같은 설정 파일에서 로깅 레벨을 지정할 수 있음. 

이렇게 설정하면 로그 출력 시 설정된 레벨 이상의 로그만 출력하게 됨.

예를 들어, 설정 파일에서 로그 레벨을 `ERROR`로 지정했다고 가정.

그렇게 되면 `ERROR` 레벨의 로그 메시지만 출력하게 되며, `DEBUG` 또는 `INFO` 레벨의 로그 메시지는 무시하게 됨.

하지만 이렇게 설정 파일에서 로그 레벨을 지정하는 것은 로그 메시지를 출력하는 것만을 제어할 뿐, `실제로 로그 메시지를 생성하는 비용을 줄이지는 못함`. 

왜냐하면, `로그 메시지는 실제로 로그 메소드가 호출되는 시점에서 생성되는데, 이 시점은 설정 파일에서 지정한 로그 레벨을 체크하는 시점보다 앞섬`.

```java
logger.debug("Processing trade with id: " + tradeId + " and amount: " + computeAmount());
```

여기에서 `computeAmount()`는 실제 거래 금액을 계산하는 복잡한 연산을 수행하는 메소드라고 가정.

이 코드는 `DEBUG` 레벨로 로그를 출력하는 코드이지만, 실제로 로그 메시지를 생성하는 시점은 `logger.debug()` 메소드가 호출되는 시점이며, 이 때 `computeAmount()` 메소드가 호출.

그런데 만약 설정 파일에서 로그 레벨을 `ERROR`로 설정했다면, 이 `DEBUG` 레벨의 로그 메시지는 출력되지 않을 것. 

그러나 `computeAmount()` 메소드는 여전히 호출되고, 그 결과는 로그 메시지를 생성하는 데 사용되지만 실제로 출력되지는 않음. 

위와 같이 설정 파일에서 로그 레벨을 지정하는 것만으로는 로그 메시지 생성 비용을 줄일 수 없음.

따라서 아래와 같이 프로그램적으로 생성을 억제 할 수 있음.

```java
if (logger.isDebugEnabled()) {
    logger.debug("Processing trade with id: " + tradeId + " and amount: " + computeAmount());
}
```

하지만, 대부분의 일반적인 로그 메시지 생성은 매우 빠르게 이루어지므로,  `if`문을 사용하는 것이 오히려 성능을 저하시킬 수 있음. 


>그러나 특수한 경우 
>복잡한 문자열 조작, 네트워크 호출, 또는 데이터베이스 조회 등의 비용이 포함 
>그 비용을 줄이기 위해 `if 문을 사용하여 레벨을 확인하는 것이 합리적일 수 있으며 위와 같은 패턴을 알 고 있을 필요는 있음`.
