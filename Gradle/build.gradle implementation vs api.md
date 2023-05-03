
## Implementation

`implementation`은 Gradle의 기본 종속성 구성입니다. 이는 모듈이 컴파일 시 해당 종속성을 사용하도록 지정. 

그러나 `implementation`을 사용하면 종속성이 다른 모듈로 누출되지 않음. 

따라서 다른 모듈이 해당 종속성을 사용하려면 각 모듈에 독립적으로 종속성을 추가해야 함.

`implementation` 예제

```groovy
dependencies {
    implementation 'com.google.guava:guava:30.1-jre'
}

```

`implementation`을 사용하면 컴파일 시간이 더 짧아지고 빌드 성능이 향상됨. 
또한 이는 다른 모듈에 영향을 주지 않기 때문에 내부 구현 변경에 대한 위험을 줄임.

## API

`api` 구성은 `implementation`과는 다르게 종속성을 다른 모듈로 누출시킴. 

이는 모듈 간의 공유 인터페이스에 사용됨.

`api` 구성을 사용하면 다른 모듈은 명시적으로 종속성을 추가하지 않고도 해당 종속성을 사용할 수 있음.

`api` 예시

```groovy
dependencies {
    api 'com.google.guava:guava:30.1-jre'
}
```

`api` 구성을 사용할 때는 공개 API에 영향을 주는 변경이 발생할 수 있으므로 주의해야 함. 

이로 인해 다른 모듈에 대한 충돌이나 호환성 문제가 발생할 수 있음.

### 권장 사항.

-   `implementation`
  모듈 내부에서만 종속성이 사용되는 경우 `implementation`을 사용합니다. 이렇게 하면 빌드 성능이 향상되고, 다른 모듈에 영향을 주지 않음.
  
-   `api`
  모듈 간의 공유 인터페이스 또는 공개 API에서 종속성이 필요한 경우 `api`를 사용. 
  이렇게 하면 다른 모듈이 명시적으로 종속성을 추가하지 않고도 해당 종속성을 사용할 수 있음.
