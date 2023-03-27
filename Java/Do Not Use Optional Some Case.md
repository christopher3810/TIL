

>Of course, people will do what they want. But we did have a clear intention when adding this feature, and it was **not to be a general purpose Maybe type**, as much as many people would have liked us to do so. **Our intention was to provide a limited mechanism for library method return types where there needed to be a clear way to represent "no result"**, and using null for such was overwhelmingly likely to cause errors.
>
>stackoverflow Brian Goetz
>
>물론 사람들은 자신이 원하는 대로 할 것입니다. 
>하지만 이 기능을 추가할 때 저희는 분명한 의도를 가지고 있었고, 많은 사람이 원하는 것처럼 **범용적인 Maybe 타입을 만들려고 한 것은 아니었습니다**.
>**저희의 의도는 "결과 없음"을 명확하게 표현할 수 있는 방법이 필요한 라이브러리 메서드 반환 유형에 대해 제한된 메커니즘을 제공하는 것**이었고, 이러한 경우 null을 사용하면 오류가 발생할 가능성이 압도적으로 높았습니다.


조금 애매하다 api 공식 문서를 다시 살펴 보자.

>API Note:  
>Optional is primarily intended for use as a method return type where there is a clear need to represent “no result,” and where using null is likely to cause errors. A variable whose type is Optional should never itself be null; it should always point to an Optional instance.
>
>**메서드가 반환할 결과값이 ‘없음’을 명백하게 표현할 필요가 있고, `null`을 반환하면 에러를 유발할 가능성이 높은 상황에서 메서드의 반환 타입으로 `Optional`을 사용하자는 것이 `Optional`을 만든 주된 목적이다.** 
>`Optional` 타입의 변수의 값은 절대 `null`이어서는 안 되며, 항상 `Optional` 인스턴스를 가리켜야 한다.


다시 이어서 staci overflow 의 밑에 글로 이어서 가보자

>For example, you probably should *never use it* for something that *returns an array of results, or a list of results*;  instead return an empty array or list. 
>You should almost never use it as a field of something or a method parameter.
>예를 들어 결과 *배열이나 결과 목록을 반환하는 용도로는 절대로 사용하지 말고* 빈 배열이나 목록을 반환해야 합니다. 
>무언가의 *필드나 메서드 매개변수로 사용하는 일은 거의 없어야 합니다.*


>I think routinely using it as a return value for getters would definitely be over-use.
>일상적으로 게터의 반환값으로 사용하는 것은 분명 남용이라고 생각합니다.

<br>

### DTO Field 에서의 Optional을 사용하는 경우

DTO에서도 null값이 존재할 수 있는 경우가 있기 때문에 Field에 간혹 Optional을 사용하는 경우가 있는데  

```java
public class UserDTO {
    private Long id;
    private String name;
    private Optional<String> email;

    //bad case

```

Optional은 주로 값을 반환하지 않을 수 있는 메서드의 반환 유형으로 사용하기 위한 것이지, DTO나 데이터 구조의 필드로 사용하기 위한 것이 아니다.

```java
public class UserRepository {

    public Optional<User> findById(Long id) {
        // Fetch user from the database, might return null if not found
        User user = ...;
        return Optional.ofNullable(user);
    }
}

```

<br>

### DTO Field에서 Optional 을 사용했을때 발생하는 단점들에 대해서 확인해보자

`직렬화 문제(Serialization issues)`

Optional 클래스는 Java에서 null 값을 다룰 때 사용되는 클래스입니다. 하지만 Optional 클래스는 Serializable 인터페이스를 구현하고 있지 않음.
따라서 Jackson과 같은 많은 직렬화 라이브러리는 Optional 클래스를 제대로 처리하지 못함.
라이브러리를 올바르게 처리하도록 구성하거나 사용자 정의 직렬화/역직렬화기를 작성해야 할 수 있음.

`메모리 사용량 증가`

Optional은 실제 값 주위에 래퍼 객체를 추가하여 메모리 사용량을 증가시킨다.
즉 Optional을 사용하는 것 자체가 기본적으로 드는 비용이 존재한다.
DTO 인스턴스 수가 많은 경우 이 오버헤드가 상당할 수 있음.

`코드 복잡도 증가`

DTO에서 옵션을 사용하면 사용할 때마다 옵션 값을 래핑 해제해야 하므로 코드가 더 장황해질 수 있음.

`over-use`

DTO에서 필드 유형으로 옵션을 사용하면 개발자가 간단한 null 확인으로 충분할 경우에도 모든 곳에서 이 Optional을 사용하도록 강제할 수 있음  \
이는 over-use으로 이어져 코드를 필요 이상으로 복잡하게 만들 수 있음.


### 참조
[Optional (Java SE 9 & JDK 9 ) (oracle.com)](https://docs.oracle.com/javase/9/docs/api/java/util/Optional.html)
[Should Java 8 getters return optional type? - Stack Overflow](https://stackoverflow.com/questions/26327957/should-java-8-getters-return-optional-type/26328555#26328555)
