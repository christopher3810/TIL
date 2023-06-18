
### 1. Attribute Pattern

Attribute Pattern은 문서의 속성을 통해 쿼리를 수행하거나 색인을 만드는 패턴. 

상품 문서가 다양한 속성을 가질 때, 이러한 속성을 쉽게 쿼리하거나 색인하고자 하는 경우에 사용.

예를 들어, 상품에 대한 문서는 아래와 같이 표현될 수 있음.

```mongodb
Product
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a03"),
   productName: "smartphone",
   attributes: [
      { color: "black" },
      { brand: "BrandA" },
      { size: "6 inches" }
   ]
}
```

위의 상품 문서에서 속성은 배열의 형태로 저장되며, 각각의 속성은 color, brand, size 등으로 구성되어 있음. 

이러한 구조를 사용하면, 상품의 속성에 따라 쉽게 쿼리하거나 색인을 만들 수 있음.

### 2. Extended Reference Pattern

Extended Reference Pattern은 한 문서에서 다른 문서를 참조하면서, 필요한 정보를 미리 가져오는 패턴. 

이는 주로 읽기 작업이 빈번하며, 다른 문서를 조회하는 데 부담을 줄이고 싶은 경우에 사용됨.

주문에 대한 Document 예시

```mongodb
Order
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a04"),
   product: "Apple",
   customer: { 
       id: ObjectId("60f71c128e4f0d4b5c2e2a00"), 
       name: "John Doe" 
   }
}

```

위 예제에서는 주문 문서 내에 고객의 `_id` 외에도 고객의 이름을 추가로 저장하고 있음. 

이를 통해 주문을 조회할 때 고객의 이름을 별도로 조회하지 않고도 얻을 수 있음.

### 3. Subset Pattern

Subset Pattern은 전체 데이터 중 일부만 자주 사용되는 경우에 사용. 

이 패턴을 사용하면, 필요한 부분만 가져와 성능을 향상시키고 네트워크 트래픽을 줄일 수 있음.

아래는 사용자가 본 상품 목록을 일부만 저장하는 예제.

```mongodb
User
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a00"),
   name: "John",
   viewedProducts: [ 
       { productId: ObjectId("60f71c128e4f0d4b5c2e2a03") }, 
       /* more products */
       { productId: ObjectId("60f71c128e4f0d4b5c2e2a04") } 
   ]
}
```

위 예제에서는 사용자가 본 상품 중 일부만 `viewedProducts`에 저장. 

이를 통해, 사용자가 본 상품 목록을 조회할 때 성능을 향상시킬 수 있음.

### 4. Computed Pattern

Computed Pattern은 미리 계산된 결과를 저장하고 이를 사용해 읽기 성능을 향상시키는 패턴. 

대량의 데이터에 대한 계산을 요구하는 경우에 효율적.

예를 들어, 사용자가 작성한 리뷰의 평균 점수를 계산하는 경우에 적용할 수 있음.

```mongodb
Review
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a05"),
   userId: ObjectId("60f71c128e4f0d4b5c2e2a00"),
   product: ObjectId("60f71c128e4f0d4b5c2e2a03"),
   rating: 4,
   comment: "Great product!"
}

User
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a00"),
   name: "John",
   avgRating: 4.2
}
```

위 예제에서는 각 리뷰에 평점(rating)이 있고, 사용자 문서에서는 이 사용자의 리뷰에 대한 평균 평점(`avgRating`)을 계산해 저장하고 있음. 

리뷰를 조회하거나 추가할 때 마다 평균 평점을 계산하지 않아도 됨.

### 5. Bucket Pattern

Bucket Pattern은 시계열 데이터를 효과적으로 저장하고 관리하기 위한 패턴입니다. 이 패턴을 사용하면, 각 버킷에 여러 데이터를 묶어 저장할 수 있습니다.

예를 들어, 센서에서 주기적으로 데이터를 수집하는 경우에 적용할 수 있습니다.

```mongodb
SensorData
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a06"),
   sensorId: "sensor1",
   start: ISODate("2023-05-15T10:00:00.000Z"),
   end: ISODate("2023-05-15T10:59:59.999Z"),
   readings: [
      { time: ISODate("2023-05-15T10:00:00.000Z"), value: 0.5 },
      /* more readings */
      { time: ISODate("2023-05-15T10:59:59.999Z"), value: 0.6 }
   ]
}
```

위 예제에서는 한 시간 동안의 센서 데이터를 하나의 버킷(`SensorData`)에 묶어 저장.

이를 통해, 각 시간대별 센서 데이터를 효율적으로 관리할 수 있음.

### 6. Schema Versioning Pattern

Schema Versioning Pattern은 스키마의 변경을 관리하는 패턴. 

MongoDB는 스키마가 유연하기 때문에, 문서의 스키마가 시간이 지남에 따라 변경될 수 있음. 

이 패턴을 사용하면, 스키마의 버전을 명시적으로 관리할 수 있음.

예를 들어, 사용자 스키마가 변경된 경우에 적용할 수 있음.

```mongodb
UserV1
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a00"),
   name: "John",
   email: "john@example.com",
   schemaVersion: 1
}

UserV2
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a07"),
   name: "Jane",
   email: "jane@example.com",
   phone: "123-456-7890",
   schemaVersion: 2
}

```

위 예제에서는 초기 버전의 사용자 스키마(`UserV1`)에서는 이메일만 저장하고 있고, 새로운 버전의 사용자 스키마(`UserV2`)에서는 추가로 전화번호를 저장하고 있음. 

각 문서에는 해당 스키마의 버전(`schemaVersion`)을 명시적으로 저장하고 있음. 

이를 통해, 서로 다른 스키마 버전의 문서를 효과적으로 관리할 수 있음.