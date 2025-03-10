runc와 CRI-O에 본격적으로 집중하기 전에 컨테이너 런타임의 역사적 기원에 대한 내용을 먼저 다룬다.

[이전 파트](Kernel Space)에서는 Linux Kernel Namespace와 격리 기술에 대해 설명하였다.
이번에는 컨테이너는 어떻게 실제로 실행되는가에 집중한다.

# A Brief History
2008년, 컨테이너 기술 업계를 뒤집어엎는 Linux Containers(LXC)라는 프로젝트가 시작된다.
`cgroup`과 namespace를 결합하여 애플리케이션을 실행할 수 있는 격리된 환경을 제공한다.
반면 구글은 2007년부터 cgroup 등의 세부 기반 지식 없이도 안정적, API-driven 설정을 가능하게 하는 *Let me Contain That For You*(LMCTFY)라는 컨테이너화 프로젝트를 시작했다.

2013년, LXC에 기반한 Docker는 컨테이너를 이미지로 만들어서 다른 머신에 넘길 수 있게 하였다.
Docker는 [Standard Container Manifesto](https://github.com/moby/moby/blob/0db56e6c519b19ec16c6fbd12e3cee7dfa6018c5/README.md)를 통해 처음으로 컨테이너가 소프트웨어의 표준 기본 단위로 제시하였다.

수 년 뒤, docker는 libcontainer 기반이 되었고, LMCTFY 역시 중단되고 중심 개념과 이점만 libcontainer와 Docker로 포팅된다.

2015년에는 쿠버네티스 1.0 릴리즈, CNCF(당시 Linux Foundation 산하), OCI의 출범 등 많은 일이 일어난다.
CNCF와 OCI의 목적은 컨테이너 형식과 런타임의 개방형 표준을 작성하는 것이 목적으로, [OCI Runtime Specification](https://github.com/opencontainers/runtime-spec)이라는 결과물이 나온다.
런타임 개발자들은 이로 인하여 잘 정의된 API를 얻는다. 
libcontainer 프로젝트는 OCI에 donate되면서, runc라는 새로운 툴이 출시된다.
runc를 통해 libcontainer와 직접 상호작용하고 OCI Runtime Specification을 해석해서 컨테이너를 실행하는 것이 가능해진다.

runc는 컨테이너 생태계에서 가장 널리 쓰이는 프로젝트 중 하나로, containerd(Docker), CRI-O, podman 등의 다양한 프로젝트에서 사용된다.
OCI Runtime Specification 또한 다양한 프로젝트에서 채택되었다.

# Running Containers
# runc
