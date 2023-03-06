***

Gradle이 새 dependency를 다운로드할 때 cache 폴더에 description.bin 파일이 생성됩니다.\
cache -> module -> metadata -> descriptor -> 각 dependency 별 description.bin 생성

description.bin 파일의 생성 시간은 dependency을 마지막으로 다운로드하거나 업데이트한 시간에 해당합니다.\
description.bin 파일이 실제 파일보다 오래된 경우 Gradle은 캐시된 버전이 오래된 것으로 간주하고 dependency를 다시 다운로드합니다.\
이 파일에는 프로젝트의 작업, 속성 및 dependency에 대한 정보가 포함된 프로젝트 설명의 바이너리 직렬화가 포함되어 있습니다.

### gradle reload 와 description.bin

Gradle은 reload할 때 .gradle 하위 경로의 cache 폴더에서cache된 버전의 dependency를 우선적으로 가져옵니다. \
.gradle 디렉터리에는 이전 빌드 중에 다운로드한 모든 dependency의 cache가 포함되어 있습니다. 
새 빌드를 실행할 때 Gradle은 필요한 dependency가 이미 cache에 있는지 확인합니다. \
이미 있는 경우 다시 다운로드하는 대신 cache된 버전을 사용합니다.\
이렇게 하면 특히 dependency가 많은 프로젝트에서 작업하는 경우 많은 시간을 절약할 수 있습니다.

description.bin 파일의 메타데이터는 Gradle에서 dependency가 이미 캐시에 있는지 확인하고 캐시된 파일의 무결성을 확인하는 데 사용됩니다.\
description.bin 파일의 메타데이터가 실제 파일의 메타데이터와 일치하지 않으면 Gradle은 파일이 손상된 것으로 간주하고 다시 다운로드합니다.


### reload 예시

여러 종속 요소가 있는 Gradle 프로젝트가 있고 이전에 이미 한 번 빌드한 적이 있다고 가정한다면,
초기 빌드 중에 Gradle은 필요한 모든 dependency를 다운로드하여 .gradle 캐시 폴더에 저장했습니다.

Gradle에서 프로젝트를 realod하면 먼저 cache된 각 종속 요소에 대한 description.bin 파일을 확인합니다. \
Gradle은 이 메타데이터를 사용하여 캐시된 dependency의 버전이 여전히 유효하고 최신 상태인지 확인합니다.

description.bin 파일의 메타데이터가 실제 dependency 파일의 메타데이터와 일치하면 Gradle은 dependency가 여전히 유효한 것으로 간주하고 캐시된 dependency 버전을 사용합니다. \
이렇게 하면 Gradle이 dependency들을 다시 다운로드할 필요가 없으므로 빌드 프로세스의 속도가 크게 빨라질 수 있습니다.

그러나 description.bin 파일의 메타데이터가 실제 dependency 파일의 메타데이터와 일치하지 않는 경우 Gradle은 캐시된 dependency 버전이 손상되었거나 오래된 것으로 간주합니다.\
이 경우 Gradle은 원격 리포지토리에서 종속 요소를 다시 다운로드하여 .gradle 캐시 폴더에 저장합니다.

description.bin 파일을 사용하여 캐시된 dependency의 무결성을 확인하면 Gradle은 프로젝트가 모든 필수 dependency의 올바른 버전을 사용하여 빌드되도록 할 수 있습니다. \
이렇게 하면 빌드 오류를 방지하고 빌드되는 머신이나 환경에 관계없이 프로젝트가 일관되게 빌드되도록 보장할 수 있습니다.


### 참조
https://docs.gradle.org/current/userguide/directory_layout.html
https://docs.gradle.org/current/userguide/working_with_files.html