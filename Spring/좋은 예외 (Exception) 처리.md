
> 해당글은 https://jojoldu.tistory.com/734 에서 작성해주신 좋은 예외 처리 글을 예제 코드를 최대한
> Spring Code로 변환해서 학습한 것을 기록 하는 것을 목표로 만듬.

추후 Exception 처리에 관련된 팁들을 추가적으로 얻게 될 경우에 해당 글을 확장하거나 테마별로 분할할 예정임.

## 복구 가능한 오류와 불가능한 오류 구분하기
---

가장 먼저 할 것은 **복구 가능한 오류와 복구 불가능한 오류를 구분**하는 것.  
모든 예외에 대해서 동일한 방식을 적용할 수는 없음.  
어떤 예외는 상시로 발생해서 무시할 수 있으며, 어떤 예외는 무시하면 절대 안되는 경우도 있음.

이들을 구분없이 다룬다면 사용자는 불편하고, 개발자는 상시로 발생하는 알람으로 점점 더 시스템의 문제에 등한시 하게 된다.

그래서 이들을 구분해서 **예외가 발생 했을 때 어떻게 처리할지** 결정해야 함.

### 복구 가능한 오류
---

복구 가능한 오류는 일반적으로 **시스템 외적인 요소로 발생하는 치명적이지 않은 오류**.  
이러한 오류는 프로그램이 감지하고 적절한 조치를 취한 후 정상적으로 계속 실행될 수 있는 오류.

1. 사용자의 오입력
2. 네트워크 오류
3. 서비스적으로 중요하지 않은 작업의 오류

복구 가능한 오류는 **상시로 발생할 수 있다고 가정하고**, 사용자 (호출자) 에게 가능한 문제 원인을 인지할 수 있게 해야 함.

너무 잦은 복구 가능한 오류의 발생은 복구 불가능한 오류 (개발자의 잘못구현된 코드)일 수 있으니 이를 위해 로그 레벨을 `warn` 으로 두고 임계치를 넘으면 모니터링 알람을 보내도록 구성한다.  
(단, 여기서의 알람 임계치는 복구 불가능한 오류 보다는 기준이 높아야 한다)

### 복구 불가능한 오류
---

복구 불가능한 오류는 **별도의 조치 없이 시스템이 자동으로 복구할 수 있는 방법이 없는 경우**이다.  
대부분의 경우, 이 오류의 원인을 해결하기 전에 프로그램이 계속 실행될 수 없다.

1. 메모리 부족 (Out of Memory)
    시스템이 필요한 메모리를 할당 받지 못할 때 발생한다.
2. 스택오버플로우 (StackOverflow)
    재귀 함수가 너무 깊게 호출되어 스택 메모리가 고갈될 때 발생한다.
3. 시스템 레벨의 오류
   하드웨어 문제나 운영 체제의 중대한 버그로 인해 발생한다.
4. 개발자의 잘못 구현된 코드
5. 데이터베이스 스키마 불일치
6. 쓰레드 데드락

복구 불가능한 오류는 자주 발생하는 오류가 아니기 때문에 **빠르게 개발자에게 문제 원인을 알려야한다**.

이를 위해 로그 레벨을 `error`로 두고, 로그에서는 에러 트레이스를 남긴 뒤, 임계치를 초과하면 개발자에게 알람을 보내도록 구성해야 한다.

### null, -1, 빈 문자열등 특 수값을 예외로 사용하지 말기.
---

```java
return -1;
```

위와 같이 의미를 알 수 없는 null, -1 등의 값들을 사용하면 안됨.

### 문자열을 throw 하지 않기
---

```java
throw '~~~'
```

Exception 객체를 생성 한 뒤 해당 객체에 문제가 되는 정보를 담아서 throw해야 함.

### 추적 가능한 예외


실제 운영 환경에서 문제가 발생했을 때, 예외의 원인을 빠르게 찾아내는 것은 중요하다. 

그러나 호출 스택만으로는 코드의 의도나 실패 원인을 완벽히 파악하기 어렵다.

그렇기 때문에 예외 메세지에는 다음의 정보들이 포함되어야 한다.

- 실패한 작업의 이름과 실패 유형
- 실패할 때 사용한 값
- 오류 메시지

이러한 정보는 운영 환경에서 예외가 발생했을 때 문제를 정확하고 빠르게 파악하는데 도움을 준다.

부적절한 예제
```java
@PostMapping("/users")
public ResponseEntity<?> createUser(@RequestBody User user) {
    if (user.getName().isEmpty() || user.getEmail().isEmpty()) {
        throw new IllegalArgumentException("잘못된 입력입니다.");
    }
    // ... 비즈니스 로직
    return ResponseEntity.ok().build();
}
```

어떤 입력이 잘못되었는지 구체적으로 알 수 없음.

추적 가능한 예외 처리

