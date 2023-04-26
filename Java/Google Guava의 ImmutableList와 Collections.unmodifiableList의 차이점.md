
Java에서 불변 컬렉션을 사용하는 데에는 주로 두 가지 방법을 사용했음. 

하나는 Google Guava 라이브러리의 `ImmutableList`를 사용하는 것이고, 다른 하나는 `java.util.Collections`의 `unmodifiableList()` 메서드를 사용하는 것. 

## 1. 각각의 자료구조를 사용하는 차이점

### ImmutableList

Guava 라이브러리의 일부로 제공되며, 불변 리스트를 생성하고자 할 때 사용.

 `ImmutableList` 객체는 생성 시점부터 불변이며, 원본 리스트로부터 변경되지 않음.
 
`ImmutableList`는 다양한 정적 팩토리 메서드를 제공하여 객체 생성을 용이하게 함.
 (`of()`, `copyOf()` 등)


### Collections.unmodifiableList()

Java 표준 라이브러리의 일부로 제공되며, 기존 리스트를 불변 뷰로 감싸서 반환.

원본 리스트에 대한 참조를 유지하므로, 원본 리스트가 변경되면 반환된 불변 뷰도 영향을 받음.

객체 생성에 `Collections.unmodifiableList()` 메서드를 사용하여 기존 리스트를 불변 뷰로 변환.

## 어떤 상황에서 사용할까?

### ImmutableList

1. 원본 데이터에 대한 참조가 필요하지 않은 경우에 사용.
2. 생성 시점부터 불변을 보장해야 하는 경우에 사용.
3. 외부 라이브러리(Guava)에 대한 의존성 추가를 해도 상관 없는 경우.

### Collections.unmodifiableList()

1. 원본 데이터에 대한 참조가 필요하고, 원본 데이터의 변경에 따라 불변 뷰도 같이 변경되어야 하는 경우에 사용.
2. Java 표준 라이브러리를 사용하고 Guava에 대한 의존성 추가를 따로 하고 싶지 않은 경우.

### 간단 예제

`ImmutableList` 예제

```java
import com.google.common.collect.ImmutableList;
import org.springframework.stereotype.Service;
import javax.transaction.Transactional;
import java.util.List;

@Service
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Transactional
    public ImmutableList<UserDto> findAllUsers() {
        List<User> users = userRepository.findAll();
        ImmutableList<UserDto> userDtos = users.stream()
                .map(user -> new UserDto(user.getId(), user.getName(), user.getEmail()))
                .collect(ImmutableList.toImmutableList());
        return userDtos;
    }
}

```

원본 데이터에 대한 참조가 필요하지 않은 상황이므로, `Collections.unmodifiableList()` 보다는`ImmutableList`를 사용하는 것이 더 적절함.

`Collections.unmodifiableList()` 예제

```java
@Service
public class TeamService {
    private final List<Member> teamMembers = new ArrayList<>();

    public TeamService() {
        teamMembers.add(new Member("Alice", "Developer"));
        teamMembers.add(new Member("Bob", "Designer"));
    }

    public List<Member> getTeamMembers() {
        return Collections.unmodifiableList(teamMembers);
    }

    public void addTeamMember(String name, String role) {
        teamMembers.add(new Member(name, role));
    }

    public void removeTeamMember(String name) {
        teamMembers.removeIf(member -> member.getName().equals(name));
    }
}

```

팀원 목록이 언제든지 변경될 수 있으며. 

이를 위해 원본 데이터의 참조를 유지하면서 변경을 방지함.

반환되는 `List<Member> teamMembers` 에대한 불변성을 유지할 수 있으며 `List<Member> teamMembers` 의 수정을 TeamService에서만 할 수있도록 강제 할 수 있음.

즉 `Collections.unmodifiableList()` 로 반환한 b라는 list에 대한 변경이 불가능 하며 `Collections.unmodifiableList()` 에 할당하는 a라는 list의 변경사항이 생길때마다 반영하기 위해서 해당 자료구조를 사용.
