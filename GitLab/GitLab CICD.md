# Overview
GitLab CI는 job과 stage로 구성된다.
만약 stage 목록을 명시적으로 정의(explicitly define)하지 않는다면, 기본으로 아래의 default 스테이지를 실행한다.
1. build
2. test
3. deploy
모든 job은 stage를 가지며, 명시적으로 stage를 지정하지 않으면 기본으로 test 스테이지에 배정된다.

https://docs.gitlab.com/runner/install/docker.html

```
RUNNER_NAME=retail-galaxy-01
RUNNER_AUTH_TOKEN=glrt-3TBRb3t17gN-dF-y-9Lm
```
1. Instance/Group/Project runner 생성
2. docker volume 생성
```bash
docker volume create ${RUNNER_NAME}-config
```
3. docker volume register 
```bash
docker run --rm -v ${RUNNER_NAME}-config:/etc/gitlab-runner gitlab/gitlab-runner:latest register \
  --non-interactive \
  --url "https://repo.cheil-tech.com/" \
  --name "${RUNNER_NAME}" \
  --token "${RUNNER_TOKEN}" \
  --executor "docker" \
  --docker-image alpine:latest \
```
4. 실행
```bash
docker run -d --name ${RUNNER_NAME} --restart always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v ${RUNNER_NAME}-config:/etc/gitlab-runner \
  gitlab/gitlab-runner:latest
```

# Variable expansion mechanism
https://docs.gitlab.com/ee/ci/variables/where_variables_can_be_used.html#gitlab-internal-variable-expansion-mechanism

# Distributed Caching
## S3 Bucket 권한 허용
아래 policy가 **GitLab**과 **GitLab Runner** 인스턴스에 **모두** 붙어있어야 한다.
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObjectVersion",
                "s3:GetObject",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::dtdev-gitlab-runner-cache*"
        }
    ]
}
```

## S3 캐시 설정 관련 버그
```toml
[runners.cache]
  Type = "s3"
  Path = "PROJECT_NAME"
  Shared = true
  MaxUploadedArchiveSize = 0
  [runners.cache.s3]
    BucketName = "dtdev-gitlab-runner-cache"
    BucketLocation = "ap-northeast-2"
```
위와 같은 설정이 필요하지만, GitLab에서 제공하는 `register` CLI에 버그가 있어서 `[runner.cache.s3]` 하위 항목에 설정이 제대로 안 들어가므로 수동으로 넣어줘야함
https://gitlab.com/gitlab-org/gitlab-runner/-/issues/28003&ved=2ahUKEwihxammuqGJAxU94DQHHfhdLkQQFnoECBQQAQ&usg=AOvVaw3Pr7_a7OeQCEQqxMZZ0dnl

If I set config.toml in EFS properly and mount it to the new container when it starts, it will able to pick up job immediately without registration, right?