# Gradle

## What Is Gradle?

Gradle is a build automation tool that uses a Groovy or Kotlin DSL instead of XML. It is the default build tool for Android projects and is widely used in modern Java and Kotlin backend projects. Its build scripts are significantly shorter than Maven's POM files and it supports incremental builds, which makes it faster on large projects.

---

## Groovy DSL vs Kotlin DSL

Gradle supports two scripting languages for `build.gradle`:

| Feature | Groovy DSL (`build.gradle`) | Kotlin DSL (`build.gradle.kts`) |
|---------|-----------------------------|---------------------------------|
| Syntax | Dynamic, loosely typed | Statically typed, IDE-friendly |
| IDE autocompletion | Limited | Excellent |
| Performance | Slightly faster to parse | Slightly slower first build |
| Adoption | Established, more examples online | Growing rapidly — preferred for new projects |

---

## A Minimal build.gradle.kts (Kotlin DSL, Java 21, JUnit 5)

```kotlin
plugins {
    java
}

group = "com.amalitech"
version = "1.0.0-SNAPSHOT"

java {
    sourceCompatibility = JavaVersion.VERSION_21
    targetCompatibility = JavaVersion.VERSION_21
}

repositories {
    mavenCentral()
}

dependencies {
    testImplementation(platform("org.junit:junit-bom:5.10.1"))
    testImplementation("org.junit.jupiter:junit-jupiter")
    testRuntimeOnly("org.junit.platform:junit-platform-launcher")
}

tasks.test {
    useJUnitPlatform()
}
```

---

## A Minimal build.gradle (Groovy DSL)

```groovy
plugins {
    id 'java'
}

group = 'com.amalitech'
version = '1.0.0-SNAPSHOT'

java {
    sourceCompatibility = JavaVersion.VERSION_21
    targetCompatibility = JavaVersion.VERSION_21
}

repositories {
    mavenCentral()
}

dependencies {
    testImplementation platform('org.junit:junit-bom:5.10.1')
    testImplementation 'org.junit.jupiter:junit-jupiter'
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}

test {
    useJUnitPlatform()
}
```

---

## Project Structure

Gradle uses the same standard directory layout as Maven:

```
my-project/
├── build.gradle.kts     ← build script
├── settings.gradle.kts  ← project name and subprojects
├── gradlew              ← Gradle wrapper script (Unix)
├── gradlew.bat          ← Gradle wrapper script (Windows)
├── gradle/
│   └── wrapper/
│       └── gradle-wrapper.properties
└── src/
    ├── main/java/
    └── test/java/
```

---

## Common Gradle Commands

```bash
# Compile source code
./gradlew compileJava

# Run tests
./gradlew test

# Build the project (compile + test + JAR)
./gradlew build

# Clean build outputs
./gradlew clean

# Clean and build
./gradlew clean build

# Skip tests
./gradlew build -x test

# List all available tasks
./gradlew tasks
```

Always use `./gradlew` (the wrapper) rather than a system-installed `gradle`. The wrapper pins the Gradle version and ensures every developer uses the same one.

---

## Gradle Wrapper

The Gradle wrapper (`gradlew` / `gradlew.bat`) is a small script committed to the repository. When run, it downloads the correct Gradle version if not already present, then executes the build. This avoids "works on my machine" issues caused by different Gradle versions.

The wrapper version is specified in `gradle/wrapper/gradle-wrapper.properties`:

```properties
distributionUrl=https\://services.gradle.org/distributions/gradle-8.5-bin.zip
```

---

## Gradle Tasks

A Gradle build is composed of tasks. Each task does one unit of work (compile, test, copy files). Tasks declare dependencies on other tasks — running `build` automatically runs `compileJava`, `test`, and `jar`.

You can define custom tasks:

```kotlin
tasks.register("hello") {
    doLast {
        println("Hello from Gradle!")
    }
}
```

```bash
./gradlew hello
# > Task :hello
# Hello from Gradle!
```

---

## Gradle vs Maven — Quick Comparison

| Aspect | Maven | Gradle |
|--------|-------|--------|
| Config format | XML (`pom.xml`) | Kotlin/Groovy DSL |
| Build speed | Slower (always full rebuild) | Faster (incremental builds, build cache) |
| Learning curve | Easier to start | More flexible but more to learn |
| Convention | Strict lifecycle phases | Flexible task graph |
| Android | Not supported | Official build tool |
| Ecosystem | Larger, more mature | Growing rapidly |
| Verbosity | High (XML) | Low (DSL) |

**When to use Maven:** Established Java/Jakarta EE projects, teams already using Maven, Spring Boot projects (both work, Maven is more documented in Spring tutorials).

**When to use Gradle:** Android projects (required), Kotlin-first projects, projects needing custom build logic, monorepos with many subprojects.

---

## Dependency Configuration Names

Gradle's configuration names differ slightly from Maven scopes:

| Maven scope | Gradle configuration |
|-------------|---------------------|
| `compile` | `implementation` |
| `test` | `testImplementation` |
| `provided` | `compileOnly` |
| `runtime` | `runtimeOnly` |

```kotlin
dependencies {
    implementation("com.google.guava:guava:32.1.3-jre")
    testImplementation("org.junit.jupiter:junit-jupiter:5.10.1")
    compileOnly("jakarta.servlet:jakarta.servlet-api:6.0.0")
}
```
