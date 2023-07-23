Big O 표기법은 알고리즘 성능을 평가하는 효율적인 방법. 

여기서는 시간에 주로 초점을 맞추며, 알고리즘이 작업을 완료하는 데 걸리는 시간이 더 빠를수록 더 효율적.  

Big O 표기법은 입력 크기가 알고리즘의 실행 시간에 어떻게 영향을 미치는지 분석하는 데 도움이 됨. 

### 상수 시간 알고리즘 – O(1) 

상수 시간 알고리즘은 가장 빠름. 

```java
public void printConstantTime() {
    int n = 10000;
    System.out.println(n);
}
```

이 코드는 n의 값에 관계없이 고정된 시간 내에 실행되며, 이 알고리즘의 시간 복잡도는 O(1)임.

### 로그 시간 알고리즘 – O(log(n)) 

그 다음은 로그 시간 복잡도를 가진 알고리즘.

로그 시간 알고리즘의 대표적인 예는 이진 탐색 알고리즘.

```java
public int binarySearch(int arr[], int x) {
    int l = 0, r = arr.length - 1;
    while (l <= r) {
        int m = l + (r - l) / 2;

        if (arr[m] == x)
            return m;

        if (arr[m] < x)
            l = m + 1;

        else
            r = m - 1;
    }

    return -1;
}
```

### 선형 시간 알고리즘 – O(n) 

입력 크기에 비례하는 시간 복잡도를 가지는 선형 시간 알고리즘.

```java
public void printLinearTime(int n) {
    for (int i = 1; i <= n; i++) {
        System.out.println(i);
    }
}
```

### N log N 시간 알고리즘 – O(n log n)

알고리즘은 선형 시간 복잡도를 가진 알고리즘보다 성능이 떨어짐. 

이는 실행 시간이 입력 크기에 따라 선형적으로 늘어나고 로그를 따라 늘어남.

```java
public void printNLogN(int n) {
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= Math.log(n); j++) {
            System.out.println(i + "," + j);
        }
    }
}
```

### 다항 시간 알고리즘 – O(n^m) 

O(n^2), O(n^3) 및 일반적으로 O(n^m)과 같은 복잡도를 가지는 알고리즘을 포함.

```java
public void printPolynomialTime(int n) {
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            System.out.println(i + "," + j);
        }
    }
}
```


### 지수 시간 알고리즘 - O(k^n) 

지수 시간 알고리즘은 O(2^n) 또는 일반적으로 O(k^n)와 같은 실행 시간을 가짐.

이러한 알고리즘은 보통 무작위 탐색 알고리즘, 피보나치 수열 생성, 행렬곱셈, 모든 부분집합 생성 등에서 찾아볼 수 있음. 

이러한 알고리즘은 입력 크기가 증가함에 따라 빠르게 비효율적이 되므로, 입력 크기가 큰 문제에는 적합하지 않음.

피보나치 수열을 생성하는 단순한 재귀 알고리즘은 지수 시간 복잡성을 가진 좋은 예.

```java
public int fibonacci(int n) {
    if (n <= 1) return n;
    else return fibonacci(n-1) + fibonacci(n-2);
}
```

자신의 값과 함께 두 개의 추가적인 함수 호출을 생성. 

이 경우, 재귀 호출 트리는 매우 빠르게 확장되므로, 이 알고리즘의 실행 시간은 O(2^n)이 됨.

### 팩토리얼 시간 알고리즘 – O(n!)

이 알고리즘 클래스는 실행 시간이 입력 크기의 팩토리얼에 비례하여 증가. 

예제는 각 팩토리얼 수의 반복에서 0에서 n!까지의 모든 숫자를 출력하는 간단한 O(n!) 알고리즘.

```java
public void printFactorialTime(int n) {
    long factorial = 1;
    for (int i = 1; i <= n; i++) {
        factorial *= i;
        for (int j = 0; j < factorial; j++) {
            System.out.println(j);
        }
    }
}
```

### 각 Big O 시간 복잡도 시각화 표

![bigoGraph](https://github.com/christopher3810/TIL/assets/61622657/0528dd89-ba9e-44b8-b867-6911ee02937e)