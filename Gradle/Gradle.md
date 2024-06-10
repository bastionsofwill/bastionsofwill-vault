Gradle은 가장 널리 사용되는 오픈소스 빌드 자동화 툴이다.
Java 프로젝트를 빌드하는데 자주 쓰이지만, C/C++, Android, JavaScript 프로젝트를 빌드하는데 사용되기도 한다.

## Gradle Directories
![Gradle directories](gradle-directories.png)
### Gradle User Home directory
Gradle User Home에는 global 설정 등이 위치한다.
![Gradle User Home directory structure](gradle-user-home-dir-structure.png)
[Gradle Directories reference](https://docs.gradle.org/current/userguide/directory_layout.html#dir:gradle_user_home)
### Project Root directory
project root에는 프로젝트 source 파일과 프로젝트 관련 설정, gradle이 생성하는 파일 및 디렉토리(`.gradle`, `build`)가 위치한다.
![Gradle Project Root directory structure](gradle-project-root-dir-structure.png)

Gradle은 사전에 정의된 task를 수행하는 방식으로 동작하는데, `grade task` 명령어를 사용하여 현재 프로젝트에 정의된 task의 목록을 확인할 수 있다.
task는 `build.gradle`(Kotlin-based일 경우에는 `build.gradle.kts`)에서 정의하는데, built-in task만 사용하여도 웬만한 작업은 수행이 가능하다.

## Multi-Project Builds
Gradle의 이점 중 하나는 다중 프로젝트 빌드를 지원한다는 것이다.
![Gradle Multi-Project Build](gradle-multi-project-build.png)

프로젝트가 방대해질수록, 프로젝트를 상호의존적(*interdependant*)인 여러 모듈 단위로 분할하여 관리하여야 할 필요성이 생긴다.
다중 프로젝트 빌드는 하나의 root 프로젝트와 하나 이상의 하위 프로젝트(*subprojects*)로 구성된다.
![Gradle Multi-Project Build](gradle-multi-prj-build.png)
- root 프로젝트의 `settings.gradle.kts` 는 모든 하위 프로젝트를 포함한다.
- 각 하위 프로젝트는 개별 `build.gradle.kts` 파일을 갖는다.

다중 프로젝트 빌드 구조는 두 종류의 표준을 가진다.
### 1. Multi-Project Builds using `buildSrc`
`buildSrc`는 root 하위 모든 빌드 로직을 포함하는 유사 하위 프로젝트(subproject-like) 디렉토리이다.
### 2. Composite Builds
재사용 가능한 빌드 로직을 포함하는`build-logic`을 사용하며, 빌드가 다른 빌드를 포함하는 구조이다.






This page provides a detailed list of all the built-in Gradle tasks, organized by category. Some of the main categories include:

1. **Build tasks**: Tasks related to the overall build process, such as clean, build, assemble, and check.
2. **Compilation tasks**: Tasks related to compiling source code, such as compileJava, compileKotlin, and compileScala.
3. **Testing tasks**: Tasks related to running tests, such as test, check, and jacocoTestReport.
4. **Packaging tasks**: Tasks related to packaging the application, such as jar, war, and distZip.
5. **Documentation tasks**: Tasks related to generating documentation, such as javadoc and groovydoc.
6. **Publishing tasks**: Tasks related to publishing artifacts, such as publish and publishToMavenLocal.
Each task in the list includes a description of its purpose, the type of the task, and the plugins that provide the task.
Additionally, you can run the gradle tasks command in your project's directory to see a list of all the tasks available in your specific project, including both the built-in tasks and any custom tasks you've defined in your build.gradle.kts file.
By referring to the Gradle User Manual's "Task Reference" section, you can learn more about the built-in Gradle tasks and how to use them in your project's build process.


## References
- [Gradle 8.7 Userguide](https://docs.gradle.org/current/userguide)