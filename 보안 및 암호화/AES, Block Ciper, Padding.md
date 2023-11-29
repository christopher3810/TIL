
### AES ?

>[!INFO]
>AES (Advanced Encryption Standard)는 대칭 키 암호화 알고리즘으로, 데이터를 안전하게 암호화하고 복호화하는 데 사용됨. 
>AES 는  [ISO](https://en.wikipedia.org/wiki/International_Organization_for_Standardization "International Organization for Standardization")/[IEC](https://en.wikipedia.org/wiki/International_Electrotechnical_Commission "International Electrotechnical Commission") [18033-3](https://en.wikipedia.org/wiki/List_of_International_Organization_for_Standardization_standards,_18000-19999 "List of International Organization for Standardization standards, 18000-19999") 표준이며  [NSA](https://namu.wiki/w/NSA "NSA")에 의해 1급 비밀에 사용할 수 있도록 승인된 암호화 알고리즘임. 
>AES는 세 가지 키 길이를 지원 : 128비트, 192비트, 256비트. 이 키 길이는 암호화의 강도를 결정. \
>당연하게 비트수가 길수록 보안성을 올라가나 프로세스는 증가함. 


- **AES-128**: 128비트 키를 사용하며, 10 라운드의 암호화 과정을 거침.
- **AES-192**: 192비트 키를 사용하며, 12 라운드의 암호화 과정을 거침.
- **AES-256**: 256비트 키를 사용하며, 14 라운드의 암호화 과정을 거침.

>[!info]
>Java 에서는 SecretKey 의 길이에 따라서 정의됨.


### Block Ciper Mode


#### ECB

![ECB](https://github.com/christopher3810/TIL/assets/61622657/b08a7a75-b3ac-49e3-9f86-37f6ca38d7d8)

#### CBC

![CBC](https://github.com/christopher3810/TIL/assets/61622657/937af214-0e53-4f8c-b777-156396f6e38c)

#### CFB

![CFB](https://github.com/christopher3810/TIL/assets/61622657/f4202cae-333f-4a2b-8be2-20298b7239ed)

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

