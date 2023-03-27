
복잡한 Model 객체로 작업할 때는 데이터와 데이터를 활용하는 로직을 명확하게 분리하는 것이 중요함.

Model간 결합 또는 분리를 할때 래퍼 클래스를 사용하는 것이 도움이 됨.

Model 객체를 캡슐화하고 해당 관계와 데이터를 관리하기 위한 인터페이스를 제공 할 수 있음. 

래퍼 클래스 사용의 장점

`캡슐화` \
래퍼 클래스를 사용하면 서로 다른 Model 객체 간의 관계를 캡슐화할 수 있으므로 이러한 관계를 더 쉽게 관리하고 이해할 수 있음.

`유지보수성 향상` \ 
래퍼 클래스를 사용하면 Model 객체가 데이터 보유라는 기본 책임에 집중할 수 있음. \
이렇게 하면 시스템의 다른 부분에 영향을 주지 않고 Model을 업데이트하거나 수정하기가 쉬워져 코드베이스의 유지 관리가 더욱 쉬워짐.

`유연성` \
래퍼 클래스는 Model 관계와 로직을 유연하게 관리할 수 있는 방법을 제공함. \
기본 Model 개체를 변경하지 않고도 관계와 로직을 쉽게 추가, 제거 또는 수정할 수 있음.

`테스트 가능성 향상` \
Model 에서 로직과 관계를 분리하면 데이터 Model과 로직을 모두 테스트하기가 더 쉬워짐.

전자 상거래 시스템 예시

entity
```java
@Entity
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    @ManyToOne(fetch = FetchType.LAZY)
    private Order order;

    // Getters and setters
}

@Entity
public class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;

    // Getters and setters
}

@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY)
    private Customer customer;
    
    @OneToMany(mappedBy = "order", fetch = FetchType.LAZY, cascade = CascadeType.ALL)
    private List<Product> products;
    
    private double total;

    // Getters and setters
}

```

dto

```java
public class ProductDTO {
    private Long id;
    private String name;
    private double price;
    // Getters and setters
}

public class CustomerDTO {
    private Long id;
    private String name;
    private String email;
    // Getters and setters
}

public class OrderDTO {
    private Long id;
    private CustomerDTO customer;
    private List<ProductDTO> products;
    private double total;
    // Getters and setters
}

```

wrapper

```java
public class OrderWrapper {
    private Order order;
    private OrderDTO orderDTO;

    public OrderWrapper(Order order) {
        this.order = order;
        this.orderDTO = mapToDTO(order);
    }

    private OrderDTO mapToDTO(Order order) {
        // Implement the mapping logic from Order entity to OrderDTO
    }

    public double calculateTotal() {
        return orderDTO.getProducts().stream()
            .mapToDouble(ProductDTO::getPrice)
            .sum();
    }

    public boolean isCustomerPreferred() {
        return orderDTO.getCustomer().getEmail().endsWith("@preferred.com");
    }

    public List<ProductDTO> getDiscountedProducts() {
        if (isCustomerPreferred()) {
            return orderDTO.getProducts().stream()
                .filter(p -> p.getPrice() > 100)
                .collect(Collectors.toList());
        }
        return Collections.emptyList();
    }

    public OrderDTO getOrderDTO() {
        return orderDTO;
    }

    // Other methods for managing the order
}

```

 OrderWrapper 클래스는 Order JPA 엔티티, OrderDTO, CustomerDTO 및 ProductDTO 간의 관계와 데이터 로직을 처리함. \
 래퍼 클래스는 주문 총액을 계산하고, 고객이 선호 고객인지 확인하고, 선호 고객에 대해 할인된 제품을 필터링하는 로직을 포함함. \
 기본 데이터 모델인 JPA 엔티티와 DTO는 모두 데이터를 어떻게 쓰겠다 에만 초점을 맞추고 있으며 비즈니스 로직을 포함하지 않게됨.
