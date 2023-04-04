## Persistent Storage in Docker

Docker를 사용할 때 컨테이너는 일시적으로 설계되어 쉽게 생성, 파괴 및 교체할 수 있다. \
그러나 컨테이너 내에서 생성되거나 수정된 데이터는 컨테이너가 파괴되거나 교체되면 손실될 수 가 있음. \
이 문제를 해결하기 위해 Docker는 영속성 있는 저장소 솔루션을 제공함으로서 컨테이너 생명주기 이벤트를 넘어 데이터가 계속 유지되도록 함.

Docker에서 영속성 있는 저장소는 `Docker 볼륨`, `바인드 마운트` 및 `tmpfs 마운트`를 사용하여 달성할 수 있음.

이번엔 Docker 볼륨에 중점을 두고 살펴봄.

<br>

## Docker Volume Concepts

Docker 볼륨은 Docker ecosystem에서 관리되는 객체로, 저장소 기술을 추상화하여 컨테이너 간의 원활한 데이터 액세스를 가능하게 함.

볼륨은 Docker command이나 API 호출을 사용하여 created, listed, inspected, 그리고 removed 할 수 있음.

Docker 볼륨에 대한 3가지 포인트.

1.  `Volume Drivers` \
   Docker는 볼륨 드라이버를 사용하여 실제 저장소 백엔드를 처리하며, 이를 통해 볼륨이 호스트의 파일 시스템 또는 NFS 또는 클라우드 기반 저장소 서비스와 같은 원격 저장소 시스템에 저장될 수 있음. \
   기본적으로 Docker는 호스트 파일 시스템에 볼륨을 저장하는 `local` driver를 사용함.

2.  `Volume Plugins` \
   플러그인은 additional volume drivers을 제공하여 Docker의 기능을 extend 해줌. \
    이를 통해 Amazon EBS, Google Cloud Storage 또는 Ceph와 같은 타사 저장소 솔루션을 Docker 볼륨과 함께 사용할 수 있음.
    
3.  `Volume Scopes` \
   볼륨은 로컬 또는 글로벌 범위를 가질 수 있음. 
   로컬 볼륨은 동일한 호스트에서 실행되는 컨테이너에만 액세스할 수 있고, 글로벌 볼륨은 Docker Swarm 클러스터의 모든 컨테이너에서 사용할 수 있음.
       

<br>

## Docker Volume Management

Docker Volume을 관리하기 위한 기본적인 명령어는 다음과 같음.

Volume 생성하기

```bash
docker volume create <볼륨_이름>
```
`
Volume 목록 보기

```bash
docker volume ls
```

Volume 검사하기

```bash
docker volume inspect <볼륨_이름>
```

Volume 제거하기

```bash
docker volume rm <볼륨_이름>
```

<br>

### 컨테이너와 함께 Volume 생성 및 사용하기

컨테이너와 함께 Volume을 생성하고 사용하려면, 컨테이너를 실행할 때 `-v` 또는 `--mount` 플래그를 사용할 수 있음.

```bash
docker run -d --name my_container -v my_volume:/app/data my_image
```

이 명령은 `my_volume`라는 이름의 볼륨을 생성하고 컨테이너 내부의 `/app/data` 디렉토리에 마운트함.

`my_volume`이 이미 존재하는 경우, 기존 데이터가 컨테이너에 마운트됨.

<br>

### Sharing Volumes Between Containers

Docker 볼륨은 여러 컨테이너 간에 공유될 수 있어 애플리케이션의 다른 인스턴스 간 data persistence 및 synchronization를 가능하게 함.
볼륨을 공유하려면, 여러 컨테이너를 실행할 때 동일한 볼륨 이름을 사용하기만 하면 됨.


```bash
docker run -d --name container1 -v my_shared_volume:/shared/data my_image
docker run -d --name container2 -v my_shared_volume:/shared/data my_image
```


### Tip For Docker Volume


1.   `Volume Plugins을 사용하기` \
   원격 또는 분산 스토리지 솔루션을 사용할 때는 볼륨 플러그인을 사용하여 볼륨 관리를 단순화하고 Docker와 통합하는게 좋음.
    
2.   `Volume Backup` \ 
   Docker 볼륨을 정기적으로 백업하여 데이터 손실을 방지해야함. \
    `docker cp`와 같은 네이티브 Docker 명령 또는 타사 도구를 사용하여 볼륨의 백업을 생성할 수 있음.
    
3.   `볼륨 사용량 모니터링` \ 
   볼륨과 해당 사용량을 추적하여 스토리지 용량 문제를 방지해야 함. \
   Docker 명령 또는 모니터링 도구를 사용하여 볼륨 사용량을 체크.
    
4.   `바인드 마운트 사용 지양` \ 
   바인드 마운트는 호스트 파일 시스템 권한과 보안 문제를 일으킬 수 있음. \
   Docker Volume을 사용하는게 이식성과 격리면에서 이점이 있다.
