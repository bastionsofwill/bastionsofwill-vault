https://brunch.co.kr/@alden/27

logrotate는 한도끝도없이 길어질 수 있는 로그 파일을 분할하여 관리할 수 있게 해주는 유틸리티이다.
설정에 따라 접두사/접미사를 붙이는 등 로그 파일을 rename해주지만, i-node 번호같은 정보가 그대로이기 때문에 이렇게만 설정하면 여전히 하나의 rename된 파일에 쌓이게 되므로 추가적인 설정이 필요하다.

이 중 한가지 옵션이 copytruncate로, 기존 로그 파일을 새로 생성한 빈 파일에 copy한 뒤, 기존 로그 파일을 truncate 해버리기 때문에 이렇게 이름이 붙었다.
다만 문제는 기존 로그파일의 크기가 클 경우, copy 시 부하가 발생할 수 있다는 점이다.

이를 해결하기 위해 log 발생주체가 제공하는 re-open signal을 사용하여 signal이 발생할 때마다 기존 로그 파일을 rename 하는 방식으로 로그 파일을 분할하여 관리할 수 있다.
