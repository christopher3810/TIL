***

이미지가 컨테이너에 들어가면 컨테이너가 시작되고 격리된 환경에서 애플리케이션이 실행됨.\ 
컨테이너는 Linux 네임스페이스, cgroups 및 UnionFS 파일 시스템의 조합을 사용하여 격리됨.

### 컨테이너 격리

docker는 linux 와 kernel을 공유하면서 guest os가없이 가볍게 동작할 수 있는데 linux kernel을 활용해서 효율적으로 컨테이너를 격리 시킴.

추후에 linux쪽에서 아래 커널에 대해서 자세하게 작성후 링크. 

`Linux namespaces`
프로세스 격리를 제공하여 컨테이너가 별도의 프로세스 트리, 네트워크 인터페이스, 사용자 ID 및 파일 시스템을 갖도록 함.

`Control groups (cgroups)`
리소스 할당을 관리하여 컨테이너가 CPU, 메모리, I/O에 대한 자체 제한과 할당량을 갖도록 함.

`UnionFS file systems`
컨테이너 레이어를 서로 겹쳐서 쌓을 수 있도록 허용하여 각 컨테이너에 대해 격리된 파일 시스템 보기를 제공.

### Default Network And Docker0

기본적으로 컨테이너가 시작되면 "bridge"라는 네트워크에 연결됨. \ 
이 네트워크는 Docker에서 생성하는 기본 네트워크 유형으로, 컨테이너와 호스트 시스템 간에 트래픽을 라우팅하는 가상 네트워크임.

![docker0](https://user-images.githubusercontent.com/61622657/226511018-31a2d495-108d-49a6-915d-ad340d4e9091.png)
출처:https://stackoverflow.com/questions/37536687/what-is-the-relation-between-docker0-and-eth0

기본 브리지 네트워크가 설정되는 방법은 다음과 같음.

Docker가 설치되고 Docker 데몬이 시작되면 호스트 시스템에 "docker0"이라는 기본 브리지 네트워크가 자동으로 만들어짐. \
이 네트워크에는 IP 주소 범위(일반적으로 172.17.0.0/16과 같은)가 할당됨. \
브리지 네트워크에 연결된 각 컨테이너는 이 범위에서 자체 IP 주소를 할당받으며, 이 IP 주소를 사용하여 다른 컨테이너 및 호스트 시스템과 통신할 수 있음.

 `docker network` command를 활용하여 docker 내 network를 관리할 수 있음.
예를들어 아래와 같이 새로운  "my_bridge_network" 이름의 new bridge network 를 만들 수 있다.

```bash
docker network create --driver bridge my_bridge_network
```

container가 실행될 때 이 네트워크에 연결하려면 `--network` flag 를 사용하면 됨.

```bash
docker run --name my_container --network my_bridge_network my_image
```

### Container 라이프 사이클

컨테이너가 시작되고 실행되면 다양한 Docker 명령을 사용하여 컨테이너와 상호 작용할 수 있음.

![containerlifecycle](https://user-images.githubusercontent.com/61622657/226511264-7071cf27-9d04-4ce9-b21d-d896cb4c61af.png)


-   `docker ps`: 실행 중인 컨테이너와 기본 정보를 나열.
-   `docker stop <container_id>`: running 중인 container stop
-   `docker start <container_id>`: stopped된 container start
-   `docker restart <container_id>`: Restarts a container.
-   `docker pause <container_name_or_id>` : Running 중인 Container Process Suspend
-   `docker unpause <container_name_or_id>` : pause 되었던 Container 내에 있는 Process 중단 시점부터 다시 시작
-   `docker logs <container_id>`: Shows logs from the container.
-   `docker exec -it <container_id> <command>`: container 내부에서 명령어 실행
  `docker exec -it my_container bash` -> Container 내부에서 bashshell 실행
-   `docker rm <container_id>`: Removes a container (container stopped 상태여아 함.).

컨테이너가 중지되면 해당 프로세스가 종료되고 더 이상 CPU 또는 메모리 리소스를 소비하지 않음. \
그러나 컨테이너의 파일 시스템과 런타임 중에 변경된 내용은 보존됨. \
컨테이너를 다시 시작하면 컨테이너가 중지된 시점의 상태로 재개됨.

컨테이너와 관련 파일 시스템을 제거하려면 `docker rm` 명령을 사용하면 됨. \ 
컨테이너를 제거하면 해당 파일 시스템이 영구적으로 삭제되며 런타임 중에 변경한 내용은 모두 손실된다는 점에 유의해야함. 
