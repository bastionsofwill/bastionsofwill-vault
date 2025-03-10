`/etc/passwd`는 다양한 사용자 정보를 담고 있지만, 패스워드는 저장하지 않는다.
그러면 대체 왜 이름이 이렇게 붙은걸까?
`/usr`이 'user'에서 유래한게 아니라 Unix System Resources인 것처럼, `passwd` 또한 'password'가 아니라 다른 뜻이 있을까?

password에서 유래한 것이 맞다.

1970년대 초기 유닉스 시스템에서는 실제로 `/etc/passwd` 파일에는 사용자 패스워드가(암호화는 되었지만) 다른 사용자 정보와 함께 저장되어있었다.
이를 통해 사용자 인증 데이터의 중심 저장소 역할을 하였지만, 읽기가 허용된 파일에 패스워드를 저장하는 것은 보안적으로 바람직하지 않았다.
이에 shadow password를 사용하게 되면서 `/etc/shadows`와 같이 읽기 권한이 제한된 별개 파일에 패스워드를 저장하게 되었다.
오늘날, `/etc/passwd`는 일반적으로 아래와 같은 사용자 계정 정보를 포함한다.
- Username
- User ID (UID)
- Group ID (GID)
- User's full name or description
- Home directory
- Login shell
- password 필드는 `x` 또는 `*`로 실제 패스워드가 다른 방식으로 저장되었음을 표시한다.
즉,  `/etc/passwd`라는 이름을 그대로 유지하는 것은 기존의 스크립트, 프로그램 등의 호환을 위함이다.