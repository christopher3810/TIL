
***


## 종류
<br>

![db_join_ex1](https://user-images.githubusercontent.com/61622657/221702813-c5c925a7-e83d-471a-8702-96554b9cecb5.png)

- 내부조인(InnerJoin) : 양쪽 테이블에 같은 데이터가 있을 떄만 결합하는 방식

- 왼쪽 외부 조인(left outer join) , 오른쪽 외부 조인(Right Join) : 각각 어느 한쪽에만 데이터가 있어도 데이터를 결합하는 방식.

- (Full Outer Join) : 두 테이블간 출력가능한 데이터를 모두 출력하는 방식

<br>

### 교차 조인

- 교차 조인(cross join)은 수학적 관점에서 봤을때 데카르트 곱 이라고 하는 곱집합 개념으로 조인에 참여하는 테이블에서 발생할 수 있는 모든 조합을 찾아내어 반환합니다.


<aside>

🔥 모든 경우의 수가 출력 대상이므로 내부 조인에 비해 수십 배에서 수백 배이상 많은 데이터 양을 얻을 수 있습니다.

다만 오버헤드가  발생하는 만큼 주의를 요합니다.


</aside>

![Cross_join_ex1](https://user-images.githubusercontent.com/61622657/221702870-10c1dcc5-45f9-4f5f-abeb-e16a74325fa2.png)

- 4 + 4 + 4 + 4 = 16가지


```sql

SELECT 학생.학번, 학생.이름, 지도교수.학번, 지도교수.교수명

FROM 학생

CROSS JOIN 지도교수

```


| 학번 | 이름 | 학번 | 교수명 |
| --- | --- | --- | --- |
| 1 | 이순신 | 1 | 이성계 |
| 2 | 권율 | 1 | 이성계 |
| 1 | 이순신 | 2 | 이방원 |
| 2 | 권율 | 2 | 이방원 |


<br>

### 자연조인
  

- 자연 조인(natural join)은 2개의 테이블에 동일한 열 명이 있을 때 조인 조건절을 따로 작성하지 않아도 자동으로 조인을 수행해주는 방법입니다.

    - 조인이 제대로 성사되면 내부 조인과 동일한 결과가 출력됩니다.

    - 열들의 데이터 유형이 서로 달라도 자연 조인이 수행됩니다.

학생 테이블

| 학번 | 이름 | 성별 |
| --- | --- | --- |
| 1 | 이순신 | 남 |
| 2 | 권율 | 남 |
| 3 | 곽재우 | 남 |
| 4 | 신립 | 남 |


지도교수 테이블

| 학번 | 지도교수명 |
| --- | --- |
| 1 | 손자 |
| 2 | 오자 |
| 4 | 한신 |
| 99 | 조조 |

  

🌟 자연 조인을 의도하고 명시적으로 작성해야 할 키워드는 **NATURAL JOIN** 입니다.

해당 키워드를 작성하면 **조인 조건절을0 알아서 찾아주므로** 직접적인 조인 조건문은 작성하지 않습니다.


(on 학생.학번 = 지도교수.학번 과같이 작성시 오류납니다.)


```sql

SELECT 학생.* , 지도교수.*

FROM 학생

NATURAL JOIN 지도교수

```

  

결과

| 학번 | 이름 | 성별 | 학번 | 교수명 |
| --- | --- | --- | --- | --- |
| 1 | 이순신 | 남 | 1 | 손자 |
| 2 | 권율 | 남 | 2 | 오자 |
| 4 | 신립 | 남 | 4 | 한신 |

<br>


<aside>

🔥 만약 위와 같이 동일한 열명이 있지 않은

동일한 **열 명칭이 없는 테이블간 자연조인을 수행**한다면

가능한 모든 경우의 수를 조합하는 **교차조인으로 수행**됩니다.

</aside>



### 참조
***
http://www.yes24.com/Product/Goods/102382080