Private 네트워크에 있는 리모트 서버에 배포하기 위해, bastion 서버를 거쳐 `scp`로 파일을 전송하여야 하는 경우가 있다. 

1. `scp -o ProxyCommand` 사용
```bash
scp -o ProxyCommand="ssh -W %h:%p $BASTION_USER@$BASTION_HOST" $FILE_TO_SEND $REMOTE_USER@$REMOTE_HOST:/path/on/remote/server/
```
`scp`으로 직접적으로 `ProxyCommand`옵션으로 bastion 호스트를 통할 연결을 수행하는 `ssh`명령을 명시한다.
파일 전송과 bastion 호스트 연결을 모두 `scp`가 담당한다.

2. `scp` 명령 스크립트를 `ssh` 명령에 인수로 전달
```bash
ssh $BASTION_USER@$BASTION_HOST "scp $FILE_TO_SEND $REMOTE_USER@$REMOTE_HOST:/path/on/remote/server/"
```
local 호스트에서 실행하는 `ssh`명령에, bastion 호스트에서 수행할 `scp`명령을 인수로 전달하는 방식이다.
`ssh`가 bastion 호스트까지의 연결을, bastion 호스트에서의 `scp`가 파일 전송을 담당하는 구조이다.

두 방식의 차이점은 아래와 같다.

|| `scp`에 `ProxyCommand` 옵션 사용 | `ssh`에 `scp` 명령 인수로 전달 |
|--|--|--|
|**Connection Handling**|`scp`: 연결을 포함한 모든 절차를 담당|`ssh`: bastion 호스트와의 연결을 담당<br>`scp`: 파일 전송을 담당|
|**Execution Context**|local에서 `scp`가 실행됨|`scp`는 bastion 호스트에서 실행됨|

두 방식의 결과는 같지만, `ProxyCommand`옵션을 사용하는 방식이 프록시 연결을 `scp` 안에서 캡슐화하기 때문에 더 관리하기 쉽고 우아하다(는데 나는 2가 더 직관적으로 이해하기 쉽다...).