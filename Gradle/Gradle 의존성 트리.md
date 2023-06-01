
## 의존성 트리 보기

Gradle에서는 `dependencies` 태스크를 통해 프로젝트의 의존성 트리를 확인할  수 있음

intellij 에서 `Execute Gradle Task`에서 하위 Task를 실행시키면 가능.

```groovy
gradle :dependencies
```


```terminal
(c) - dependency constraint
(*) - dependencies omitted (listed previously)
```

`(c)`는 의존성 제약이 걸려있는 상태를 의미.

gradle이 알아서 결정하지 못하도록 명시되어 있는 경우.

`(\*)`은 이전에 트리에서 이미 하위구조를 보여주었기 때문에 생략되었음을 의미.
 
예시

```terminal
+--- ar.com.fdvs:DynamicJasper:5.3.1
|    +--- net.sf.jasperreports:jasperreports:[6.8.0, 6.13.0] -> 6.13.0
|    |    +--- commons-beanutils:commons-beanutils:1.9.4 -> 1.7.0 (*)
```

위 JasperReport에서 beanutils 1.9.4 를 사용하지만 

build.gradle에서 1.7.0 beantuils 를 사용하도록 명시 했기 때문에 `(*)` Gradle이 의존성을 알아서 처리할 수 없도록 Constraint 되어있는 것이고 결국 1.7.0 버전으로 `->` 대체가 된 것을 의미.

## 의존성 항목 외부로 추출 하기

FIle로 외부로 받아서 보고 처리하는게 훨씬 편하기 때문에 외부 로 추출하는 것을 권장

1. intellij Terminal open
2. 파일을 담을 Folder 생성

```bash
 mkdir dependencyFile
```

3. `dependency.tree` 라는 이름의 파일로 외부로 내보내는 명령어 입력

```groovy
./gradlew :dependencies --configuration compileClasspath > dependency.tree
```

intellij 내부에서는 root 가 이미 잡혀있기 때문에 :dependencies 앞에 root project 명칭을 따로 주지 않아도 된다.

```
./gradlew :dependencies --configuration compileClasspath > dependency.tree
```


## 의존성 문제 해결

### 전이 의존성 컨트롤하기 `upgrade`

특정 라이브러리의 전이 의존성 버전을 업그레이드하려면, `constraints` 블록을 사용하여 해당 의존성을 명시적으로 선언할 수 있음.

```groovy
dependencies {                                                                                  
    implementation 'B:1.0.0'
    constraints {
        implementation('C:1.2.0') {
            because 'previous versions have a bug impacting this application'
        }
    }
}

```

### 전이 의존성 제외하기 `exclude`

특정 라이브러리에서 특정 모듈이나 그룹을 제외하려면, `exclude` 키워드를 사용하여 그룹이나 모듈을 지정할 수 있음.

```groovy
configurations {
    all {
        exclude group: 'commons-beanutils', module: 'commons-beanutils'
    }
}
```

configurations 블록 사용해서 의존성 전체에 대해서 글로벌 하게 사용할 수 있음.

## 의존성 충돌 해결

프로젝트의 다양한 모듈과 라이브러리가 동일한 라이브러리의 서로 다른 버전을 참조하는 경우, 의존성 충돌이 발생할 수 있음. 

Gradle은 이런 문제를 해결하기 위해 `resolutionStrategy`를 제공.

```groovy
configurations.all {
    resolutionStrategy {
        failOnVersionConflict() // 버전 충돌 시 빌드 실패
        force 'com.google.guava:guava:27.1-jre' // 특정 버전 강제 지정
    }
}
```
