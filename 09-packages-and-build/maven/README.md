# Maven

## What Is Maven?

Apache Maven is a build automation and dependency management tool for Java projects. It does three things:

1. **Dependency management** — you declare what libraries your project needs; Maven downloads them from Maven Central automatically.
2. **Build automation** — a standard lifecycle (compile → test → package) that any developer can run with a single command.
3. **Project standardisation** — every Maven project has the same directory layout, so any Java developer can navigate it immediately.

---

## Standard Maven Project Structure

```
my-project/
├── pom.xml                  ← the Project Object Model (build descriptor)
└── src/
    ├── main/
    │   └── java/
    │       └── com/amalitech/myproject/
    │           └── Main.java
    └── test/
        └── java/
            └── com/amalitech/myproject/
                └── MainTest.java
```

Source code lives in `src/main/java`. Test code lives in `src/test/java`. Build output goes to `target/` (never commit this directory — add it to `.gitignore`).

---

## The pom.xml File

POM stands for Project Object Model. It is an XML file at the project root that describes the project and its build configuration.

### Minimal pom.xml for Java 21 with JUnit 5

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
             https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <!-- Project coordinates — uniquely identifies this artifact -->
    <groupId>com.amalitech</groupId>
    <artifactId>my-project</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <!-- Tells Maven to compile for Java 21 -->
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <!-- Ensures consistent file encoding -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!-- Centralise version numbers to avoid repetition -->
        <junit.version>5.10.1</junit.version>
    </properties>

    <dependencies>

        <!-- JUnit 5 — test scope means it is not included in the production JAR -->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>${junit.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- Mockito for mocking in tests -->
        <dependency>
            <groupId>org.mockito</groupId>
            <artifactId>mockito-core</artifactId>
            <version>5.8.0</version>
            <scope>test</scope>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <!-- Required to run JUnit 5 tests with mvn test -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.2.3</version>
            </plugin>

            <!-- Packages the application as an executable JAR -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.3.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>com.amalitech.myproject.Main</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

---

## Key POM Elements

| Element | Description |
|---------|-------------|
| `groupId` | Organisation identifier — typically reverse domain: `com.amalitech` |
| `artifactId` | Project/module name: `my-project` |
| `version` | Current version. `SNAPSHOT` means in-development |
| `packaging` | Output type: `jar` (default), `war`, `pom` |
| `dependencies` | Libraries this project needs |
| `properties` | Reusable key-value pairs referenced with `${key}` |
| `build.plugins` | Build tool plugins (compiler, testing, packaging) |

---

## Dependency Scopes

Scope controls when a dependency is on the classpath.

| Scope | Compile | Test | Runtime JAR | Description |
|-------|---------|------|-------------|-------------|
| `compile` (default) | ✅ | ✅ | ✅ | Available everywhere; included in final JAR |
| `test` | ❌ | ✅ | ❌ | Only for tests; not in production JAR |
| `provided` | ✅ | ✅ | ❌ | Available at compile time, supplied by the runtime (e.g. servlet container) |
| `runtime` | ❌ | ✅ | ✅ | Not needed to compile, but needed to run (e.g. JDBC driver) |

---

## Adding Dependencies

Find dependencies on [Maven Central](https://search.maven.org/). Copy the XML snippet and paste it inside `<dependencies>`.

Example — adding Google Guava:

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>32.1.3-jre</version>
</dependency>
```

Maven downloads the JAR to your local repository (`~/.m2/repository`) and caches it. Subsequent builds use the cached copy.

---

## Maven Build Lifecycle

Maven defines a standard sequence of phases. Running a later phase runs all earlier phases first.

| Phase | What It Does |
|-------|-------------|
| `validate` | Checks the POM is valid and complete |
| `compile` | Compiles `src/main/java` → `target/classes` |
| `test` | Compiles and runs tests in `src/test/java` |
| `package` | Creates `target/my-project-1.0.0-SNAPSHOT.jar` |
| `verify` | Runs integration tests and quality checks |
| `install` | Installs the JAR into your local `~/.m2` repository |
| `deploy` | Uploads the JAR to a remote repository (CI/CD) |

---

## Common Maven Commands

```bash
# Download dependencies and compile source code
mvn compile

# Compile and run all tests
mvn test

# Skip tests (useful during development)
mvn test -DskipTests

# Compile, test, and create the JAR
mvn package

# Install to local repository (makes it available to other local projects)
mvn install

# Delete the target/ directory
mvn clean

# Most common command: clean, compile, test, package
mvn clean package

# Run the packaged JAR
java -jar target/my-project-1.0.0-SNAPSHOT.jar
```

---

## Dependency Tree

To see all dependencies (including transitive ones — dependencies of dependencies):

```bash
mvn dependency:tree
```

---

## Multi-Module Projects

Large projects split into modules, each with its own `pom.xml`. A parent POM ties them together:

```xml
<!-- parent pom.xml -->
<modules>
    <module>core</module>
    <module>web</module>
    <module>cli</module>
</modules>
```

---

## Tips

- Always run `mvn clean` before `mvn package` in CI to ensure a reproducible build.
- Commit `pom.xml` but not the `target/` directory.
- Pin dependency versions explicitly — avoid open ranges like `[1.0,2.0)`.
- Use `<dependencyManagement>` in a parent POM to centralise version numbers across modules.
