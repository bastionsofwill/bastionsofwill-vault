https://docs.gitlab.com/omnibus/settings/logs/

# 로그 조회
`/var/log/gitlab`하위 경로에 저장되는 로그를 조회 가능하다.
```zsh
# Tail all logs; press Ctrl-C to exit
sudo gitlab-ctl tail

# Drill down to a sub-directory of /var/log/gitlab
sudo gitlab-ctl tail gitlab-rails

# Drill down to an individual file
sudo gitlab-ctl tail nginx/gitlab_error.log
```

# 로그 로테이션
다른 많은 설정들과 비슷하게, `/etc/gitlab/gitlab.rb`

ㅌ타탖타저정저오옻오처!