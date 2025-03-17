1. [Admin area](https://repo.cheil-tech.com/admin)
2. [Applications](https://repo.cheil-tech.com/admin/applications)
3. [GitLab Web IDE](https://repo.cheil-tech.com/admin/applications/10)

|   |   |
|---|---|
|Callback URL|https://repo.cheil-tech.com/-/ide/oauth_redirect|
|Trusted|Yes|
|Confidential|No|
|Scopes|- api (Access the API on your behalf)|

`/etc/gitlab/gitlab.rb`에 `external_url`이 https://repo.cheil-tech.com 인지도 확인: scheme 다를 경우 admin page에서 계속 경고 메시지.