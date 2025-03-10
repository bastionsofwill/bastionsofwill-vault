Kernel이라는 단어는 사전에서 찾아보면 핵, 중심부, 씨앗 등의 뜻이 있다.
운영체제의 커널 또한 말 그대로 운영체제의 핵심이며, 메모리 관리와 프로세스 스케줄링 같은 운영체제 하면 떠오르는 역할을 담당하는 것이 바로 커널이다.

이렇듯 커널은 운영체제의 근간을 이루는 굉장히 중요한 프로그램이지만, 그만큼 커널의 취약점은 전체 시스템의 보안에 중대한 위협이 될 수 있으므로 꾸준한 관리가 필요하다.

## 커널 업데이트와 업그레이드
업그레이드와 업데이트라는 용어는 항상 나를 헷갈리게 했다.
심지어 update/upgrade는 명사이자 동사여서, 때로는 '변경' 그 자체를 뜻하기도 하고(보통 'How to check/install Linux kernel updates?' 처럼 복수형으로 쓴다.), 때로는 '변경을 적용하는 행위'를 나타내기도 해서('Update your Linux kernel.') 더 혼란스러웠다.
구별의 필요성을 처음으로 느낀 것은 패키지 관리자를 사용하면서였다.
`sudo apt update`는 패키지 '리포지토리의 정보를' 최신화하는 것이라 자주 해주는 것이 좋지만, `sudo apt upgrade`는 모든 패키지'를' 최신 버전으로 교체해버리는 것으로, 절대 함부로 건드리는 게 아니다... 정도로만 이해하고 있었다.

보다 근본적인 의미상의 차이로는, 업그레이드는 큰 단위의(major) 개선/진보를 뜻하는 반면, 업데이트는 특정 문제점을 해결하기 위한 대응이라는 점이 있다.
따라서 업데이트는 비교적 시급하고, 또 작은 단위의 변경인 경우가 대부분이다.

체감상 보통 업그레이드는 OS의 메이저 버전이 넘어갈 때에 사용하고, 커널 관련 패치 적용은 대부분 업데이트라고 표현하는 경우가 대부분이었다.

