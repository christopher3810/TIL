
최근 github cto 인 jason warner 의 트위터를 보면 

>**Note**
>지난 십년 간 건축에서 가장 큰 실수 중 하나는 마이크로서비스를 전면적으로 채택한 것이라고 확신합니다.

라는 트윗을 시작으로 장문의 트윗이 올라왔다.

### 제안
---


>**Important**
>모놀리식에서 마이크로서비스에 이르는 스펙트럼에서, 저는 다음과 같이 제안합니다. \
>모놀리식 > 애플리케이션 > 서비스 > 마이크로서비스

첫째, 이것들은 생각일 뿐 반드시 적용해야 할 규칙이 아닙니다.
대규모 분산 시스템을 구축해 본 사람이라면 실제로는 그렇게 작동하지 않으며, 이에 맞춰 적응해야 한다는 것을 압니다.

둘째, 단계가 중요합니다. 만약 여러분이 5-50인 규모의 회사에서 이 글을 읽고 있다면... 
그냥 모놀리식을 고수하세요. 저를 믿으세요.

만약 여러분이 1만 명 규모의 회사에서 이 글을 읽고 있다면, 아마도 이 모든 것들을 어느 정도 가지고 있을 것입니다. 하지만 제 생각은 과거에 행해진 것과는 조금 다를 수 있습니다.

### 정의
---

이제 정의에 대한 이야기로 넘어갑니다. 

이 모든 것에 대한 정확한 정의는 존재하지 않습니다.

그러니 정의에 대해 논쟁하는 대신, 저는 보통 이렇게 생각합니다.

