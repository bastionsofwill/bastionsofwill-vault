https://medium.com/@saschagrunert/demystifying-containers-part-i-kernel-space-2c53d6979504

# Introduction
컨테이너를 'a standard unit of software'^[https://www.docker.com/resources/what-container/] 와 같이 설명하는 것은 전체적인 관점에서 파악하는 데 도움을 주지만, 그 기반을 보여주지는 못한다.
따라서 사람들은 컨테이너를 저렴한 VM 정도로 받아들이게 되는데, 이는 '컨테이너'나 '파드'와 같은 용어가 실제로 어떤 뜻을 가지지 않는 단어이기 때문이다.

컨테이너는 **'기본적인'기능 집합을 충족하면서 단일 호스트에서 실행되는, 고립된 프로세스들의 그룹**이다. 이 중 일부는 Linux 커널에 포함되어 있지만, 대부분 각자 서로 다른 여러 역사적 유래를 가진다.

이를 좀 더 받아들이는 수준대로 분해하여 살펴보면, 아래와 같이 나눠볼 수 있다.
1. 반론의 여지 없음: '단일 호스트에서 실행'. 둘 이상의 컴퓨터가 하나의 컨테이너를 실행할 수 없다.
2. 명확함: '프로세스들의 그룹'. Linux 프로세스들은 tree 구조이므로, container들은 반드시 root 프로세스를 가진다 할 수 있다.
3. 받아들일 수 있음: '고립된'. 고립이라는게 정확히 무엇이던간에 일단 고립된 상태여야 한다.
4. 애매함: '기본적인 기능 집합의 충족'. '기본적인' 기능이라는 것은 일반적으로 바뀌기 때문에, 가장 기본적인 기능이 무엇인가에 대한 논의가 필요하다.
아직은 이것이 와닿지 않고 혼란스러우므로, 역사적 맥락으로부터 시작하는 것이 도움이 된다.

# chroot
프로세스는 모두 각자의 root 디렉토리를 가지며, 이를 기준으로 다른 파일들을 탐색한다.
일반적인 프로세스들은  파일 시스템의 `/`를 root 디렉토리로 가지나, `chroot`을 사용하면 프로세스의 root 디렉토리를 변경할 수 있다.
이렇게 상위 디렉토리와 독립된 환경에서 프로세스가 실행된다는 특징 때문에 감옥(jail)이라고도 불리지만, 재차 chroot을 사용하여 root 디렉토리를 재변경할 수 있고, 권한이 있는 사용자라면 상대 경로로 접근 시 변경된 root의 상위 경로에도 접근이 가능하므로 진정한 의미의 격리라 보기는 어렵다.

# Linux Namespaces
Namespace의 기반이 되는 발상은 전역 시스템 자원을 추상화 레이어로 감싸는 것이다.
이를 통해 프로세스는 각각 자신만의 고유한 자원의 인스턴스를 가지게 된다.
프로세스 그룹마다 시스템을 다른 관점(view)로 보도록 하는 것이다.

이를 위한 업데이트가 거듭되어, 현재 Linux에는 아래와 같이 7개의 namespace가 존재한다.
(이외에도 time과 syslog라는  namespace가 2016년에 제안되었으나, 아직 완벽하게 구현되지는 않았다.)
- mnt(Mount)
- pid(Process ID)
- net(Network)
- ipc(Interprocess Communication)
- uts(UNIX Time-sharing System)
- user(User ID)
- cgroup(Control Group)

## API
namespace API는 `clone`, `unshare`, `setns` 3개의 system call로 구성된다.
`clone` API 함수는 `fork`와 유사한 방식으로 새로운 자식 프로세스를 생성하는데, `fork`와는 다르게 자식 프로세스는 부모 프로세스와 메모리 공간, file descriptor/signal handler table과 같은 execution context의 일부를 공유할 수 있다.
`unshare` 함수는 다른 프로세스와의 execution context 공유를 해제할 수 있다.
마지막으로 `sentns` 함수는 호출 스레드를 namespace file descriptor에 재연결함으로서 기존 namespace에 합류하는데 사용할 수 있다.


# references
https://www.44bits.io/ko/post/change-root-directory-by-using-chroot
https://www.44bits.io/ko/keyword/linux-container
