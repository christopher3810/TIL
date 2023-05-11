
### @Where Annotation?

`@Where` 어노테이션은 엔티티를 조회할 때 특정 조건에 따라 필터링하고 싶은 경우에 사용됨. 

이 어노테이션을 사용하면 SQL 쿼리에 `WHERE` 절을 추가하여 특정 조건에 맞는 데이터만 조회할 수 있음.

예를 들어, `User` 엔티티에서 활성화된 사용자만 조회하고자 할 때 `@Where` 어노테이션을 사용할 수 있음.

```java
@Entity
@Where(clause = "is_active = true")
public class User {
    @Id
    private Long id;

    private String name;

    @Column(name = "is_active")
    private boolean isActive;

    // 생성자, getter, setter 생략
}

```

이제 `User` 엔티티를 조회할 때마다 `is_active = true` 조건이 자동으로 적용되어 활성화된 사용자만 조회됨.

필요한 데이터만 조회하기 때문에 불필요한 데이터를 가져오지 않음. 

데이터베이스 수준에서 필터링이 이루어지기 때문에 애플리케이션에서 추가적인 필터링 작업이 필요하지 않음.

주의 사항은 당연히 이 어노테이션은 엔티티에 적용되어 모든 조회에서 적용되기 때문에 특정 경우에만 필터링을 적용하고 싶다면 별도의 쿼리를 작성해야 함. 

따라서, 일부 상황에서만 필터링이 필요한 경우에는 `@Where` 어노테이션 대신 다른 방법을 사용해야 할 수도 있음.