```java
@PostMapping("/users")
public ResponseEntity<?> createUser(@RequestBody User user) {
    if (user.getName().isEmpty()) {
        throw new IllegalArgumentException(String.format("사용자 이름이 비어 있습니다. 입력값: %s", user.getName()));
    }
    if (user.getEmail().isEmpty()) {
        throw new IllegalArgumentException(String.format("사용자 이메일이 비어 있습니다. 입력값: %s", user.getEmail()));
    }
    // ... 비즈니스 로직
    return ResponseEntity.ok().build();
}

```

실패 원인과 실패한 값이 예외 메시지에 포함되므로, 문제가 발생했을 때 쉽게 원인을 파악할 수 있다.

```java
@PostMapping("/users")
public ResponseEntity<?> createUser(@RequestBody User user) {
	try {
		if (user.getName().isEmpty()) {
			throw new IllegalArgumentException(String.format("사용자 이름이 비어 있습니다. 입력값: %s", user.getName()));
		}
		if (user.getEmail().isEmpty()) {
			throw new IllegalArgumentException(String.format("사용자 이메일이 비어 있습니다. 입력값: %s", user.getEmail()));
		}
		// ... 비즈니스 로직
		return ResponseEntity.ok().build();
	} catch (IllegalArgumentException e) {
		log.warn(e.getMessage(), e);
		return ResponseEntity.badRequest().body("잘못된 입력입니다.");
	}
}
```

예외 메시지를 그대로 사용자에게 노출하는 것은 보안과 사용자 경험에 문제가 될 수 있다. 

따라서 로그와 사용자에게 보여주는 메시지를 분리하는 것이 바람직하다.

###  Layer에 맞는 예외를 던지자
---
1. 데이터 액세스 계층 (Data Access Layer)
    - DataAccessException, DuplicateKeyException ....
2. 비즈니스 로직 계층 (Business Logic Layer)
    - CustomBusinessException
3. 프레젠테이션 계층 (Presentation Layer)
    - HttpRequestTypeNotSupportException ...

### 예외 계층 구조 만들기
---

가능한 계층 구조를 만들어서 사용하자.

bad

```java
class DuplicatedException extends Error {} 
class UserAlreadyRegisteredException extends Error {}
```

good

```java
class ValidationException extends Error {} 
class DuplicatedException extends ValidationException {} 
class UserAlreadyRegisteredException extends ValidationException {}
```

### 외부 예외 감싸기
---

SDK, 외부 API를 통해 발생하는 예외들은 하나로 묶어서 처리.

```java
@Service
public class PaymentService {

    public void processPayment() {
        try {
            // 외부 결제 라이브러리 호출
            externalPaymentLibrary.call();
        } catch (ExternalPaymentException e) {
            // 외부 예외 내부에서 사용하는 예외로 변환
            throw new PaymentProcessingException("Payment processing failed", e);
        }
    }
}
```

위와 같이 exceptgion 을 한번에 catch 할경우 구체적으로 어디서 문제가 발생했으며 그에 따른 해결방법을 포함시키기 어려움.

instanceof로 전부 구현하기엔 에러의 종류가 변경이 된다면 서비스 코드 전체에 영향을 끼침.

의존성을 분리해야 함.
결제 서비스는 결제 처리만을, 주문 서비스는 주문 처리만을 담당해야 함.

```java
@Service
public class PaymentService {

    public void billing(Payment payment) throws BillingException {
        try {
            // 외부 결제 라이브러리 호출
            payment.externalBilling();
        } catch (PayNetworkException e) {
            log.error("Network issue during payment: ", e);
            // Network 이슈인 경후 해야할 로직 추가  가능..
            throw new BillingException("Network issue during payment", e);
        } catch (EmptyMoneyException e) {
            log.error("Insufficient funds: ", e);
            //EmptyMoney 인경우에 해야 할 로직 추가 가능
            throw new BillingException("Insufficient funds for payment", e);
        } catch (PayServerException e) {
            log.error("Server issue at payment provider: ", e);
            // 서버 오류의 경우 해아할 로직 추가 가능.
            throw new BillingException("Server issue during payment processing", e);
        }
    }
}
```


```java
@Service
public class OrderService {

    @Autowired
    private PaymentService paymentService;

    @Autowired
    private OrderRepository orderRepository;

    @Transactional
    public void placeOrder(Order order, Payment payment) {
        try {
            paymentService.billing(payment);
            // 주문 데이터 저장
            orderRepository.save(order);
        } catch (BillingException e) {
		    // 주문 취소
	        paymentService.cancelPayment(payment);
        }
    }
}

```

