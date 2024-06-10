# JAR file's'
최초에 궁금했던 내용은 왜 `gradle build`를 수행하면 JAR 파일이 2개가 생기는지였다.
공부한 결과, thin JAR와 executable JAR라는 게 있고, 내가 필요한건 executable JAR이기 때문에 `jar` task를 비활성화하면 executable JAR만 얻을 수 있다는 것을 알게 되었다.
문제는, 내가 정의한 적이 없는 `jar` task는 왜 실행되는건지, `jar` task는 정확히 무슨 역할을 하는지, 비활성화해도 문제는 없는건지 모르겠다는 것이다.

그러고보니 애초에 나는 `build` task를 정의한 적도 없는데, 어떻게 빌드가 가능했을까?

# [The Java Plugin](https://docs.gradle.org/current/userguide/java_plugin.html)
`build.gradle.kts`에서 task를 정의할 수 있지만, `build` task의 정의는 보이지 않는다.
```kotlin
// build.gradle.kts
plugins {
	java
	id("org.springframework.boot") version "3.3.0"
	id("io.spring.dependency-management") version "1.1.5"
}

group = "com.example"
version = "1.0.0"

java {
	sourceCompatibility = JavaVersion.VERSION_17
}

repositories {
	mavenCentral()
}

dependencies {
	implementation("org.springframework.boot:spring-boot-starter-web")
	testImplementation("org.springframework.boot:spring-boot-starter-test")
	testRuntimeOnly("org.junit.platform:junit-platform-launcher")
}

tasks.jar {
	enabled = false
}

tasks.withType<Test> {
	useJUnitPlatform()
}
```
그럼에도 불구하고 `gradle build`를 실행할 수 있는 이유는 `plugins`에 있는 Java 플러그인 덕분이다. 
## Tasks
Java 플러그인은 아래와 같은 task를 추가해준다.
![javaPluginTasks](javaPluginTasks.png)

- `compileJava` — [JavaCompile](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.compile.JavaCompile.html)
_Depends on_: All tasks which contribute to the compilation classpath, including `jar` tasks from projects that are on the classpath via project dependencies
Compiles production Java source files using the JDK compiler.

- `processResources` — [ProcessResources](https://docs.gradle.org/current/dsl/org.gradle.language.jvm.tasks.ProcessResources.html)
Copies production resources into the production resources directory.

- `classes`
_Depends on_: `compileJava`, `processResources`
This is an aggregate task that just depends on other tasks. Other plugins may attach additional compilation tasks to it.

- `compileTestJava` — [JavaCompile](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.compile.JavaCompile.html)
_Depends on_: `classes`, and all tasks that contribute to the test compilation classpath
Compiles test Java source files using the JDK compiler.

- `processTestResources` — [Copy](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.Copy.html)
Copies test resources into the test resources directory.

- `testClasses`
_Depends on_: `compileTestJava`, `processTestResources`
This is an aggregate task that just depends on other tasks. Other plugins may attach additional test compilation tasks to it.

- `jar` — [Jar](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html)
_Depends on_: `classes`
Assembles the production JAR file, based on the classes and resources attached to the `main` source set.

- `javadoc` — [Javadoc](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.javadoc.Javadoc.html)
_Depends on_: `classes`
Generates API documentation for the production Java source using Javadoc.

- `test` — [Test](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.testing.Test.html)
_Depends on_: `testClasses`, and all tasks which produce the test runtime classpath
Runs the unit tests using JUnit or TestNG.

- `clean` — [Delete](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.Delete.html)
Deletes the project build directory.

- `clean_TaskName_` — [Delete](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.Delete.html)
Deletes files created by the specified task. For example, `cleanJar` will delete the JAR file created by the `jar` task and `cleanTest` will delete the test results created by the `test` task.
#### SourceSet Tasks
For each source set you add to the project, the Java plugin adds the following tasks:

- `compile_SourceSet_Java` — [JavaCompile](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.compile.JavaCompile.html)
_Depends on_: All tasks which contribute to the source set’s compilation classpath
Compiles the given source set’s Java source files using the JDK compiler.

- `process_SourceSet_Resources` — [Copy](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.Copy.html)
Copies the given source set’s resources into the resources directory.

- `_sourceSet_Classes` — [Task](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html)
_Depends on_: `compile_SourceSet_Java`, `process_SourceSet_Resources`
Prepares the given source set’s classes and resources for packaging and execution. Some plugins may add additional compilation tasks for the source set.

### Lifecycle Tasks
The Java plugin attaches some of its tasks to the lifecycle tasks defined by the [Base Plugin](https://docs.gradle.org/current/userguide/base_plugin.html#sec:base_tasks) — which the Java Plugin applies automatically — and it also adds a few other lifecycle tasks:

- `assemble`
_Depends on_: `jar`
Aggregate task that assembles all the archives in the project. This task is added by the Base Plugin.

- `check`
_Depends on_: `test`
Aggregate task that performs verification tasks, such as running the tests. Some plugins add their own verification tasks to `check`. You should also attach any custom `Test` tasks to this lifecycle task if you want them to execute for a full build. This task is added by the Base Plugin.

- `build`
_Depends on_: `check`, `assemble`
Aggregate tasks that performs a full build of the project. This task is added by the Base Plugin.

- `buildNeeded`
_Depends on_: `build`, and `buildNeeded` tasks in all projects that are dependencies in the `testRuntimeClasspath` configuration.
Performs a full build of the project and all projects it depends on.

- `buildDependents`
_Depends on_: `build`, and `buildDependents` tasks in all projects that have this project as a dependency in their `testRuntimeClasspath` configurations
Performs a full build of the project and all projects which depend upon it.

- `build_ConfigName_` — _task rule_
_Depends on_: all tasks that generate the artifacts attached to the named — _ConfigName_ — configuration
Assembles the artifacts for the specified configuration. This rule is added by the Base Plugin.
