사내망 환경에서 Gradle을 사용하여 Spring project를 빌드하는 것은 나의 오랜 숙원이다.
수많은 시도를 하였으나 실패하였다.
시행착오라도 두 번 하지 않기 위해 기록한다.

# 프록시 설정
proxy는 사내망에서 `gradle build`가 성공하지 못하는 가장 큰 원인이다.
따라서 관련 설정을 `~/.gradle/gradle.properties`에 작성하였으나, 단 한 번도 효과를 보지 못하고 외부망 환경에서의 빌드만 방해해서 주석 처리했다.
```
# systemProp.http.proxyHost=121.252.4.178
# systemProp.http.proxyPort=8080
# systemProp.https.proxyHost=121.252.4.178
# systemProp.https.proxyPort=8080
```

# Gradle distribution 설치
`gradlew`를 사용하여 빌드 시, 명시된 version의 gradle이 `~/.gradle/wrapper/dists`에 설치되어있는지 확인 후 그렇지 않다면 아래와 같이 설치를 시도한다.
`Downloading https://services.gradle.org/distributions/gradle-8.7-bin.zip`
문제는 저 URL은 `https://github.com/gradle/gradle/releases/download/v8.7/gradle-8.7-bin.zip`로 리디렉션되는 방식으로 동작하기 때문에, `github.com`과 443포트 통신이 불가한 환경에서는 문제가 된다.
(사내망에서 따로 신청해서 허용처리 하면 브라우저에서는 해당 URL 사용하여 정상적으로 바이너리 파일을 다운로드할 수 있는데, gradle로 시도할 때는 꼭 위의 프록시 설정이 있든 없든 모두 실패했다.)

# Dependency 해소
한 번 외부망을 사용하여 맞는 버전의 Gradle distribution binary를 설치했다 해도, 여전히 빌드는 불가능하다.
1. **Dependency Resolution Stall**: If Gradle is stuck resolving dependencies, the progress rate may appear to be stuck at 0%. This can happen if there are issues with remote repositories, network connectivity, or problematic dependencies.
2. **Infinite Task Execution**: If your build script contains an infinite loop or a task that never completes, the progress rate will remain at 0% indefinitely.
3. **Gradle Daemon Crash**: If the Gradle Daemon has crashed or encountered an unrecoverable error, the build process may appear to be stuck at 0% progress.
4. **File System Monitoring Issues**: Problems with Gradle's file system monitoring can cause the build to appear stuck, as Gradle may be continuously checking for changes without making any actual progress.
5. **Resource Constraints**: If your system is running low on memory, CPU, or disk space, Gradle may be unable to make progress on the build, causing the progress rate to remain at 0%.

사내망에서 문제가 되는 것은 1. 프로젝트 dependency 해결이다.
한 번 외부망에 연결해서 빌드에 성공하고 나면, 새로운 dependency를 추가하지 않는 이상 빌드가 가능해진다.
아래 리포지토리에 443 포트 접근이 가능하면 빌드가 가능해야 할 것 같은데, 위 경우와 마찬가지로 브라우저에 Maven Central repository 주소를 치면 정상적으로 접근이 되는걸로 보아 443 포트 접근이 막힌 것은 아닌데 왜 Gradle은 실패하는지 모르겠다. 
프록시를 넣어봐도, 빼봐도 안 됐다.

When building a Gradle project, there are a few URLs and ports that typically need to be accessible: 

1. **Maven Central Repository**: The primary repository for Java/Kotlin libraries and dependencies.
   - URL: https://repo1.maven.org/maven2/
   - Port: 443 (HTTPS)
   - https://mvnrepository.com/repos/central

2. **JCenter Repository**: Another popular repository for Java/Kotlin libraries.
   - URL: https://jcenter.bintray.com/
   - Port: 443 (HTTPS)

3. **Google's Maven Repository**: Used for accessing Android-related dependencies.
   - URL: https://dl.google.com/dl/android/maven2/
   - Port: 443 (HTTPS)

4. **Gradle Distribution**: The Gradle wrapper may need to download the Gradle distribution from Gradle's servers.
   - URL: https://services.gradle.org/distributions/
   - Port: 443 (HTTPS)

5. **Custom Repositories**: If your project uses any custom or private Maven repositories, you'll need to ensure those URLs and ports are also accessible.

In addition to the repository URLs, you may also need to allow access to the following ports:

- **Port 5005**: If you're running Gradle in debug mode (e.g., org.gradle.debug=true), this port is used for remote debugging.
- **Port 8000**: Some Gradle plugins, like the Spring Boot plugin, may use this port for running the application during development.

# 결론
외부망에서 Gradle wrapper distribution 받고, dependency 해결한 상태로 다시 빌드하는 경우만 사내망에서 가능하다.
새로운 dependency가 프로젝트에 추가될 경우에는 이를 다시 외부망에서 가져온 다음에 사내망에서 빌드할 수 있다.