## Linux 커널 보안 기능
- **사용자 및 커널 공간 격리:** 사용자 공간과 커널 공간을 분리하여, 사용자 수준의 애플리케이션이 커널 및 기타 프로세스를 손상시키거나 간섭, 접근하지 못하도록 차단한다.
- **메모리 보호:** 각 프로세스는 MMU(Memory Management Unit)에 의해 가상 메모리 영역이 서로 분리되어 유지된다. 할당된 영역에 속하지 않는 메모리에 접근하는 프로세스는 MMU가 segmentation fault로 거부한다.
- **사용자 권한:** Linux는 사용자와 프로세스에 파일 소유권과 권한을 통한 액세스 권한을 제공하는 권한 모델을 구현한다. 이러한 권한은 커널에 의해 적용되어 권한이 없는 사용자 및 그룹은 파일과 폴더에 액세스하지 못한다.
- **ASLR:** ASLR(Address Space Layout Randomization)을 통해 시스템이 부팅될 때마다 실행 중인 프로세스의 메모리 주소 영역을 무작위로 할당하여 [버퍼 오버플로 공격](https://tuxcare.com/ko/blog/memory-corruption-vulnerabilities-in-the-linux-kernel/#linux)을 방지한다. 
- **커널 감사:** auditd와 같은 프로그램을 사용하여 커널 활동을 감사할 수 있는 기능을 제공하여 시스템 변경 사항, 사용자 활동 및 잠재적인 보안 침해 등의 보안 관련 커널 이벤트를 모니터링할 수 있다.
- **보안 부팅:** 부팅 과정에서 커널을 포함하여 서명되고 신뢰할 수 있는 소프트웨어만 실행할 수 있도록 하는 이 기능은 루트킷과 부트킷이 부팅 프로세스를 방해하는 것을 방지하여 이를 방어한다.
- **seccomp:** secure computing 모드는 프로세스가 수행할 수 있는 시스템 호출을 `exit()`, `sigreturn()`,이미 열린 file descriptor에 대한 `read()`,`write()`로 제한함으로써 sandboxing 기능을 제공하고 공격 표면을 크게 줄인다.
- **Linux Security Module:** LSM은 Linux 커널에 추가 보안 모듈을 통합할 수 있는 프레임워크를 제공하여 커널 보안 기능을 향상시키고 관리자에게 액세스 제한 및 정책 구현에 대한 더 많은 제어 기능을 제공한다.

## Ubuntu AWS Rolling Kernel
문제는 내가 주로 사용하는 Amazon EC2 인스턴스의 경우, Ubuntu 인스턴스의 커널 버전이 뭔가 다르다는 점이다. 
```bash
$ uname -r
6.5.0-1018-aws
```
`uname -r`의 결과로 보아 Linux kernel 6.5.0 버전에 기반하여 AWS가 EC2용으로 수정한 커널일 것이라 생각했으나, 6.5버전은 이미 EOL인데 AWS에서 이를 계속 사용하도록 두는 것으로 보아 별개의 커널이 아닌가 짐작하게 되었다.

### Identifying a kernel
자세한 내용은 `/proc/version_signature`를 확인해보고 알 수 있었다.
```bash
$ cat /proc/version_signature 
Ubuntu 6.5.0-1018.18~22.04.1-aws 6.5.13
```
- Ubuntu를 관리하는 Canonical이 “`Ubuntu`”를 붙인다.
- Ubuntu kernel-release = `6.5.0-1018.18~22.04-aws`
	- kernel version is `6.5`, which is identical to upstream stable kernel version
    - `.0` is an obsolete parameter left over from older upstream kernel version naming practices
    - `-1018` application binary interface (ABI) bump for this kernel
    - `.18~22.04` Ubuntu OS 버전 넘버일 것이라 추정... 
    - `.1` upload number for this kernel
    - `-aws` is kernel flavour parameter, where `-generic` is the default Ubuntu kernel flavour
- Mainline kernel-version = `6.5.13`

즉 저 커널은 Canonical이 특별히 AWS를 위해 관리하는 커널이라는 건데, 이게 어떻게 된 일인지 찾아본 결과, 최신의 버그 수정 및 성능 향상을 기대할 수 있는 rolling kernel이라는 것을 사용한다는 것을 알게되었다.

rolling kernel이란 default linux-aws 커널의 한 버전을 다음 버전으로 패치 주기에  
**_What is the rolling kernel model?_**  
A rolling kernel model transitions the default linux-aws kernel from one base version to the next as part of its regular patching cycle. That new kernel is the kernel of the latest interim Ubuntu release.  Applying this model directly to 18.04 today, the linux-aws kernel is a 4.15 based kernel and when we roll, it will become a 5.3 based kernel which was part of our 19.10 interim release.    

Today, that 5.3 kernel is currently available for preview as the linux-aws-edge kernel, which we encourage all users to run with their workloads in non-production deployments.  It is important to keep in mind that both the -edge kernels and the rolling release kernels are fully baked prior to being made available for our customers to use and meet exactly the same quality and durability standards all our kernels must meet for release.   
rolling kernel 모델을 통해 
1) 최신 AMI가 최신 커널을 사용하게 되고
2) 패키지 업데이트 또는 자동 보안 업데이트를 통해 최신 커널을 사용하게 된다.

## Reference
[Wikipedia - Kernel](https://en.wikipedia.org/wiki/Kernel_(operating_system))
[TuxCare blog - 2023년 Linux 커널 업데이트 심층 분석](https://tuxcare.com/ko/blog/the-2023-deep-dive-to-linux-kernel-updates/)
[TuxCare blog - 리눅스 커널 보안 이해하기: Linux 커널 패치의 필요성](https://tuxcare.com/ko/blog/demystifying-linux-kernel-security-the-need-for-linux-kernel-patching/)
[Introducing the Ubuntu AWS rolling kernel](https://ubuntu.com/blog/introducing-the-ubuntu-aws-rolling-kernel-2)
[Ubuntu kernels from Canonical](https://ubuntu.com/kernel)
[Ubuntu kernel variants from Canonical](https://ubuntu.com/kernel/variants)