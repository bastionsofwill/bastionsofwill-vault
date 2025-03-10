```json
{
  "family": "gitlab-runner",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "1",
  "memory": "2000",
  "volumes": [
    {
      "name": "efs-config",
      "efsVolumeConfiguration": {
        "fileSystemId": "fs-1234abcd",
        "rootDirectory": "/gitlab-runner-config"
      }
    }
  ],
  "containerDefinitions": [
    {
      "name": "gitlab-runner",
      "image": "gitlab/gitlab-runner:latest",
      "essential": true,
      "mountPoints": [
        {
          "sourceVolume": "docker-socket",
          "containerPath": "/var/run/docker.sock",
          "readOnly": false
        },
        {
          "sourceVolume": "efs-config",
          "containerPath": "/etc/gitlab-runner",
          "readOnly": true
        }
      ],
      "command": ["gitlab-runner run --max-builds 1"],
      "environment": [
        {
          "name": "ECS_CLUSTER_NAME",
          "value": "your-cluster-name"
        }
      ],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/gitlab-runner",
          "awslogs-region": "${AWS_REGION}",
          "awslogs-stream-prefix": "ecs"
        }
      }
    }
  ],
  "taskRoleArn": "arn:aws:iam::your-account-id:role/ecsTaskExecutionRole"
}
```


to run ecs task on EC2, I need ecsTaskExecutionRole for task definition and AmazonEC2ContainerServiceforEC2Role Policy for the role attached to EC2 instance. Also, AWS attaches ECSServiceRole to ECS Service by default. Why do I need these 3 roles and how they work to run containers in my EC2 instance with ALB?


Let me break down the three different IAM roles and explain their specific purposes in the ECS architecture with EC2 and ALB:

1. **ecsTaskExecutionRole** (Task Execution Role): ECS Agent가 task에 관련된 작업에 사용하는 role로, task가 아래와 같은 AWS API 호출을 가능하게 한다.
- ECR에서 이미지 Pull
- 컨테이너 로그를 CloudWatch Logs로 전달
- AWS Secrets Manager/Parameter Store로부터 정보 얻기

Example minimum permissions (managed policy: AmazonECSTaskExecutionRolePolicy):
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ecr:GetAuthorizationToken",
                "ecr:BatchCheckLayerAvailability",
                "ecr:GetDownloadUrlForLayer",
                "ecr:BatchGetImage",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "*"
        }
    ]
}
```

2. **AmazonEC2ContainerServiceforEC2Role** (EC2 Instance Role):• This role is used by the ECS Agent running on EC2 instances
• Primary responsibilities:
- Register/deregister container instances with ECS clusters
- Report container instance status
- Connect to ALB for health checks and registration

- Pull container images

  

Key permissions include:

**json**

{

    "Version": "2012-10-17",

    "Statement": [

        {

            "Effect": "Allow",

            "Action": [

                "ecs:CreateCluster",

                "ecs:RegisterContainerInstance",

                "ecs:DeregisterContainerInstance",

                "ecs:DiscoverPollEndpoint",

                "ecs:UpdateContainerInstancesState",

                "ecs:Submit*"

            ],

            "Resource": "*"

        }

    ]

}

  

  

3. **ECSServiceRole** (Service Role):

• This role allows ECS services to interact with ALB

• Primary responsibilities:

- Register/deregister tasks with ALB target groups

- Perform health checks

- Update load balancer when tasks start/stop

  

Key permissions include:

**json**

{

    "Version": "2012-10-17",

    "Statement": [

        {

            "Effect": "Allow",

            "Action": [

                "elasticloadbalancing:RegisterTargets",

                "elasticloadbalancing:DeregisterTargets",

                "elasticloadbalancing:Describe*"

            ],

            "Resource": "*"

        }

    ]

}

  

  

How they work together:

1. When you launch an EC2 instance:

   • EC2 instance role allows the ECS Agent to communicate with ECS service

   • ECS Agent registers the instance with your cluster

  

2. When you run a task:

   • Task Execution Role pulls images and sends logs

   • ECS Agent uses instance role to report task status

  

3. When using ALB:

   • Service Role manages target group registration

   • ALB routes traffic to container ports

   • ECS Agent reports container health status

  

This separation of roles follows the principle of least privilege, where each component has only the 

permissions it needs to perform its specific functions. The roles work together to enable secure container

orchestration and load balancing while maintaining proper access controls.