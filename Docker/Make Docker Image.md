
Docker 이미지는 코드, 런타임, 시스템 도구, 라이브러리, 설정 등 소프트웨어를 실행하는 데 필요한 모든 것을 포함하는 경량의 독립 실행형 소프트웨어 패키지입니다. 도커 이미지는 도커파일이라는 일련의 명령어 집합에서 생성됩니다.

Docker Image 생성 과정에 대해서 자세히 확인해 보겠습니다.

1.  Base Image
     Base Image는 Docker 이미지를 만들기 위한 시작점입니다. \
     Base Image는 특정 소프트웨어 패키지가 설치된 최소한의 pre-configured os입니다. \
     Base Image는 소프트웨어 공급업체에서 제공하는 official 이미지 또는 커뮤니티에서 유지 관리하는 이미지일 수 있습니다. \
     예를 들어 우분투, 알파인 또는 Node.js Base Image가 있습니다. \
     Base Image는 **`FROM`** 명령어를 사용하여 Dockerfile에 지정합니다.
   
2.  Layers
   Docker파일의 각 명령어는 이미지에 새 레이어를 생성합니다. \
   레이어는 서로 겹쳐서 쌓이고 각 레이어는 이전 레이어와의 차이점을 나타냅니다. \
   이러한 계층 구조는 변경된 레이어만 전송하거나 저장하면 되기 때문에 이미지를 효율적으로 저장하고 공유할 수 있습니다. \
   즉 Image를 통째로 생성하지 않고 부모 이미지를 계속 참조하는 방식으로 레이어를 쌓아 나가는 식으로 생성합니다.

![docker-layers](https://user-images.githubusercontent.com/61622657/226214955-cd82e229-7c3e-4f21-ba7b-6c2890c43cb3.png)
출처:[Coffee Chat: Layers inside Docker Image! | Sachin Jha (sachcode.com)](https://sachcode.com/tech/docker-images-layers-teatime/)

3.  Dependencies
   Dependencies는 애플리케이션을 실행하는 데 필요한 필수 라이브러리, 프레임워크 및 기타 소프트웨어 구성 요소입니다. \
   Dependencies는 일반적으로 **`apt`**, **`yum`** 또는 **`npm`**과 같은 패키지 관리자를 사용하여 기본 이미지 위에 설치됩니다. \
   설치된 각 종속 요소는 Docker 이미지에 새 레이어를 생성합니다. \
   Dependencies이 업데이트되면 변경된 종속성이 있는 레이어만 업데이트하면 되므로, 저장 및 배포 측면에서 Docker 이미지가 효율적인 이유 중 하나입니다.
   
4.  Hexadecimal IDs
  Docker 이미지의 각 레이어는 고유한 16진수 ID로 식별됩니다. \ 
  이 ID는 레이어의 콘텐츠와 메타데이터의 해시입니다. \ 
  이미지를 가져오거나 내보낼 때 Docker는 이러한 ID를 사용하여 어떤 레이어를 전송해야 하는지 결정합니다. \
  이렇게 하면 필요한 레이어만 전송되므로 전송되는 데이터의 양이 줄어들고 프로세스 속도가 빨라집니다.
   
5.  Creating a Docker image
  Docker 이미지를 만들려면 이미지 빌드에 대한 일련의 지침이 포함된 Docker파일을 작성해야 합니다. Docker파일은 기본 이미지, 종속성, 구성 설정은 물론 애플리케이션 코드를 이미지에 추가하는 방법도 지정합니다. \
   Docker파일이 준비되면 **`docker build`** 명령을 사용하여 이미지를 빌드할 수 있습니다. \
   Docker는 Docker파일의 명령어를 읽고 순서대로 실행하여 각 명령어에 대해 새 레이어를 생성합니다.
   
6.  Managing Docker images
   Docker 이미지는 Docker CLI 또는 Docker 데스크톱과 같은 GUI 도구를 사용하여 관리할 수 있습니다.
   몇 가지 일반적인 관리 작업은 다음과 같습니다.
   
-   Building images: **`docker build -t your-image-name .`**
-   Listing images: **`docker images`**
-   Removing images: **`docker rmi your-image-name`**
-   Tagging images: **`docker tag your-image-name your-repo/your-image-name:your-tag`**
-   Pushing images to a registry: **`docker push your-repo/your-image-name:your-tag`**
-   Pulling images from a registry: **`docker pull your-repo/your-image-name:your-tag`**