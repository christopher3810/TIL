# mutable Infrastructure

### 🤔 mutable Infrastructure란 무엇일까?
mutable 은 단순하게 변경가능하며 customizable을 의미한다.
더 간단하게 mutable은 필요 할때 변경 할 수 있는 모든 것을 의미한다.
변경 및 업데이트가 주기적으로 수행되며 mutable 한 infrastructure의 경우 서버에 로그인하여 구성 및 세부 정보를 업데이트 하는 것이 가능하다.

이처럼 변경가능한 경우 한가지 상황이 발생하는데
infrastructure 환경마다 일관성이 없어질 수 있다는 것이다.
즉 서버마다 구성 및 설정이 다를 수 있고 다양한 서버 간의 균형을 유지하기가 힘들어진다.

mutable 함으로써 얻어지는 장단점이 존재하는데.

장점
1. infrastructure가 완전히 customization이 가능하기 때문에 모든 서버에서 실행중인 모든 응용프로그램은 요구사항을 충족할 수 있다.
2. infrastructure 담당하는 담당자가 전반적인 서버를 인식할 수 있으므로(하지만 이부분은 담당자가 인지할 수 있는 server infrastructure의 규모가 제한적이기 때문에 해당 규모에 내에서만 가능하는 듯 하다.)문제를 더 쉽게 발견 할 수 있다.

단점
1. 서버마다 구성이 다르며 하나부터 열까지 완전히 다를 수 있기 때문에 *configuration drift가 발생할 수 있으며 추가적인 server 버전관리와 같은 문서화 절차가 존재하지 않는다면 버전 관리가 어려워집니다.

2. 각 서버에서 정도에 따라 다르지만 수동 구성을 수행해야 할 가능성이 높아집니다.
시간이 많이 걸리고 human error를 통한 error가 발생하기 쉬운 process 입니다. 

3. 환경구성이 다다름으로 인해서 배포가 실패할 수 도 있습니다.

<br/>

> *configuration drift 
configuration drift(구성 드리프트)는 손으로 직접 수정한 임시 변경 및 업데이트와 일반적인 엔트로피 때문에 시간이 지남에 따라 infrastructure server들이 서로 더욱 다르게 되는 현상을 말합니다.

<br/>

### 참조
[linked-in history-infrastructure-immutable-muttable-tools](https://www.linkedin.com/pulse/history-infrastructure-immutable-mutable-tools-achieve-amit-kalia)

[configuratioj-drift](http://kief.com/configuration-drift.html)