# FreeIPA란?^[https://en.wikipedia.org/wiki/FreeIPA]
FreeIPA는 IPA(Identity, Policy, and Audit)의 중앙집중적 관리를 목표로 하는, 오픈소스 솔루션이다.
Fedora Linux, 389 Directory Server, MIT Kerberos, NTP, DNS, DogTag, SSSD 등의 다양한 오픈 소스 기술에 기반하여 다양한 기능을 제공한다.

|Component|Details|
|---|---|
|Fedora Linux|Linux Operating System|
|389 Directory Server|LDAP implementation|
|MIT's Kerberos 5|authentication and single sign-on|
|ntpd|network time protocol|
|Apache HTTP Server|Web UI and management framework|
|Python|management framework|
|DogTag|PKI certificate authority|

# AS-IS
다양한 기술에 기반한 넓은 범위의 기능을 제공하기 때문에 잘 쓰면 강력하지만, 그만큼 잘 사용하는 것은 간단하지 않다.

현재 제일기획 DT개발팀에서는 FreeIPA를 사용하여 아래와 같은 기능을 수행하고 있다.
- GitLab 계정 관리
- Jenkins 접근 관리
- Grafana 접근 관리
- 서버 HBAC/RBAC

담당자가 1명이라는 제약이 있어, 위의 사용처에 비해 아래의 한계가 치명적으로 다가오기 때문에 이를 엎어버리고 다른 솔루션을 찾을 필요성을 느끼게 되었다.
1. **설정 및 관리의 복잡함**: 설정 관리 및 유지보수에 많은 공수 발생. 현재 잘 알지 못하고 사용중인 DNS(네임서버), NTP(타임서버) 관련 설정을 비롯한 오만 잡다한 설정들이 언제 무슨 문제를 일으킬지 모른다.
2. **완만한 러닝커브**: 기반이 되는 Kerberos, LDAP, DNS등의 기술에 대한 이해가 필요하나, 하나하나가 보통 시간을 잡아먹는게 아니라는 점이 장벽으로 작용
3. **리소스 확장성(Scalability) 한계**: 사용자나 관리 대상이 매우 많아질 경우, FreeIPA의 성능을 보장하기 위해 서버를 확장하는 것이 어려움 

# Kerberos
FreeIPA를 구성하는 여러 기술 중 가장 중추 역할을 수행하는 기술로, 아래와 같은 이점을 갖는 인증 프로토콜이다.
- 암호(password)가 네트워크에 전달되지 않음
- 암호화 키(encryption keys)가 직접적으로 교환되지 않음
- 사용자와 애플리케이션이 서로를 상호 인증이 가능함
- 다양한 조직에서 SSO의 기반으로 사용 가능함

널리 알려진 그 지옥의 파수견에서 유래한 이름이 맞으며, 허가된 사용자에게만 접근을 허용한다는 의미로 명명되었다고 한다.

![[kerberos_protocol.png]]

Kerberos는 접근 시 각자의 티켓을 통해 서로를 인증하기 때문에, 티켓을 발행하고 검증하는 KDC를 중심으로 동작하게 된다.

# 389 Directory Server^[https://en.wikipedia.org/wiki/389_Directory_Server]
389 Directory Server(LDAP의 default 포트인 389번 포트에서 유래)는 MS AD, OpenLDAP과 같은 LDAP 서버 소프트웨어로, Redhat에 의해 개발, maintain되고 있다. 
FreeIPA에서, LDAP 디렉토리는 user, group 등의 식별 정보의 중심 저장소 역할을 수행한다.
이를 기반으로 애플리케이션/서비스 등에 대한 접근 권한 등의 권한 정책을 관리할 수 있으며, 애플리케이션과 서비스 또한 LDAP 디렉토리에서 user, group 정보를 받아 권한에 따른 기능을 수행할 수 있다.

## Reference
[사람인 기술 블로그 - FreeIPA 를 활용한 IdM 구축](https://saramin.github.io/2022-06-30-idm/)
[Kerberos Authentication Explained | A deep dive](https://youtu.be/5N242XcKAsM?si=iN0GLQjZWzTHtEW_)
[https://dev.to/dhirva/set-up-freeipa-server-client-4kn7](https://dev.to/dhirva/set-up-freeipa-server-client-4kn7)

I'm trying to SSH access to a server using AlmaLinux, using private key. I added public key to target server's ~/.ssh/authorized_keys, set directory/file modifier and /etc/ssh/sshd_config well. but I failed, and this is what I got:

debug3: receive packet: type 51




eock6b4i3hihvzlzpgiwzcpdfivfpbqy._domainkey.retail-galaxy.com
eock6b4i3hihvzlzpgiwzcpdfivfpbqy.dkim.amazonses.com

2hxz7foz5c2roximt6qva52qt5rcsk7w._domainkey.retail-galaxy.com
2hxz7foz5c2roximt6qva52qt5rcsk7w.dkim.amazonses.com

prhtnetvx4etnxfuxf5ohza4rawtyqjq._domainkey.retail-galaxy.com
prhtnetvx4etnxfuxf5ohza4rawtyqjq.dkim.amazonses.com


So, you're saying that although NPM stands for Node Package manager, it is crucial to other projects using JS frameworks like vue.js or next.js. And it's because NPM also manages various Javascript dependencies which are not directly related to Node.js, right?