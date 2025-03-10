# Create runner
1. docker volume create 
```
docker volume create gitlab-runner-RUNNER_IDX-config 
```
생성된 볼륨은 `/var/lib/docker/volumes`에서 확인 가능

2. GitLab Runner 생성
GitLab에서 Project 또는 Instance runner 생성 후 Registration token(`glrt-xxxxxxxxxxx`) 확인
```
RUNNER_IDX=01
RUNNER_TOKEN=glrt-xxxxxxxxxxx
```

3. Runner 등록
```
docker run --rm -it -v gitlab-runner-$RUNNER_IDX-config:/etc/gitlab-runner gitlab/gitlab-runner:latest register --non-interactive \
  --url "https://repo.cheil-tech.com/" \
  --token "$RUNNER_TOKEN" \
  --executor "docker" \
  --docker-image alpine:latest \
  --description "gitlab-runner-$RUNNER_IDX"
``` 
설정한 내용은 `/var/lib/docker/volumes/_data/gitlab-runner-01-config/config.toml`에 저장됨.

4. Runner 실행
```
docker run -d --name gitlab-runner-$RUNNER_IDX --restart always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v gitlab-runner-$RUNNER_IDX-config:/etc/gitlab-runner \
    gitlab/gitlab-runner:latest
```

# Upgrade runner
1. latest 버전 pull
```
docker pull gitlab/gitlab-runner:latest
```
2. 기존 runner stop
```
docker stop gitlab-runner-$RUNNER_IDX && docker rm gitlab-runner-$RUNNER_IDX
```
3. runner 실행
```
docker run -d --name gitlab-runner-$RUNNER_IDX --restart always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v gitlab-runner-$RUNNER_IDX-config:/etc/gitlab-runner \
    gitlab/gitlab-runner:latest
```
