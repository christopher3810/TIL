
멱등성? 멱등하다?

>冪은 **'덮을 멱**'이라는 [한자](https://namu.wiki/w/%ED%95%9C%EC%9E%90 "한자")로, '덮다', '[거듭제곱](https://namu.wiki/w/%EA%B1%B0%EB%93%AD%EC%A0%9C%EA%B3%B1 "거듭제곱")' 등을 뜻한다.
>等은 **'무리 등**'이라는 [한자](https://namu.wiki/w/%ED%95%9C%EC%9E%90 "한자")로, 무리, 같다, 등급을 뜻한다. 그래서 **'같을 등**'이나 **등급 등**이라고도 한다.

**즉 연산을 여러 번 적용하더라도 결과가 달라지지 않는 성질을 의미**

idempotent
An idempotent [element](https://dictionary.cambridge.org/ko/%EC%82%AC%EC%A0%84/%EC%98%81%EC%96%B4/element "element") of a set does not [change](https://dictionary.cambridge.org/ko/%EC%82%AC%EC%A0%84/%EC%98%81%EC%96%B4/change "change") in [value](https://dictionary.cambridge.org/ko/%EC%82%AC%EC%A0%84/%EC%98%81%EC%96%B4/value "value") when [multiplied](https://dictionary.cambridge.org/ko/%EC%82%AC%EC%A0%84/%EC%98%81%EC%96%B4/multiply "multiplied") by itself.

영어인 idempotent의 의미도 유사함.


# HTTP 메서드의 멱등성

HTTP 메서드에도 멱등성이 존재함. 

GET의 경우 여러 번 호출해도 같은 결과가 돌아오고, 리소스에 변화를 일으키지 않기 때문에 멱등성이 보장된 메서드.

|메서드|멱등성|
|---|---|
|CONNECT|X|
|DELETE|O|
|GET|O|
|HEAD|O|
|OPTIONS|O|
|POST|X|
|PUT|O|
|PATCH|X|
|TRACE|O|

