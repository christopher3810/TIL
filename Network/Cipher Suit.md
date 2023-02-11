***

### Cipher Suit란?
***

[ms doc](https://learn.microsoft.com/en-us/windows/win32/secauthn/cipher-suites-in-schannel) 의 설명

>A cipher suite is a set of cryptographic algorithms. The schannel SSP implementation of the TLS/SSL protocols use algorithms from a cipher suite to create keys and encrypt information. A cipher suite specifies one algorithm for each of the following tasks:  
	1. Key exchange  
	2. Bulk encryption  
	3. Message authentication

**cipher suite는 암호화 알고리즘의 집합입니다.**
TLS/SSL 프로토콜의 schannel SSP 구현은 **cipher suite**에서 알고리즘을 사용하여 키를 생성하고 정보를 암호화합니다.
**cipher suite**는 다음과 같은 작업에 대해 각각 하나의 알고리즘을 지정합니다.
1. Key exchange  
2. Bulk encryption  
3. Message authentication

### Cipher suite의 구조
*** 


![Cipher suite](https://user-images.githubusercontent.com/61622657/218285255-0023f9b4-c1d6-4570-ab6f-02d259ccaffa.png)

**KeyExchange**
Server와 Client간 Key를 교환할 방식을 선정하는 것입니다.

**Signature**
Server와 Client간 교환한 인증서를 확인하는 알고리즘입니다.

**Bulk Enxryption**
클라이언트와 서버 간에 교환되는 메시지를 암호화합니다
**GCM 의 경우** 블록 암호 운용 방식 이며 데이터를 암호화 할때 한꺼번에 암호화 하는 것이 아니라 블록 단위로 암호화 하게 되는데,
블록된 암호화 패킷을 조합하여 데이터를 추측 하는 것을 방지하기 위한 방식입니다.

**MessageAuthentication**
MessageAuthentication알고리즘은 메시지의 [무결성을](https://learn.microsoft.com/en-us/windows/desktop/SecGloss/i-gly) 보장하는 메시지 [해시](https://learn.microsoft.com/en-us/windows/desktop/SecGloss/h-gly) 와 서명을 생성합니다.