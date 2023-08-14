### 문제 상황
---

최근 Minikube를 사용하여 로컬에서 Kubernetes 클러스터를 설정하고 실행하던 중 다음과 같은 에러가 발생함.

```bash
christopher@Christopherui-MacBookAir ~ % minikube status
E0814 15:06:19.691239    1440 status.go:413] kubeconfig endpoint: got: 127.0.0.1:32784, want: 127.0.0.1:32774
minikube
type: Control Plane
host: Running
kubelet: Stopped
apiserver: Stopped
kubeconfig: Misconfigured
WARNING: Your kubectl is pointing to stale minikube-vm.

To fix the kubectl context, run `minikube update-context`
```

### 문제 분석
---

**kubeconfig endpoint mismatch**

`kubeconfig endpoint: got: 127.0.0.1:32784, want: 127.0.0.1:32774` 이 부분을 보면 Minikube의 현재 설정된 endpoint와 kubeconfig에 저장된 endpoint가 일치하지 않음을 나타냄.

**Stale kubectl context**: kubectl이 현재 Minikube의 VM을 가리키고 있지 않음.

특히, Minikube를 Docker 드라이버로 실행할 때 이러한 현상이 자주 발생하는 것 같은데 이는 Docker의 IP 및 포트 할당 메커니즘과 `kubeconfig`의 자동 업데이트 메커니즘이 완벽하게 동기화되지 않기 때문.

### 해결 방안
---

위의 문제를 해결하기 위한 몇 가지 접근 방법은 생각보다 간단함.

**1. Minikube context 업데이트**

Minikube의 context를 업데이트하면 `kubeconfig`와 Minikube 사이의 불일치 문제를 해결할 수 있음.

```bash
minikube update-context
```

**2. Minikube 재시작**

Minikube 클러스터를 완전히 중지한 후 다시 시작 ip 할당이 일치해서 해결 될 수있음..

```bash
minikube stop
minikube start
```

**3. kubeconfig 확인**

Minikube가 `kubeconfig`를 올바르게 설정했는지 확인하는 방식으로 해결 할 수 있음.

```bash
kubectl config view
```

