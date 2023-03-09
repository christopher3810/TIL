***

### spring 3.1

Spring 3.1에는 트랜잭션 지원을 활성화하기 위해 @Configuration 클래스에서 사용할 수 있는 @EnableTransactionManagement 어노테이션이 도입되었음.

```java
@Configuration  
@EnableTransactionManagement  
public class PersistenceJPAConfig{  
  
    @Bean  
    public LocalContainerEntityManagerFactoryBean entityManagerFactory() {  
        //...  
    }  
  
    @Bean  
    public PlatformTransactionManager transactionManager() {  
        JpaTransactionManager transactionManager = new JpaTransactionManager();  
        transactionManager.setEntityManagerFactory(entityManagerFactory().getObject());  
        return transactionManager;  
    }  
}
```

<br>

### spring boot and dependency

그러나 Spring Boot 프로젝트를 사용 중이고 클래스 경로에 spring-data-* 또는 spring-tx dependency가 있는 경우 트랜잭션 관리가 기본적으로 활성화됨.

<br>

### @Transactional

트랜잭션이 구성되었으므로 이제 클래스 또는 메서드 수준에서 @Transactional을 사용하여 빈에 주석을 달 수 있음.

```java
@Service 
@Transactional 
public class TestService { 
	//... 
}
```

annotation은 추가적인 설정도 역시 지원함.

1. 트랜잭션의 전파 유형
2. 트랜잭션의 격리 수준
3. 트랜잭션에 의해 래핑된 연산에 대한 시간 제한
4. readOnly flag - 트랜잭션이 readOnly여야 한다는 persistence provider 에게 hint 제공
5. 트랜잭션에 대한 롤백 규칙
	- 기본적으로 롤백은 런타임에 unchecked exceptions 에 대해서만 발생.
	  checked exception는 트랜잭션의 롤백을 트리거하지 않음.

<br>
그중 롤백과 격리수준에 대해서 알아보면

<br>

### Transaction Rollback Rull


  roolbackFor 및 noRollbackFor annotation Parameter를 사용하여 이 동작을 구성할 수 있음.
  
```java
@Service  
@Transactional  
public class UserService {  
  
    @Autowired  
    private UserRepository userRepository;  
  
    @Transactional(rollbackFor = { SQLException.class })  
    public void updateUser(User user) throws SQLException {  
        userRepository.save(user);  
    }  
  
    @Transactional(noRollbackFor = { NullPointerException.class })  
    public User getUserById(Long id) throws NullPointerException {  
        return userRepository.findById(id)  
            .orElseThrow(() -> new NullPointerException("User not found"));  
    }  
}
```

user entity 와 user repository가 간단하게 있다고 가정.
1. class 수준에서 @Transactional을 사용하여 UserService 클래스의 모든 메서드가 트랜잭션 내에서 실행되도록 지정
2. updateUser() 메서드에는 메서드 실행 중에 SQLException이 발생하는 경우 트랜잭션을 롤백하도록 지정
3. getUserById() 메서드에도 @Transactional 어노테이션이 지정되어 있으며, 
   NullPointerException으로 인해 트랜잭션이 롤백되지 않도록 지정

위와 같이 updateUser() 메서드와 getUserById() 메서드 메서든간 트랜잭션 동작을 다르게 지정할 수 있으며 클래스내 개별 메서드의 트랜잭션 동작을 보다 세밀하게 제어 할 수 있음.

<br>

### Change Isolation Level

Spring Boot JPA 프로젝트에서 기본 격리 수준은 일반적으로 READ_COMMITTED임

```java
@Service  
@Transactional(isolation = Isolation.READ_COMMITTED)  
public class UserServiceIsolation {  
  
    @Autowired  
    private UserRepository userRepository;  
  
    public void updateUser(User user) {  
        userRepository.save(user);  
    }  
  
    @Transactional(isolation = Isolation.SERIALIZABLE)  
    public User getUserById(Long id) {  
        return userRepository.findById(id)  
            .orElseThrow(() -> new EntityNotFoundException("User not found"));  
    }  
}
```

getUserById() 메서드에는 이 메서드가 SERIALIZABLE 격리 수준에서 실행되어야 함을 @Transactional 어노테이션을 활용해서 지정 할 수 있음.

트랜잭션이 격리 수준이 READ_COMMITTED로 설정되면 트랜잭션은 다른 트랜잭션이 커밋한 변경 내용만 볼 수 있음(즉, 볼 수 있음)

Isolation.SERIALIZABLE은 Isolation.READ_COMMITTED에 비해 더 높은 격리 수준.
다른 트랜잭션에 의해 수정되었지만 아직 커밋되지 않은 데이터를 읽지 못하도록 보장.