monolith - [http://xyz.com](http://xyz.com/)

apps - [http://abc.xyz.com](http://abc.xyz.com/), 가치 창출 또는 핵심 제안의 주요한 부분, 앱은 몇 개에 한정 

services - 앱/모놀리식을 지원, 핵심 인프라 부분, 넓은 영역에 필요, 핵심 준수 기능, 앱 팀이 작성하지 않을 수도 있음 (인프라가 유지)

microservices - 수백 줄의 코드, 대부분 일회용, 라이브러리나 SDK가 될 수 있거나 그래야 함

이제 왜 그렇게 생각하는지에 대해 이야기해 봅시다.

제 생각은 대략 이렇습니다.

### 속도와 위험
---

A. 전체 엔지니어링 팀이 하나의 큰 앱 안에서 작업하는 것이(레일즈 앱의 전체 사이트를 생각해보세요) 마이크로서비스가 실패할 방식에 대해 추론하는 것보다 일반적으로 더 쉽습니다.

B. 성장함에 따라 어쩔 수 없이 가질 분산 시스템은 수십 개는 말할 것도 없고 수백 개의 마이크로서비스, 각각 자신의 위험 프로필을 가지고 있는 것을 도입하면서 추론하기에 충분히 어렵습니다.

C. 완전히 마이크로로 가면서, 확산을 다루기 위한 새로운 개념을 도입해야 합니다.

D. 큰 것 중 하나. 각각의 맞춤형 인프라 서비스나 마이크로서비스는 극단적인 버전의 부채(IMV)입니다. 
코드는 부채지만, 서비스는 그것의 극단적인 버전입니다. 
이것이 여러분에게 무엇을 의미하는지 정말 생각해보세요. 
가장 높은 레버리지의 실제 지점이 되길 원하며, 있으면 좋을 것들보다는 그렇게 하세요.

분산 시스템 엔지니어들이 직면하는 도전 중 하나는 중복을 원치 않는다는 것입니다. 
그래서 몇몇 장소에서 수행되는 것을 보고 "이것을 빼내서 마이크로서비스로 만들자"고 생각합니다.

이론적으로 이것은 괜찮습니다. 그리고 몇 십 가지 인스턴스에 대해 수행된다면 괜찮습니다. 
그러나 이것이 수십 개의 마이크로서비스나... 더 나쁘게는... 거대한 회사 경계(마이크로소프트/구글/애플의 모든 색상 서비스를 생각해보세요)를 넘어설 때, 이것은 기술적 문제보다는 조직적 도전이 됩니다.

그리고 제가 제시하는 것이 어떤 면에서는 거짓 이분법처럼 보일 수 있지만, 실제로는 마이크로서비스와 관련된 확실한 기술적 도전이 있지만, 그것들과 관련된 조직적 도전이 더 많다는 것을 발견합니다.

그리고 제가 가장 걱정하는 것은 다음과 같습니다.

### 걱정 사항들
---

첫째, 인프라는 (회사가 특이하게 능숙한 CEO에 의해 이끌리지 않는 한) 거의 항상 우선 순위에서 밀려납니다.

둘째, 너무 많은 서비스는 일반적으로 소유권 문제와 경계 문제로 이어집니다.

셋째, 너무 많은 마이크로서비스를 다루기 위해 더 많은 도구를 도입합니다.

그리고 가장 중요한 것은, 라이브러리나 SDK 또는 무엇인가가 될 수 있었던 각 마이크로서비스는 생산 위험을 도입합니다.

더 많은 코드는 실제로 오버헤드이며, 더 많은 서비스는 고객이 직면하는 생산/경험 위험입니다. 두 접근 방식 모두 오버헤드/위험을 가지고 있지만, 분배의 비율이 다릅니다.

### 권장
---

그래서 제가 일반적으로 권장하는 것은 다음과 같습니다.

1. 가능한 한 오래 모놀리식을 유지하십시오.
    
2. 서비스는 일반적으로 앱 엔지니어링이 아니라 인프라 이유로 인프라에서 시작됩니다.
    
3. 모놀리식을 분해할 때, 작은 서비스가 아닌 큰 앱으로 분해하십시오.
    
4. 새로운 앱을 회사의 가상 벽으로 생각하세요.
    
5. 가능한 한 마이크로서비스보다 라이브러리를 선호하세요.

### 라이브러리 예시
---

"색상 서비스 도입"은 서비스 대신 라이브러리를 선택할 것이라는 극단적인 예입니다. 

예, 극단적인 예지만, 서비스의 전형적인 예로 많이 언급되는 것입니다.

"그런데 제이슨, 아마존이나 우버에 대해서는 어떻게 생각하나요?"

1. 네, 여러분은 여러분의 서비스를 하십쇼 저는 단지 제 경험을 바탕으로 말하는 것입니다.
2.  아마존이 서비스에 대한 명령을 내렸을 때 그 성공을 달성했다면, 마음껏 하세요.
3. 이것들은 규칙보다는 지침에 가깝습니다.

세계의 90% 이상의 회사들은 아마도 주 데이터베이스 클러스터를 사용하여 모놀리식을 운영하고, 데이터베이스 백업, 캐시, 프록시 몇 개를 사용하여 충분할 것입니다.

행성 규모에 도달하는 10%의 회사들의 경우(여기서 삼은 의도된 말장난은 아닙니다), 이것을 파악하는 것은 예술이 될 것입니다. 

분산 시스템과 확장하는 회사들을 결합한 것은 매우 복잡하고, 이를 수행한 사람은 매우 적어서, 그러한 회사들로부터 구체적인 교훈을 도출하기가 어렵습니다. 

각 컨텍스트와 인스턴스가 다릅니다. 제가 여기서 말하는 것은 문제에 접근하는 방법에 대한 생각들입니다.

그리고 이것으로 돌아가서,

모놀리식 > 앱 > 서비스 > 마이크로서비스

기본적으로 규모에 맞게 접근하는 방법입니다: 가능한 한 오래 하나의 큰 것으로 남아 있으세요. 

너무 작은 것들로 과도하게 수정하지 마세요, 성장하면서 (심지어 급속 성장하는 경우에도) 이를 통해 나아가세요. 이것은 조직과 기술 모두를 위한 것입니다.