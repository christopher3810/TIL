
```gradle
gradle build --refresh-dependencies
```


gradle build --refresh-dependencies 옵션을 사용하면, Gradle은 빌드를 시작하기 전에 프로젝트의 dependency를 업데이트합니다. \
이를 통해 프로젝트에 새로운 dependency이나 업데이트된 dependency가 있는지 확인하고, 필요한 경우 해당 dependency를 다운로드합니다.

이 옵션을 사용하면, build.gradle 파일이나 전역 Gradle 구성에서 지정된 저장소(repository)에서 프로젝트의 dependency를 확인합니다. \
새로운 dependency 업데이트된 dependency을 발견하면 다운로드합니다. 업데이트된 dependency는 기본적으로 홈 디렉토리의 .gradle 디렉토리에 있는 Gradle 캐시에 저장됩니다.

Gradle은 기본적으로 다운로드한 dependency를 캐시해두어, 향후 빌드 시 속도를 높이도록 합니다.

하지만 프로젝트의 dependency가 변경된 경우, 최신 버전의 dependency을 사용하고 있는지 확인하기 위해 --refresh-dependencies 옵션을 사용해야 할 수 있습니다.

