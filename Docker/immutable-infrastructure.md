# Immutable Infrastructure

<br />

### ๐ค Immutable Infrastructure๋ ๋ฌด์์ผ๊น?

Immutable Infrastructure๋ ๋ง๊ทธ๋๋ก mutable ์ ๋ฐ๋ ์ด๋ค.\
Immutable Infrastructure๋ ๋ง๊ทธ๋๋ก ์์ ํ  ์ ์๋ค.\
ํน์  ์๊ตฌ์ฌํญ์ ๋ง๊ฒ ๊ตฌ์ฑ์์๋ฅผ ๊ตฌ์ถํ๋ฉฐ ์์ผ๋ก ์์ ํ  ์ฌ์ง๊ฐ ์๋ค.

server๋ฅผ ๋ณ๊ฒฝํด์ผ ํ๋ ๊ฒฝ์ฐ ์๊ตฌ์ฌํญ์ ๋ง์ถฐ์ ์์ ํ ์๋ก์ด infrastructure๊ฐ ์ค์น ๋๋ค.\
infrastructure๋ ์๋ฐ์ดํธ๋ software์ configuration์ผ๋ก ๊ตฌ์ฑ๋๋ค.\
๊ฐ์ํ ๊ธฐ์ ์ Immutable infrastructure๋ฅผ ๊ณ ์ํ๋๋ฐ ์ค์ํ ์ญํ ์ ํด์๋ค.\
templates์ ์ฌ์ฉํ์ฌ ๋ง์ ์ธ์คํด์ค๋ฅผ ๊ตฌ์ฑํ  ์ ์๋ค.
public cloud์ ์์ด์ ์ค์ถ๋ก ์์ฉํ๋ค.\
ex) Amazon Ec2์ ๊ฒฝ์ฐ Amazon image AMI๋ฅผ ํ์ฉ EC2๋ฅผ ๊ตฌ์ฑํ  ์ ์๋ค.

docker๊ฐ ๊ฐ๋ฒผ์ด image์ ๊ฒฉ๋ฆฌ๋ container๋ฅผ ํ์ฉํ์ฌ Immutable Infrastructure๋ฅผ ์ ์ ์ฉํ๊ณ  ์๋ค.

Immutable Infrastructure์ ์ฅ๋จ์ ์ด ๋ฌด์์ผ๊น?

์ฅ์ 
1. ํ์คํธ ๊ฐ๋ฐ์์ ํ์คํธ ๋ฐ๋ณต๊น์ง ๊ฐ๋ฅํ๋ค.
2. ๊ตฌ์ฑ ๋๋ฆฌํํธ๊ฐ ๋ฐ์ํ  ๊ฐ๋ฅ์ฑ์ด ์๋ค.
3. ํน์  ์๋ฒ ์ธ์คํด์ค์ ๋ฌธ์ ๊ฐ ๋ฐ์ํ  ์ ๋ฌธ์ ์ ์์ธ์ด ๋๋ ์ธ์คํด์ค๋ฅผ ์ญ์ ํ๊ณ  ๋ค์ ๋์ฐ๋ ๊ฒ์ผ๋ก ์๋ฌ ์์ ์ด ๊ฐ๋ฅํ๋ฉฐ ์ง์ ํ๋์ด ๊ฐ์ํ  ์ ์๋ค.
4. *SnowFlake ์๋ฒ๋ฅผ ๋ฐฉ์งํ๋ค.


> *SnowFlakeServer : ์ค์  ๊ตฌ์ฑ์ด ์๋ ๊ตฌ์ฑ๋ ์๋ฒ์ ์กฐ๊ธ์ฉ ๋๋ ํฌ๊ฒ ๋ฌ๋ผ์ ธ ๊ณ ์ ํ๊ณ  ๊ด๋ฆฌํ๊ธฐ ์ด๋ ค์ด ์๋ฒ๋ฅผ ์๋ฏธํฉ๋๋ค.
๋์ก์ด๋ฅผ ๋ณด๋ฉด ๋ชจ์์ด ์กฐ๊ธ์ฉ ๋ค๋ฅธ๊ฒ์์ ์๋ฏธ๋ฅผ ๋ฐ์์.
SnowFlakes DB service ํ์ฌ์ ์๋ฒ๊ฐ์๋.



๋จ์ 
1. ๊ตฌ์ฑ์ด ๋ณ๊ฒฝ๋  ๋๋ง๋ค ์๋ฒ๋ฅผ ์ฌ๊ตฌ์ฑํด์ผ ํ ์๋ ์์ผ๋ฉฐ ์๊ฐ์ด ์ค๋๊ฑธ๋ฆฌ๋ ์์์ด ๋  ์ ์๋ค.

2. ๋ฐ์ดํฐ ์ ์ฅ์๋ฅผ ๋ก์ปฌํํ๋๊ฒ ์๋๋ผ ์ธ๋ถ์ ๋ฌ์ผ ํ  ์๋ ์๊ธฐ ๋๋ฌธ์ (์๋ฌ์ ํด๋จ ์ธ์คํด์ค๋ฅผ ์ ๊ฑฐํ๊ณ  ๋ค์๋์) ์คํ ๋ฆฌ์ง์ ๋ฐ๋ฅธ ์ถ๊ฐ์ ์ธ ๋ฆฌ์์ค๊ฐ ํ์ ํ  ์๋ ์๋ค.

<br />

Immutable Infrastructure๋ฅผ ์ฌ์ฉํ๋ Tool๋ค

1. Docker
2. Kubernetes
3. Terraform
4. Spinnaker
5. Packer
6. [AWS Cloud Formation](https://aws.amazon.com/ko/blogs/mt/leveraging-immutable-infrastructure-nubank/)



<br/>

### ์ฐธ์กฐ
[linked-in history-infrastructure-immutable-muttable-tools](https://www.linkedin.com/pulse/history-infrastructure-immutable-mutable-tools-achieve-amit-kalia)

[hasicorp-immutable-infrastructure](https://www.hashicorp.com/resources/what-is-mutable-vs-immutable-infrastructure)

[what is immutable infrastructure](https://www.digitalocean.com/community/tutorials/what-is-immutable-infrastructure)

[snowflake server](https://martinfowler.com/bliki/SnowflakeServer.html)