하지만 여기서 한가지 더 `BillingException` 은 개발한 서비스의 예외이기 때문에 가능한 가장 먼 곳에서 처리한다 (미들웨어, 글로벌 에러 핸들러 등)

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(BillingException.class)
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    public ResponseEntity<?> handleBillingException(BillingException e) {
        // 이곳에서 로그를 남기거나 필요한 처리를 할 수 있습니다.
        log.error("Billing exception occurred: ", e);
        
        // 클라이언트에게 오류 메시지를 전송하는 등의 처리
        Map<String, String> errorResponse = new HashMap<>();
        errorResponse.put("message", e.getMessage());

        return new ResponseEntity<>(errorResponse, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

```java
@Service
public class OrderService {

    @Autowired
    private PaymentService paymentService;

    @Autowired
    private OrderRepository orderRepository;

    @Transactional
    public void placeOrder(Order order, Payment payment) {
        paymentService.billing(payment);
        orderRepository.save(order);
    }
}

```

위 와 같이 의존성을 분리하면 외부 라이브러리를 다른 것으로 사용하게 되더라도 내부 로직의 변경은 최소화 됨.

### 다시 throw 할 거면 잡지 않기
---

```java
try { 
	// 비즈니스 로직 
} catch (e) {
  throw e 
}
```

로깅 혹은 Layer에 적합한 Exception 변환 등이 필요한 것이 아니라면 try catch로 다시 잡지 않는 것이 좋다.

### 정상적인 흐름에서 Catch 금지 (무분별한 Catch 금지)
---

프로그램의 정상적인 흐름을 제어하기 위해 예외를 사용하지 않음.  
예외는 오직 예외적인 경우에만 사용.

```java
@Service
public class DataService {

    @Autowired
    private DataRepository dataRepository;

    public Data fetchData() {
        Data data = dataRepository.findData();
        if (data == null) {
            throw new NoDataFoundException();
        }
        return data;
    }
}

@RestController
public class DataController {

    @Autowired
    private DataService dataService;

    @GetMapping("/data")
    public ResponseEntity<?> getData() {
        try {
            Data data = dataService.fetchData();
            return ResponseEntity.ok(data);
        } catch (NoDataFoundException e) {
            return ResponseEntity.notFound().build();
        }
    }
}

```

NoDataFoundException() 은 데이터가 없는 일반적인 상황을 나타내기 위해 사용됨.

이는 예외 프로그램의 정상적인 흐름 제어를 위한 수단으로 사용하는 잘못된 접근 방식.

```java
@Service
public class DataService {

    @Autowired
    private DataRepository dataRepository;

    public Optional<Data> fetchData() {
        return Optional.ofNullable(dataRepository.findData());
    }
}

@RestController
public class DataController {

    @Autowired
    private DataService dataService;

    @GetMapping("/data")
    public ResponseEntity<?> getData() {
        return dataService.fetchData()
            .map(ResponseEntity::ok)                 
            // 데이터가 존재하면, OK(200) 응답과 함께 데이터 반환
            .orElseGet(() -> ResponseEntity.notFound().build());  
            // 데이터가 없으면, NOT FOUND(404) 응답 반환
    }
}
```

optional 을 활용하여 값이 있을 때와 없을 때 로직을 구분해서 처리 할 수 있다.

### 가능한 늦게 예외를 처리 한다.
---

Exception을 throw 하자마자 잡지 않는다.  
가능한 가장 최상위 계층에서 처리한다.  
물론 무조건적으로 글로벌 핸들러에서 처리하는 것을 의미하지 않는다.  
해당 예외를 처리해야하는 여러 계층 중 가장 최상위 (가장 멀리 떨어진) 계층에서 처리한다.

```java
@Service
public class CalculationService {

    public double divide(double numerator, double denominator) {
        if (denominator == 0) {
            throw new ArithmeticException("Cannot divide by zero!");
        }
        return numerator / denominator;
    }
}

@RestController
public class CalculationController {

    @Autowired
    private CalculationService calculationService;

    @GetMapping("/divide")
    public ResponseEntity<Double> divide(@RequestParam double numerator, @RequestParam double denominator) {
        try {
            double result = calculationService.divide(numerator, denominator);
            return ResponseEntity.ok(result);
        } catch (ArithmeticException ex) {
            return ResponseEntity.badRequest().body(null);
        }
    }
}

```

위의 예에서는 `divide` 메서드를 호출할 때마다 예외 처리를 해주어야 함.

그렇게 되면, 비즈니스 로직 보다 예외 처리 코드가 더 많아지게 됨.

```java    
@RestController
public class CalculationController {

    @Autowired
    private CalculationService calculationService;

    @GetMapping("/divide")
    public ResponseEntity<Double> divide(@RequestParam double numerator, @RequestParam double denominator) {
        double result = calculationService.divide(numerator, denominator);
        return ResponseEntity.ok(result);
    }
}

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ArithmeticException.class)
    public ResponseEntity<String> handleArithmeticException(ArithmeticException ex) {
        return ResponseEntity.badRequest().body(ex.getMessage());
    }
}

```

글로벌 핸들러를 활용하여 최상위에서 전역적으로 예외를 처리 할 수 있음.

**장점**

1. **가독성**:  예외 처리 코드가 중앙화되어 함수나 서비스의 실제 로직에만 집중할 수 있음.
2. **재사용성** : 예외 처리 로직이 분리되어 있기 때문에 해당 함수나 서비스를 다른 상황에서도 재사용하기가 쉬움.
3. **통일성**:  예외 처리를 한 곳에서 관리하면 예외 처리 방법을 통일시키고, 변경이 필요할 경우에도 한 곳만 수정하면 되기 때문에 유지 보수가 용이.

### 출처
---
https://jojoldu.tistory.com/734

