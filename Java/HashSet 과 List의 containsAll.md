***

### containsAll 성능 차이

`HashSet`과 `List`의 `containsAll` 메서드에 대한 성능 차이는 각 자료구조의 원소 검색 방식 때문.

`HashSet`은 해시 테이블을 사용하여 원소를 저장하고 검색하므로, 평균적으로 O(1)의 시간복잡도가 소요됨.

반면, `List`에서 원소를 검색하는데는 O(N)의 시간복잡도가 소요됨.

`containsAll` 메서드의 경우, 한 자료구조의 원소가 다른 자료구조에 모두 포함되어 있는지 확인해야 함.

`HashSet`에서 `containsAll`을 사용하면 시간복잡도는 `O(min(M, N))`이 됨.

`List`에서는 `O(M * N)`이 됨.

<br>

### O(min(M, N)) vs  O(M * N) ??

`O(min(M, N))`는 두 개의 입력 크기 M과 N 중 작은 값을 사용하여 시간복잡도를 나타냄.

반면, `O(M * N)`은 두 입력 크기의 곱을 사용하여 시간복잡도를 나타냄.

예를 들어, M=1000, N=10이라면 O(min(M, N))은 O(10), O(M * N)은 O(10000)이 됨.

이 경우 O(min(M, N))이 O(M * N)보다 시간복잡도가 낮음.

<br>

### List를 반환하는 경우 HashSet 객체 생성 비용은?

`HashSet` 객체 생성 비용은 원소 검색 과정에서 얻는 성능 이점으로 상쇄될 수 있음.

원소 수가 많아질수록 `HashSet`이 `List`보다 더 빠른 성능을 보일 수 밖에 없음.

### 예제

```java
@Entity
public class Product {
    @Id
    private String id;
    private String name;

    // 생성자, getter, setter 생략
}

public interface ProductRepository extends JpaRepository<Product, String> {
}

```

```java
@Autowired
private ProductRepository productRepository;

public boolean compareProductLists() {
    List<Product> productList1 = productRepository.findAll(); // 제품 목록을 가져옵니다.
    List<Product> productList2 = productRepository.findByName("targetName");
    // 확인할 제품 목록

    List<String> productIds1 = productList1.stream().map(Product::getId).collect(Collectors.toList());
    List<String> productIds2 = productList2.stream().map(Product::getId).collect(Collectors.toList());

    // List 사용 시 O(M*N)
    boolean listContainsAll = productIds1.containsAll(productIds2);

    // HashSet 사용 시 O(min(M, N))
    boolean hashSetContainsAll = new HashSet<>(productIds1).containsAll(productIds2);

    return hashSetContainsAll;
}

```

contains역시 마찬가지이다.

contains 또는 containsall을 활용할때는 Hashset 객체를 생성해서 작성하자.


### 참조

[Performance of contains() in a HashSet vs ArrayList | Baeldung](https://www.baeldung.com/java-hashset-arraylist-contains-performance)
