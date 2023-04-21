***

### Mockito.mock()

Mockito.mock()_ 메서드를 사용하면 클래스나 인터페이스의 모의 객체를 생성할 수 있음.

그런 다음 모의 객체를 사용하여 해당 메서드의 반환값을 stub하고 호출되었는지 확인할 수 있음.

예시

```java
@Test
public void givenCountMethodMocked_WhenCountInvoked_ThenMockedValueReturned() {
    UserRepository localMockRepository = Mockito.mock(UserRepository.class);
    Mockito.when(localMockRepository.count()).thenReturn(111L);

    long userCount = localMockRepository.count();

    Assert.assertEquals(111L, userCount);
    Mockito.verify(localMockRepository).count();
}
```

이 메서드를 사용하기 전에 이 메서드에 다른 작업을 할 필요가 없음. 

이 메서드를 사용하여 모의 클래스 필드와 메서드의 로컬 모의 필드를 생성할 수 있음.

### Mockito's _@Mock_ Annotation

이 어노테이션은 _Mockito.mock()_ 메서드의 shorthand임. 

테스트 클래스에서만 사용해야 한다는 점에 유의하는 것이 중요함.

 _mock()_ method와 달리, 이 어노테이션을 사용하려면 Mockito 어노테이션을 활성화해야 함.

 _MockitoJUnitRunner_를 사용하여 테스트를 실행하거나 _MockitoAnnotations.initMocks()_ 메서드를 명시적으로 호출하여 수행할 수 있음.

MockitoJUnitRunner_를 사용한 예제.

```java
@RunWith(MockitoJUnitRunner.class)
public class MockAnnotationUnitTest {
    
    @Mock
    UserRepository mockRepository;
    
    @Test
    public void givenCountMethodMocked_WhenCountInvoked_ThenMockValueReturned() {
        Mockito.when(mockRepository.count()).thenReturn(123L);

        long userCount = mockRepository.count();

        Assert.assertEquals(123L, userCount);
        Mockito.verify(mockRepository).count();
    }
}
```

코드 가독성을 높이는 것 외에도 **_@Mock_은 실패 메시지에 필드 이름이 표시되므로 실패 시 problem mock을 쉽게 찾을 수 있음**.

```bash
Wanted but not invoked:
mockRepository.count();
-> at org.baeldung.MockAnnotationTest.testMockAnnotation(MockAnnotationTest.java:22)
Actually, there were zero interactions with this mock.

  at org.baeldung.MockAnnotationTest.testMockAnnotation(MockAnnotationTest.java:22)

```

게다가 _@InjectMocks_와 함께 사용하면 설정 코드의 양을 크게 줄일 수 있음.

### Spring Boot's _@MockBean_ Annotation

mock object를 Spring 애플리케이션 컨텍스트에 추가하기 위해 _@MockBean_ 을 사용할 수 있음.

mock object는 애플리케이션 컨텍스트에서 동일한 유형의 기존 빈을 대체함.

동일한 유형의 bean이 정의되어 있지 않으면 새 bean이 추가됨.

이 어노테이션은 외부 서비스와 같은 특정 bean을 mocking해야 하는 통합 테스트에 유용함.

이 어노테이션을 사용하려면 _SpringRunner_ 를 사용하여 테스트를 실행해야 함.

```java
@RunWith(SpringRunner.class)
public class MockBeanAnnotationIntegrationTest {
    
    @MockBean
    UserRepository mockRepository;
    
    @Autowired
    ApplicationContext context;
    
    @Test
    public void givenCountMethodMocked_WhenCountInvoked_ThenMockValueReturned() {
        Mockito.when(mockRepository.count()).thenReturn(123L);

        UserRepository userRepoFromContext = context.getBean(UserRepository.class);
        long userCount = userRepoFromContext.count();

        Assert.assertEquals(123L, userCount);
        Mockito.verify(mockRepository).count();
    }
}
```

필드에 어노테이션을 사용하면 mock 데이터들이 필드에 삽입되고 애플리케이션 컨텍스트에 등록됨.

위의 코드에서 이를 확인할 수 있습니다. 여기서는 주입된 _UserRepository_ 모의 객체를 사용하여 _count_ 메서드를 stub한 다음 애플리케이션 컨텍스트에서 bean을 사용하여 실제로 모의 객체인지 확인했음.

### Injection mock과 @Autowired로 테스트 코드에 의존성 주입하는 방식의 차이점

`@InjectMocks`
Injection mock은 테스트 대상 객체에 모의 객체를 직접 주입하는 방식. 

Mockito의 @InjectMocks 어노테이션을 사용하여 테스트 대상 클래스의 인스턴스를 생성하고, 해당 클래스에 정의된 @Mock 또는 @Spy로 주석이 달린 모의 객체를 자동으로 주입함. 

이 방식은 테스트 코드에서 스프링 컨텍스트를 로드하지 않기 때문에, 테스트 실행 속도가 빠름.

```java
@RunWith(MockitoJUnitRunner.class)
public class UserServiceTest {

    @InjectMocks
    private UserService userService;

    @Mock
    private UserRepository userRepository;

    @Test
    public void testGetUserById() {
        User expectedUser = new User(1L, "John Doe");
        Mockito.when(userRepository.findById(1L)).thenReturn(Optional.of(expectedUser));

        User actualUser = userService.getUserById(1L);

        Assert.assertEquals(expectedUser, actualUser);
        Mockito.verify(userRepository).findById(1L);
    }
}

```
    
`@Autowired`로 의존성 주입

@Autowired 어노테이션을 사용하여 테스트 코드에 의존성을 주입하는 경우, 스프링 컨텍스트를 로드하고, 해당 빈을 찾아 테스트 코드에 주입해줌.

하지만, 스프링 컨텍스트를 로드하는 데 시간이 걸리기 때문에 테스트 실행 속도가 상대적으로 느림.

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class UserServiceIntegrationTest {

    @Autowired
    private UserService userService;

    @MockBean
    private UserRepository userRepository;

    @Test
    public void testGetUserById() {
        User expectedUser = new User(1L, "John Doe");
        Mockito.when(userRepository.findById(1L)).thenReturn(Optional.of(expectedUser));

        User actualUser = userService.getUserById(1L);

        Assert.assertEquals(expectedUser, actualUser);
        Mockito.verify(userRepository).findById(1L);
    }
}

```

결론적으로, Injection mock은 단위 테스트에 더 적합한 방식이며, @Autowired를 사용한 의존성 주입은 통합 테스트나 스프링 환경에서 실행되는 테스트에 적합함.

이 두 가지 방식을 적절히 활용하여 효율적인 테스트 코드를 작성할 수 있음.

### 참조
[Mockito.mock() vs @Mock vs @MockBean | Baeldung](https://www.baeldung.com/java-spring-mockito-mock-mockbean#spring-boots-mockbean-annotation)
