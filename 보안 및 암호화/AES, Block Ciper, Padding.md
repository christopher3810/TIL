
### AES ?

>[!NOTE]
>AES (Advanced Encryption Standard)는 대칭 키 암호화 알고리즘으로, 데이터를 안전하게 암호화하고 복호화하는 데 사용됨. 
>AES 는  [ISO](https://en.wikipedia.org/wiki/International_Organization_for_Standardization "International Organization for Standardization")/[IEC](https://en.wikipedia.org/wiki/International_Electrotechnical_Commission "International Electrotechnical Commission") [18033-3](https://en.wikipedia.org/wiki/List_of_International_Organization_for_Standardization_standards,_18000-19999 "List of International Organization for Standardization standards, 18000-19999") 표준이며  [NSA](https://namu.wiki/w/NSA "NSA")에 의해 1급 비밀에 사용할 수 있도록 승인된 암호화 알고리즘임. 
>AES는 세 가지 키 길이를 지원 : 128비트, 192비트, 256비트. 이 키 길이는 암호화의 강도를 결정. \
>당연하게 비트수가 길수록 보안성을 올라가나 프로세스는 증가함. 


- **AES-128**: 128비트 키를 사용하며, 10 라운드의 암호화 과정을 거침.
- **AES-192**: 192비트 키를 사용하며, 12 라운드의 암호화 과정을 거침.
- **AES-256**: 256비트 키를 사용하며, 14 라운드의 암호화 과정을 거침.

>[!NOTE]
>Java 에서는 SecretKey 의 길이에 따라서 정의됨.


### Block Ciper Mode

>[!IMPORTANT]
>AES 암호화는 고정된 크기의 데이터 블록을 처리하는데, 이때 평문 데이터의 길이가 블록 크기와 일치하지 않을 경우 패딩이 필요합니다. \
>패딩은 데이터 블록을 채워 고정된 길이를 유지하는 방식으로, PKCS#7 같은 표준 패딩 방식을 사용하여 블록을 채웁니다.

#### ECB

![ECB](https://github.com/christopher3810/TIL/assets/61622657/b08a7a75-b3ac-49e3-9f86-37f6ca38d7d8)

ECB(Electronic Code Book)는 가장 간단한 AES 암호화 모드.

이 모드는 동일한 평문 블록에 대해 항상 같은 암호문 블록을 생성.

특히 이미지와 같은 구조화된 데이터에 사용할 경우 안전하지 않음.

ECB 모드는 병렬 처리가 가능하나, 고유한 보안 취약점 때문에 일반적으로 권장되지 않음.

#### CBC

![CBC](https://github.com/christopher3810/TIL/assets/61622657/937af214-0e53-4f8c-b777-156396f6e38c)


>[!IMPORTANT]
>XOR 연산은 암호화 과정에서 중요한 역할을 하며, 키 스트림이나 IV와 평문 데이터를 결합하여 암호문을 생성합니다.  \
>이는 블록의 데이터를 암호화하는 데에 있어서 두 데이터의 비트를 비교하여 같으면 0, 다르면 1을 반환하는 방식으로 작동합니다.

CBC(Cipher Block Chaining) 모드는 **initialization vector** (IV)를 사용하여 각 블록을 암호화하기 전에 이전 암호문 블록과 XOR 연산. 

이는 같은 평문 블록에 대해서도 다른 암호문 블록을 생성하여 패턴을 숨김.

>[!NOTE]
>평문의 길이가 블록 크기의 배수가 아닐 경우, 블록 크기에 맞추기 위해 패딩이 필요. \
>암호화 과정에서 마지막 블록의 크기가 부족하면, 패딩을 추가하여 블록 크기를 채움. \
>각 블록이 이전 블록에 의존적이므로, 블록 간의 연관성이 강해짐. \
>이로 인해 단일 블록의 변경이 전체 암호문에 영향을 미침

그러나, 하나의 블록이 손상될 경우 후속 블록에 영향을 줄 수 있음.

**Oracle Padding Attack** 공격에 취약할 수 있어, 주기적인 키 교체가 필요함.

>[!WARNING]
>Oracle Padding Attack \
>Oracle Padding Attack은 암호문의 패딩이 유효한지 알려주는 정보를 이용하는 공격 방식.  \
>암호화된 데이터의 패딩을 검증하는 과정에서 오류 메시지를 통해 유추할 수 있는 정보를 공격자가 수집하고, 이를 이용해 평문을 추론해내는 방식. \
>이 공격은 CBC 모드와 같이 패딩이 사용되는 암호화 모드에서 주로 발생할 수 있음.

#### CFB (Ciper Feed Back)

![CFB](https://github.com/christopher3810/TIL/assets/61622657/f4202cae-333f-4a2b-8be2-20298b7239ed)

1. 먼저 CFB가 IV를 암호화한 다음 일반 텍스트 블록과 XOR하여 암호 텍스트를 얻음.
2. 암호화 결과를 일반 텍스트로 암호화.
3. 일반 텍스트를 직접 암호화하지 않기 때문에 암호 텍스트를 사용하여 일반 텍스트와 XOR하여 암호 텍스트를 가져옵니다. 따라서 이 모드에서는 데이터를 패딩할 필요가 없음.

>[!IMPORTANT]
>XOR 연산하여 암호문을 생성 한 뒤 다음 블록에서는 이전 블록의 암호문 일부를 사용하여 IV로 사용하는 것이 포인트. \
>평문을 직접 암호화하지 않고, 암호화된 블록(또는 IV)과 평문을 XOR 연산하기 때문에, 평문의 길이가 블록 크기의 배수가 아니어도 블록 크기에 맞출 필요가 없게 되버림 따라서 패딩이 필요 없음.

또한 암호화가 아닌 병렬로 데이터를 복호화할 수 있음.

이 모드는 CBC와 유사하므로 블록이 깨지면 다음 블록에 모두 영향을 미침.

>[!WARNING ]
>CFB 모드는 replay attack에 취약함. \
>이는 CFB가 각 블록의 암호화에 이전 블록의 암호문을 사용하기 때문. \
>공격자가 암호문의 순서를 변경하거나 재전송함으로써 수신자는 변경된 암호문을 받고, 이를 정상적인 메시지로 잘못 해석할 수 있음. \
> CBC 모드와는 달리, CFB 모드에서는 단일 블록의 변경이나 재배열이 바로 다음 블록에만 영향을 미치므로, 일부 블록의 재사용이 가능.

>[!NOTE]
>**CPA (Chosen Plaintext Attack)**: CPA는 공격자가 임의의 평문을 선택하여 그에 대한 암호문을 얻고, 이 정보를 사용하여 암호 시스템을 분석하는 공격. \
>CPA에 대한 방어는 암호 시스템이 임의의 선택된 평문에 대해서도 안전하다는 것을 의미합니다. 
>
>**CCA (Chosen Ciphertext Attack)**: CCA는 공격자가 암호문을 선택하여 그에 대한 평문을 얻을 수 있고, 이 정보를 사용하여 암호 시스템을 공격하는 방법. \
>CCA에 대한 방어는 더 어렵고, 암호 시스템이 이러한 유형의 공격에도 안전하다는 것을 의미합니다.

**CPA로부터는 안전하지만 CCA에 쉽게 노출될 수 있음.**

보안을 유지하기 위해 이 모드에서는 **2^((n+1)/2) 암호화 블록**마다 키를 변경하는 것을 권장함.

왜 **2^((n+1)/2) 암호화 블록**마다 일까?


>[!NOTE]
> Birthday Paradox. \
>생일이 366가지이므로 임의의 두 사람의 생일이 같을 확률은 1/366이고, 따라서 366명쯤은 모여야 생일이 같은 경우가 있을 것이라고 생각하기 쉽다. \
>실제로는 23명만 모여도 생일이 같은 두 사람이 있을 확률이 50%를 넘고, 57명이 모이면 99%를 넘어간다 .\
>이 문제는 무작위로 만난 366명의 생일이 서로 겹치지 않고 고르게 분포할 확률이 사실상 없다는 점을 나타낸다.

즉 [암호학적 해시 결과](https://ko.wikipedia.org/wiki/%EC%95%94%ED%98%B8%ED%95%99%EC%A0%81_%ED%95%B4%EC%8B%9C_%ED%95%A8%EC%88%98 "암호학적 해시 함수")가 같은([해시 충돌](https://ko.wikipedia.org/wiki/%ED%95%B4%EC%8B%9C_%EC%B6%A9%EB%8F%8C "해시 충돌")) 두 입력값을 찾는 것 역시 모든 입력값을 계산하지 않아도 충분히 높은 확률로 해시 충돌을 찾을 수 있다.

`n` 비트 값은 이진수로 표현될 때 `n` 자리를 가지며, 각 자리는 0 또는 1 중 하나의 값을 가질 수 있음.

이와 유사하게, 2비트 값은 00, 01, 10, 11의 4가지 값을 가질 수 있음.

일반화하여, `n` 비트 값은 `2^n`개의 고유한 조합을 가질 수 있음.

이는 각 비트 위치에 2가지 선택(0 또는 1)이 가능하므로, 모든 비트 위치에 대한 조합의 총 수는 `2^n`이 됨.

무작위로 `k`개의 `n` 비트 값들을 선택할 때, 각각의 선택은 서로 독립적. 

첫 번째 선택은 어떤 `2^n` 개 중 하나를 선택할 수 있으므로, 충돌 확률은 0. 

두 번째 선택에서는 이제 `2^n - 1`개의 고유한 값들 중 하나를 선택해야 함.

첫 번째 선택과 동일한 값을 선택하지 않을 확률은 `(2^n - 1) / 2^n`이 됨.

이러한 방식으로 계속하면, `k`번째 선택에서는 `2^n - (k - 1)`개의 고유한 값들 중 하나를 선택해야 하므로, 충돌하지 않을 확률은 `(2^n - (k - 1)) / 2^n`이 됨.

충돌이 발생하지 않을 확률을 모두 곱한 값이 0.5(즉, 충돌 확률이 50%가 되는 지점)에 도달하는 `k`의 값은 대략 `2^(n/2)`임.

즉`n` 비트 값들 중 적어도 하나의 쌍이 동일할 확률이 50%에 도달하는 지점을 나타냄.

하지만 실제 충돌이 발생할 가능성이 이론적 확률보다 더 빨리 높아질 수 있다고 가정하는 것이 일반적.

따라서, `2^(n/2)`보다 더 작은 값에서 키를 변경하는 것이 보다 안전하다고 간주됩니다. `2^((n+1)/2)`는 `2^(n/2)`보다 약간 큰 값이며, 이를 통해 추가적인 보안 마진을 확보하게 됨.


#### OFB

![OFB](https://github.com/christopher3810/TIL/assets/61622657/210c271a-e5f6-402b-b6ed-6a03ea7e73f7)

#### CTR

![CTR](https://github.com/christopher3810/TIL/assets/61622657/a8bd0da4-cad5-48be-aeb2-9cbed6092db5) 


<b>Summary</b>

| Mode | Advantage | Disadvantage |
|------|-----------|--------------|
| ECB | 간편함.<br> 빠름.<br> 암복호화에서 parallel 을 지원함 | plaintext의 중복 데이터가 ciper text에 반영될 수 있음.<br>plaintext가 cipertext를 delete하거나 replace하면서 작동할 가능성이 존재.<br>ciphertext packet 이 손상되었으면 plaintext 에 영향을 줄 수 있음.<br>replay attack 에 취약함, 사용하면 안됨 |
| CBC | parallel computing 을 decryption 단계만 지원.<br> ciphertext packet을 decrypt하는데 빠름.<br>plaintext내에 중복된 데이터가 ciphertext에 반영되지 않음 | 암호화 과정에서 parallel computing 을 지원하지 않음.<br>잘못된 block이 이후에 올 block에 영향을 끼칠 수 있음 |
| CFB | padding 을 하지 않음.<br> parallel computing 을 decryption 단계만 지원.<br>ciphertext packet을 decrypt하는데 빠름.<br> 사전에 암호화, 복호화를 prepared 할 수 있음 | 암호화 과정에서 parallel computing 을 지원하지 않음.<br>replay atttack에 대처가 안됨.<br>잘못된 block이 이후에 올 block에 영향을 끼칠 수 있음 |
| OFB | padding 을 하지 않음.<br> 암호화.<br> 복호화를 미리 prepared 할 수 있음.<br>암호화와 복호화에 같은 구조를 사용함.<br> BadBlock은 오직 최신 Block에만 영향을 끼침 | parallel computing을 아에 지원안함.<br>Mallory(악의적 공격자)는 몇몇 손상된 ciphertext를 plaintext로 변경할 수 있음 |
| CTR | padding 을 하지 않음.<br> 암호화.<br> 복호화를 미리 prepared 할 수 있음.<br>parallel computing을 지원함.<br> 암호화와 복호화에 같은 구조를 사용함.<br>BadBlock은 오직 최신 Block에만 영향을 끼침 | Mallory(악의적 공격자)는 몇몇 손상된 ciphertext를 plaintext로 변경할 수 있음 |

