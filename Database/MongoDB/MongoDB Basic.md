## MongoDB Fundamentals

MongoDB는 고성능, 고가용성 및 쉬운 확장성을 제공하는 NoSQL, 문서 지향 데이터베이스입니다. 

데이터를 배열 및 중첩 문서와 같은 복잡한 데이터 유형을 효율적으로 저장할 수 있는 유연한 JSON과 유사한 형식인 BSON(Binary JSON)으로 저장합니다.

### Database, Collection 및 Document

-   Database
  
  MongoDB 인스턴스는 여러 개의 데이터베이스를 호스트할 수 있으며 각각의 데이터베이스는 컬렉션의 컨테이너로 작용합니다.
   
  데이터베이스는 디스크의 별도 파일에 데이터를 저장하며 각 데이터베이스에는 고유한 이름 공간이 있습니다.
  
-   Collection
  
  MongoDB의 컬렉션은 문서의 그룹이며 관계형 데이터베이스의 테이블과 유사합니다.
  
  컬렉션은 단일 데이터베이스 내에 존재하며 스키마를 강제하지 않으므로 컬렉션 내의 문서는 다른 필드와 구조를 가질 수 있음.
  
-   document 
   |
   document MongoDB에서의 기본 데이터 단위로 관계형 데이터베이스의 행과 유사. 
   
   document는 BSON 형식에 저장된 필드와 값 쌍으로 구성.

### BSON 데이터 형식

BSON은 MongoDB에서 문서를 저장하고 원격 프로시저 호출을 위해 사용하는 이진 직렬화 형식. 

BSON은 적은 용량을 사용하며, 트래버스하기 쉽고, 인코딩 및 디코딩에 효율적.

문자열, 숫자(정수, 부동 소수점, 십진수), 날짜, 이진 데이터, ObjectId 등 다양한 데이터 유형을 지원.

### CRUD 작업


CREATE

**`insertOne()`** 또는 **`insertMany()`** 메서드를 사용하여 컬렉션에 문서를 삽입합니다. 

예를 들어, MongoDB 셸에서:

```bash
`perlCopy code db.users.insertOne({   "name": "John Doe",   "email": "john.doe@example.com" })`
```

Read

**`find()`** 또는 **`findOne()`** 메서드를 사용하여 컬렉션에서 문서를 쿼리합니다. 

**`$eq`**, **`$gt`**, **`$lt`**, **`$regex`**와 같은 쿼리 연산자를 사용하여 특정 기준에 따라 문서를 필터링할 수 있습니다.

```bash
db.users.find({"age": {"$gt": 30}})`
```


Update

Collection의 Document를 Update 하려면 `updateOne()`, `updateMany()` 또는 `replaceOne()` 메서드를 사용합니다.

`$set`, `$inc`, `$push`와 같은 업데이트 연산자를 적용하여 특정 필드를 수정할 수 있습니다. 

```bash
db.users.updateOne({"_id": ObjectId("507f1f77bcf86cd799439011")}, {"$set": {"email": "new.email@example.com"}})
```

Delete
Collection에서 Document를 제거하려면 `deleteOne()` 또는 `deleteMany()` 메서드를 사용합니다.

```bash
db.users.deleteOne({"_id": ObjectId("507f1f77bcf86cd799439011")})
```


### MongoDB의 장단점

장점

스키마가 없음
다양한 구조의 데이터를 저장할 수 있는 유연성을 제공합니다. 

확장성
샤딩을 통해 수평적 확장을 지원합니다. 

높은 성능
인덱싱, 캐싱 및 메모리 내 저장을 지원하여 빠른 쿼리 처리가 가능합니다. 

높은 가용성
replica sets을 지원함.

단점

`트랜잭션 지원의 한계`

MongoDB는 다중 문서 트랜잭션을 지원하지만, 관계형 데이터베이스와 같이 성숙하지 않습니다. 

`대용량 데이터 저장`

BSON 형식으로 인해 다른 데이터베이스에 비해 저장 공간이 더 많이 필요할 수 있습니다. 
특히 대량의 작은 문서를 다룰 때 더 그렇습니다.

`Join`

MongoDB는 관계형 데이터베이스와 같이 네이티브 조인을 지원하지 않습니다. 

그러나 집계 파이프라인에서 왼쪽 외부 조인을 수행하는 $lookup 연산자를 제공하며, 전통적인 조인에 비해 효율성이 떨어질 수 있습니다.

모든 용도에 적합하지 않음

MongoDB는 구조화되지 않은 데이터를 처리하는 데 우수하지만, 복잡한 트랜잭션, 엄격한 일관성 또는 전통적인 조인 연산이 필요한 애플리케이션에는 적합하지 않을 수 있습니다.
