
## Redis Collections

---

-   List → 말그대로 자료구조 list 형태

### Strings

---

-   기본 사용법
    -   Set<key> <value>
    -   Get <key>
    -   Set token:1234567 abcdefghijklmn
    -   Get token:1234567
    -   prefix를 붙여서 읽기 편하게 가져오기 편하게 사용함. 앞으로 붙일지 뒤로 붙일지 어떻게 붙일지 고민하면 좋음.
-   멀티 key
    -   mset<key1><value1><key2><value2>…..
    -   mget<key1><key2>…..
-   간단한 SQL을 대체하는 경우
    -   Using Set
        -   Set name: smayng smayng
        -   Set email: smayng [smayng@naver.com](mailto:smayng@naver.com)
    -   Using Mset
        -   Mset name : smayng smayng email: smayng [smayng@naver.com](mailto:smayng@naver.com)

### List

---

-   당연하듯이 Head와 Tail에는 값을 넣긴 편하고 중간에 무언가 값을 넣어야 한다면 List 를 사용하면 안됨.
-   기본 사용법
    -   insert
        -   Lpush<key> <A>
            -   Key:(A)
        -   Rpush<key> <B>
            -   Key:(A, B)
        -   앞에서 넣고 뒤에서 넣고
        -   잡큐들은 List를 많이 사용함
            -   집어 넣어두고 앞에서 하나씩 가져감
    -   pop
        -   Key:(A,B,C,D)
        -   Lpop<key>
            -   Pop A, Key: (B, C, D)
        -   Rpop<key>
            -   Pop D, Key: (A, B, C)
        -   BLPOP <key>
            -   누가 데이터를 Push하기 전까지 대기

### Set

---

-   데이터가 있는지 없는지 체크하는 용도
-   기본 사용법
    -   SADD <Key><Value>
        -   Value가 이미 key에 있으면 추가안함
    -   SMEMBERS <Key>
        -   모든 Value를 돌려줌
    -   SMEMBERS <Key><value>
        -   value가 존재하면 1, 없으면 0
-   특정 유저를 Follow 하는 목록, 친구리스트, 유니크해야 하고 해당 리스트를 저장하는 경우

### Sorted Set

---

-   Set은 순서가 없으나 Sorted Set은 Score를 줘서 순서를 부여할 수 있음
    
-   Score 순으로 정렬 가능
    
-   기본 사용법
    
    -   ZADD<Key><Score><Value>
        
        -   Value가 이미 Key에 있으면 해당 Score로 변경됨.
        
        <aside> 🔥 Sorted Set의 Score는 실수형임 javascript 정수형도 실수형임 어떤 특정 정수는 실수형에서 표현이 안됨. 숫자값을 보내야되면 Long이나 이런건 문자열로 보내야됨. 특정 Score를 넣어었는데 특정 Score 범위를 보면 없을 수도 있다. 이부분에서 문제가 생길수가 있다.
        
        </aside>
        
    -   ZRANGE<Key><StartIndex><EndIndex>
        
        -   해당 Index 범위 값을 모두 돌려줌
        -   Zrange testkey 0 -1
            -   모든 범위를 가져옴.
        
        ```java
        select * from rank order by score limit 50, 20;
        zrange rank 50 70
        ```
        
-   유저 랭킹 보드 사용 가능
    

### Hash

---

-   key ,value 안에 sub key value를 줄수 있게 만들어 줌
-   기본 사용법
    -   Hmset <key> <subkey1> <value1> <subkey2> <value2>
    -   Hgetall <key>
        -   해당 key의 모든 subkey와 value를 가져옴
    -   Hget <key><subkey>
    -   Hmget <key><subkey1><subkey2>……

```java
Insert into user(name, email) values('smyang', 'smyang@naver.com');
hmset smayng name smayng
```

## Collection 주의사항

---

-   하나의 컬렉션에 너무 많은 아이템을 담으면 좋지 않음
    -   10000개 이하 몇천개 수준으로 유지하는게 좋음.
    -   적게쓰는건 괜춚
-   Expire는 Collection의 item 개별로 걸리지 않고 전체 Collection에 대해서만 걸림
    -   10000개 Collection 에 TTL을 걸어버리면 시간 지나고 10000개가 전부 날라감