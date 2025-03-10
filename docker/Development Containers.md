# Overview
## [What are development containers?](https://containers.dev/overview#overview)
로컬/클라우드 컨테이너 기반 개발에 필요한 설정값을 서술하기 위한 구조화된 JSON with Comments (jsonc) 메타데이터 형식(format)으로, 다양한 컨테이너 오케스트레이션이 필요한 상황에서 개발에 필요한 설정과 툴을 관리하는 것을 목표로 한다.
즉, dev container는 배포 준비가 완료되기 전에 소프트웨어가 개발되는 환경을 정의한다.
![[dev-container-stages.png.png]]

dev container 메타데이터는 이제 [image labels](https://containers.dev/implementors/spec/#image-metadata)과 [Dev Container Features](https://containers.dev/features)(재사용 가능한 메타데이터와 설치 스크립트 모음)에 저장이 가능하다. 

[Using Images, Dockerfiles, and Docker Compose](https://containers.dev/guide/dockerfile#dockerfile)

# devcontainer.json
The `devcontainer.json` file contains any needed metadata and settings required to configurate a **development container** for a given well-defined tool and runtime stack. It can be used by [tools and services that support the dev container spec](https://containers.dev/supporting) to create a **development environment** that contains one or more **development containers**.
```json
{
  "name": "Python 3",
  // Or use a Dockerfile or Docker Compose file. More info: https://containers.dev/guide/dockerfile
  "image": "mcr.microsoft.com/devcontainers/python:1-3.11-bullseye",
  "customizations": {
    "codespaces": {
      "openFiles": [
        "README.md",
        "streamlit_app.py"
      ]
    },
    "vscode": {
      "settings": {},
      "extensions": [
        "ms-python.python",
        "ms-python.vscode-pylance"
      ]
    }
  },
  "updateContentCommand": "[ -f packages.txt ] && sudo apt update && sudo apt upgrade -y && sudo xargs apt install -y <packages.txt; [ -f requirements.txt ] && pip3 install --user -r requirements.txt; pip3 install --user streamlit; echo '✅ Packages installed and Requirements met'",
  "postAttachCommand": {
    "server": "streamlit run streamlit_app.py --server.enableCORS false --server.enableXsrfProtection false"
  },
  "portsAttributes": {
    "8501": {
      "label": "Application",
      "onAutoForward": "openPreview"
    }
  },
  "forwardPorts": [
    8501
  ]
}
```

Metadata properties marked with a 🏷️️ can be stored in the `devcontainer.metadata` **[container image label](https://containers.dev/implementors/reference/#labels)** in addition to `devcontainer.json`. This label can contain an array of json snippets that will be automatically merged with `devcontainer.json` contents (if any) when a container is created.

## [General devcontainer.json properties](https://containers.dev/implementors/json_reference/#general-properties)
|Property|Type|Description|
|---|---|---|
|`name`|string|A name for the dev container displayed in the UI.|
|`forwardPorts` 🏷️|array|An array of port numbers or `"host:port"` values (e.g. `[3000, "db:5432"]`) that should always be forwarded from inside the primary container to the local machine (including on the web). The property is most useful for forwarding ports that cannot be auto-forwarded because the related process that starts before the `devcontainer.json` supporting service / tool connects or for forwarding a service not in the primary container in Docker Compose scenarios (e.g. `"db:5432"`). Defaults to `[]`.|
|`portsAttributes` 🏷️|object|Object that maps a port number, `"host:port"` value, range, or regular expression to a set of default options. See [port attributes](https://containers.dev/implementors/json_reference/#port-attributes) for available options. For example:  <br>`"portsAttributes": {"3000": {"label": "Application port"}}`|
|`customizations` 🏷️|object|Product specific properties, defined in [supporting tools](https://containers.dev/supporting)|

## [Scenario specific properties](https://containers.dev/implementors/json_reference/#scenario-specific)
### [Image or Dockerfile specific properties](https://containers.dev/implementors/json_reference/#image-specific)
|Property|Type|Description|
|---|---|---|
|`image`|string|**Required** when using an image. The name of an image in a container registry ([DockerHub](https://hub.docker.com), [GitHub Container Registry](https://docs.github.com/packages/guides/about-github-container-registry), [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)) that `devcontainer.json` supporting services / tools should use to create the dev container.|
|`build.dockerfile`|string|**Required** when using a Dockerfile. The location of a [Dockerfile](https://docs.docker.com/engine/reference/builder/) that defines the contents of the container. The path is relative to the `devcontainer.json` file.|

### [Docker Compose specific properties](https://containers.dev/implementors/json_reference/#compose-specific)
|Property|Type|Description|
|---|---|---|
|`dockerComposeFile`|string,  <br>array|**Required** when using [Docker Compose](https://docs.docker.com/compose/). Path or an ordered list of paths to Docker Compose files relative to the `devcontainer.json` file. Using an array is useful [when extending your Docker Compose configuration](https://docs.docker.com/compose/extends/#multiple-compose-files). The order of the array matters since the contents of later files can override values set in previous ones.  <br>The default `.env` file is picked up from the root of the project, but you can use `env_file` in your Docker Compose file to specify an alternate location.  <br>Note that the array syntax will execute the command without a shell. You can [learn more](https://containers.dev/implementors/json_reference/#formatting-string-vs-array-properties) about formatting string vs array properties.|

## [Lifecycle scripts](https://containers.dev/implementors/json_reference/#lifecycle-scripts)
|Property|Type|Description|
|---|---|---|
|`initializeCommand`|string,  <br>array,  <br>object|A command string or list of command arguments to run on the **host machine** during initialization, including during container creation and on subsequent starts. The command may run more than once during a given session.  <br>  <br>⚠️ The command is run wherever the source code is located on the host. For cloud services, this is in the cloud.  <br>  <br>Note that the array syntax will execute the command without a shell. You can [learn more](https://containers.dev/implementors/json_reference/#formatting-string-vs-array-properties) about formatting string vs array vs object properties.|
|`onCreateCommand` 🏷️|string,  <br>array,  <br>object|This command is the first of three (along with `updateContentCommand` and `postCreateCommand`) that finalizes container setup when a dev container is created. It and subsequent commands execute **inside** the container immediately after it has started for the first time.  <br>  <br>Cloud services can use this command when caching or prebuilding a container. This means that it will not typically have access to user-scoped assets or secrets.  <br>  <br>Note that the array syntax will execute the command without a shell. You can [learn more](https://containers.dev/implementors/json_reference/#formatting-string-vs-array-properties) about formatting string vs array vs object properties.|
|`updateContentCommand` 🏷️|string,  <br>array,  <br>object|This command is the second of three that finalizes container setup when a dev container is created. It executes inside the container after `onCreateCommand` whenever new content is available in the source tree during the creation process.  <br>  <br>It will execute at least once, but cloud services will also periodically execute the command to refresh cached or prebuilt containers. Like cloud services using `onCreateCommand`, it can only take advantage of repository and org scoped secrets or permissions.  <br>  <br>Note that the array syntax will execute the command without a shell. You can [learn more](https://containers.dev/implementors/json_reference/#formatting-string-vs-array-properties) about formatting string vs array vs object properties.|
|`postCreateCommand` 🏷️|string,  <br>array,  <br>object|This command is the last of three that finalizes container setup when a dev container is created. It happens after `updateContentCommand` and once the dev container has been assigned to a user for the first time.  <br>  <br>Cloud services can use this command to take advantage of user specific secrets and permissions.  <br>  <br>Note that the array syntax will execute the command without a shell. You can [learn more](https://containers.dev/implementors/json_reference/#formatting-string-vs-array-properties) about formatting string vs array vs object properties.|
|`postStartCommand` 🏷️|string,  <br>array,  <br>object|A command to run each time the container is successfully started.  <br>  <br>Note that the array syntax will execute the command without a shell. You can [learn more](https://containers.dev/implementors/json_reference/#formatting-string-vs-array-properties) about formatting string vs array vs object properties.|
|`postAttachCommand` 🏷️|string,  <br>array,  <br>object|A command to run each time a tool has successfully attached to the container.  <br>  <br>Note that the array syntax will execute the command without a shell. You can [learn more](https://containers.dev/implementors/json_reference/#formatting-string-vs-array-properties) about formatting string vs array vs object properties.|

# VSCode Dev Containers
**Visual Studio Code Dev Containers**는 컨테이너 기반 개발 환경을 제공하는 VSCode extension으로, 별도의 컨테이너에 로컬 환경과 독립된 개발환경을 구성할 수 있게 한다.
로컬 파일 시스템의 일부를 마운트하거나 복사하여 컨테이너 내부 workspace 파일이 생성되며, Extension은 컨테이너 내부에 설치되고 실행되며, tools, platform, and file system에 대한 모든 접근 권한을 갖는다.
![[vscode-devcontainers-architecture-containers.png.png]]
이를 통해, VSCode의 기능을 독립된 환경에서도 로컬 환경에서 개발하는 것과 같이 사용할 수 있다.