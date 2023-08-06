
### Helm의 중요성 및 쿠버네티스 메니페스트 대비 장점

쿠버네티스는 마이크로서비스 아키텍처를 지원하는 강력한 플랫폼이지만, 애플리케이션의 배포 및 관리가 복잡할 수 있음. 

이러한 복잡성을 감소시키기 위해 Helm이라는 패키지 매니저가 개발됨.

Helm은 쿠버네티스 애플리케이션을 정의, 저장, 관리하는 도구로서, 쿠버네티스 메니페스트를 보다 효율적으로 관리하는 데 도움을 줌

**장점**

1. **재사용성**"
   Helm 차트는 패키지로서의 재사용성을 제공. \ 
   한 번 만든 차트를 다른 환경이나 프로젝트에서 재사용할 수 있음.

2. **버전 관리**
   Helm은 차트의 버전 관리를 지원. \
   이를 통해 특정 버전의 애플리케이션을 쉽게 롤백하거나 업그레이드 할 수 있음.

3. **의존성 관리**
   Helm은 차트의 의존성을 관리함. \
   이는 복잡한 애플리케이션에서 여러 차트가 함께 작동해야 할 때 유용.

4. **템플릿화**
   Helm은 Go 템플릿을 지원하여 동적으로 리소스를 생성할 수 있게 해줌. \
   이를 통해 환경별 구성 변경 등을 편리하게 수행할 수 있음.

5. **배포 관리**
   Helm을 통해 쿠버네티스 애플리케이션의 배포 과정을 간소화하고 자동화 할 수 있음.

**M1 환경에서 Helm 설치**

Homebrew를 통해 간단하게 수행할 수 있음.

```bash
brew install helm
```

### Helm 차트 폴더 구조 및 역할

Helm으로 새로운 프로젝트를 생성(`helm create`)하면, 아래와 같은 폴더 및 파일 구조가 생성.

`Chart.yaml`

차트의 기본 정보를 포함하고 있습니다. 차트의 이름, 버전, 설명 등을 포함.

`values.yaml`

차트에서 사용하는 기본 설정값들을 정의.

이 파일에서 정의한 값들은 템플릿 파일들에서 사용될 수 있음.

`charts/`

이 폴더에는 이 차트가 의존하는 다른 차트들을 포함할 수 있음.

`templates/`

쿠버네티스 리소스 정의(YAML 파일)를 가지고 있는 폴더입니다. 이 폴더의 파일들은 `values.yaml` 파일의 값을 이용해 템플릿화됨.

`templates/NOTES.txt`

Helm 설치 후 출력되는 메시지를 담고 있는 파일.

`.helmignore`

`.gitignore`와 비슷한 역할.

### values.yaml을 이용한 PostgreSQL 설정 예시

`values.yaml` 파일 내에 PostgreSQL 설정을 하는 간략한 예시


```yaml
postgresql:
  image:
    repository: postgres
    tag: latest
  service:
    port: 5432
  persistence:
    enabled: true
    size: 1Gi
  postgresqlUsername: postgres
  postgresqlPassword: postgres_password

```

이 설정은 PostgreSQL의 도커 이미지, 서비스 포트, 사용자 이름, 비밀번호, 그리고 영속성 설정 등을 정의. 

영속성을 사용하도록 설정하면, PostgreSQL 데이터가 파드가 삭제되어도 유지됨.

### Helm을 이용한 배포

Helm을 이용한 배포는 `helm install` 명령을 통해 이루어짐. 

예를 들어, 위에서 생성한 차트를 `my-chart`라는 이름으로 배포하려면 다음과 같은 명령을 사용.

```bash
helm install my-chart ./my-chart`
```


여기서 `./my-chart`는 차트의 위치를 가리킴. 

이 위치는 로컬 파일 시스템의 경로일 수도 있고, 원격 차트 리포지토리의 URL일 수도 있음.

### Helm 내에서 차트 버전 관리

Helm에서는 차트의 버전 관리가 가능함. 

이는 `Chart.yaml` 파일 내의 `version` 항목을 통해 설정할 수 있음. 

Helm은 이 버전 정보를 이용해 특정 버전의 차트를 설치하거나, 차트의 업데이트를 관리함.

차트를 업데이트하려면, 먼저 `Chart.yaml`의 `version`을 업데이트한 후에, `helm upgrade` 명령을 사용. 

예시

```bash
helm upgrade my-chart ./my-chart
```

이전 버전으로 롤백하려면, `helm rollback` 명령을 사용. 

예시

```bash
helm rollback my-chart 1
```

### Helm 설치 시 DNS-1035 레이블 규칙 준수

Helm을 사용하여 쿠버네티스 리소스를 생성할 때, 리소스 이름은 DNS-1035 레이블 규칙을 따라야 함.

>DNS-1035 레이블 규칙에 따르면, 리소스 이름은 소문자 알파벳, 숫자, 또는 하이픈으로 구성되어야 하며, 숫자로 시작할 수 없습니다. 또한, 최대 63자로 제한.

이러한 규칙은 Helm 차트의 `Chart.yaml`에서 `name` 항목으로 설정하거나, `helm install` 명령에서 `--name` 플래그로 설정할 수 있음. 

예시

```bash
helm install --name my-chart ./my-chart
```


### 배포된 내용 확인
---
Minikube Dashboard와 k9s 활용

Helm을 통해 배포한 내용은 Minikube Dashboard나 k9s 등의 도구를 통해 확인할 수 있음.

**Minikube Dashboard**

Minikube에는 기본적으로 Dashboard가 포함되어 있습니다. Minikube Dashboard를 실행하려면 다음의 명령을 사용합니다.

```bash
minikube dashboard
```

이 명령은 웹 브라우저에서 Minikube Dashboard를 열어줌. 

여기서는 배포한 애플리케이션의 상태를 확인하거나, 쿠버네티스 리소스를 직접 생성, 수정, 삭제할 수 있음.

**k9s**

k9s는 터미널 기반의 쿠버네티스 관리 도구.

k9s를 통해 쿠버네티스 클러스터의 상태를 실시간으로 확인하거나, 리소스를 관리할 수 있음.

k9s를 실행하려면, 터미널에서 다음의 명령을 사용.

```bash
k9s
```

이 명령은 k9s UI를 실행. 

여기서는 쿠버네티스 클러스터의 전반적인 상태를 한 눈에 볼 수 있으며, 특정 리소스를 선택하여 상세 정보를 확인하거나, 직접 리소스를 생성, 수정, 삭제할 수 있음.