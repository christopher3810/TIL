***

### Application Level


### Consist Hashing

- 서버가 추가가되거나 줄어들어도 문제가 생기지 않는 방식 hash 링을 만듬.

### HashRing

![consistence_hashing](https://user-images.githubusercontent.com/61622657/226858099-e3cb2b56-f460-4e24-958a-cd710cd5ab64.png) \
출처 : [https://binux.tistory.com/119](https://binux.tistory.com/119)

- 어떤 key가 들어오면 hash를해서 hash 함수를 통해 유한한 값의 범위로 결과가 나온다면

ex) 0~1

- 이 유한한 범위를 노드의 수로 나눌 수 있다.

ex) Node1 (0), Node2 (0.3), Node3 (0.6)

- Hash ring 에서 저장할 값의 hash 값과 가장 가까운 값을 가지는 Node에 해당 값을 저장함.

  

### Node가 만약 사용불가가 된다면

![consistence_hashing_2](https://user-images.githubusercontent.com/61622657/226858108-7e52a693-94dc-4682-a30d-b4fe8e37de76.png) \
출처 : [https://binux.tistory.com/119](https://binux.tistory.com/119)

- 만약에 Node3이 사용 불가가 된다면. Node에 저장되어 있던 값들은 어떻게 될까?

![consistence_hash3](https://user-images.githubusercontent.com/61622657/226858113-ade66357-a00b-4998-945d-95e3477bf93c.png) \
출처 : [https://binux.tistory.com/119](https://binux.tistory.com/119)

- hash 값 이상 값을 가지는 가장 가까운 Node였으므로 Node 3에 저장되는 범위는 자동으로 Node 1을 가르키게 됨

- Redis에서 node추가 제거시 node module 단위로 % 을한뒤 분산시킬 때에 특정 노드가 죽을시 %연산을 통해 저장되있는 데이터들이 다양하게 다른 서버로 이동하는 것을 방지 할 수 있음.

  

## Sharding

- Sharding이란 기본적으로 데이터를 어떻게 나눌 것인가?

- 데이터를 어떻게 찾을 것인가?

- 하지만 하나의 데이터를 모든 서버에서 찾아야 하는 경우 라면?

- 상황마다 Sharding 전략이 다름


### Range Sharding

<img width="433" alt="thiiing-db-range-sharding" src="https://user-images.githubusercontent.com/61622657/226859028-0f73b9ec-c4b3-40fd-81a4-7a05f206f4d8.png">

  
- 특정 Range를 정의하고 해당 Range에 속하면 거기에 저장

    - key 500은

    - 1~1000, 1001~2000, 2001~3000 중 첫번째에 저장

- Range Sharding은 PK의 범위를 기준으로 DB를 특정하는 방식입니다. 간략한 장단점은 아래와 같다.

    - 장점 : 모듈러샤딩에 비해 기본적으로 증설에 재정렬 비용이 들지 않음.

    - 단점 : 일부 DB에 데이터가 몰릴 수 있음

  

### Indexe

![Indexed_sharding](https://user-images.githubusercontent.com/61622657/226859167-ca44cc49-56b5-45e6-8f96-9a82683f060c.png)

참조 [https://codingexplained.com/coding/elasticsearch/understanding-sharding-in-elasticsearch](https://codingexplained.com/coding/elasticsearch/understanding-sharding-in-elasticsearch)

  
- Client → where is 2? → server → 2 is on Server 3

- Index server에 대다수 의존하고 있기 때문에 Index 서버가 죽으면 에러가 발생

  

### Moduler sharding

<img width="427" alt="thiiing-db-modular-sharding" src="https://user-images.githubusercontent.com/61622657/226859280-bbeb7685-251f-4c5f-b11d-3ef69c7ee90a.png">

Moduler sharding은 PK를 모듈러 연산한 결과로 DB를 특정하는 방식.

  

- 장점 : Range Sharding에 비해 데이터가 균일하게 분산됨.

- 단점 : DB를 추가 증설하는 과정에서 이미 적재된 데이터의 재정렬이 필요함.

- 데이터량이 일정 수준에서 유지될 것으로 예상되는 데이터 성격을 가진 곳에 적용하는게 적합.

- 데이터가 균일하게 분산된다는 점은 트래픽을 안정적으로 소화하면서도 DB 리소스를 최대한 활용할 수 있음.

  

# 참조

  

---


[Consistent hashing (Hash ring)](https://binux.tistory.com/119) \
[[우아한테크세미나] 191121 우아한레디스 by 강대명님](https://www.youtube.com/watch?v=mPB2CZiAkKM&ab_channel=%EC%9A%B0%EC%95%84%ED%95%9CTech) \
[DB분산처리를 위한 sharding | 우아한형제들 기술블로그](https://techblog.woowahan.com/2687/)