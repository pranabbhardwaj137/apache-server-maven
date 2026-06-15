# DevOps Lab P5/P8/P9 - Maven & Gradle

## Aim

Install JDK, build a Java app with Maven (POM, dependencies, plugins), then migrate the same project to Gradle.

---

# Project Structure (Maven)

```text
demo-project/
│
├── pom.xml
└── src/
    ├── main/java/com/example/App.java
    └── test/java/com/example/AppTest.java
```

---

# Project Structure (Gradle — after migration)

```text
demo-project/
│
├── build.gradle
├── settings.gradle
└── src/
    ├── main/java/com/example/App.java
    └── test/java/com/example/AppTest.java
```

---

# Part 1: Install JDK 21

1. Download JDK 21 from https://www.oracle.com/java/technologies/javase/jdk21-archive-downloads.html  
   Direct: https://download.oracle.com/java/21/archive/jdk-21.0.9_windows-x64_bin.exe
2. Run installer → Next → Next → Install → Finish  
   Default path: `C:\Program Files\Java\jdk-21`

## Set Environment Variables

1. Win + S → search **Environment Variables** → **Edit the system environment variables**
2. **Environment Variables** → under **System variables** → **New**:

| Variable | Value |
|----------|-------|
| `JAVA_HOME` | `C:\Program Files\Java\jdk-21` |

3. Select **Path** → **Edit** → **New** → add `%JAVA_HOME%\bin` → OK

Verify:

```bash
java -version
```

---

# Part 2: Install Maven

## Step 1: Download

https://maven.apache.org/download.cgi

Download **Binary zip only**: `apache-maven-3.9.6-bin.zip`

Do **not** download `src.zip` or `.tar.gz`.

## Step 2: Extract & Set PATH

1. Extract to: `C:\Program Files\Apache\Maven\apache-maven-3.9.6`
2. Open **CMD as Administrator**:

```bash
setx MAVEN_HOME "C:\Program Files\Apache\Maven\apache-maven-3.9.6" /M
setx PATH "%PATH%;C:\Program Files\Apache\Maven\apache-maven-3.9.6\bin" /M
```

3. Restart CMD and verify:

```bash
mvn -v
```

---

# Part 3: Create Maven Project

```bash
mvn archetype:generate -DgroupId=com.example -DartifactId=demo-project -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

```bash
cd demo-project
```

---

# Part 4: Understand `pom.xml`

Key sections:

| Section | Purpose |
|---------|---------|
| `<groupId>` | Organization / package namespace |
| `<artifactId>` | Project name |
| `<version>` | Project version |
| `<dependencies>` | External libraries (auto-downloaded from Maven Central) |
| `<build><plugins>` | Build tools (compiler, packaging, etc.) |

## Default `pom.xml` (generated)

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>demo-project</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>demo-project</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.11.0</version>
        <configuration>
          <source>21</source>
          <target>21</target>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

---

# Part 5: Add Dependencies

Add libraries inside `<dependencies>` in `pom.xml`. Maven downloads them automatically.

## Example: Gson (JSON parsing)

```xml
<dependency>
  <groupId>com.google.code.gson</groupId>
  <artifactId>gson</artifactId>
  <version>2.10.1</version>
</dependency>
```

## How to add any dependency

1. Find `groupId`, `artifactId`, `version` on https://mvnrepository.com
2. Paste inside `<dependencies>` block
3. Run `mvn compile` — Maven fetches the JAR

## Example usage in `App.java`

```java
package com.example;

import com.google.gson.Gson;

public class App {
    public static void main(String[] args) {
        Gson gson = new Gson();
        String json = gson.toJson("Hello Maven!");
        System.out.println(json);
    }
}
```

---

# Part 6: Build & Run (Maven)

```bash
cd demo-project
mvn compile
mvn test
mvn package
```

Run the JAR:

```bash
java -cp target/demo-project-1.0-SNAPSHOT.jar com.example.App
```

## Common Maven Commands

| Command | What it does |
|---------|--------------|
| `mvn compile` | Compile source code |
| `mvn test` | Run unit tests |
| `mvn package` | Build JAR in `target/` |
| `mvn clean` | Delete `target/` folder |
| `mvn clean package` | Clean + rebuild |
| `mvn dependency:tree` | Show dependency tree |

---

# Part 7: Migrate to Gradle

## Step 1: Remove Maven files

In `demo-project/`:

- Delete `pom.xml`
- Keep `src/` folder

---

# Part 8: Install Gradle

1. Download binary-only ZIP from https://gradle.org/releases/ (Gradle 8.x+)
2. Extract to: `C:\Gradle\gradle-8.x.x`
3. Open **CMD as Administrator**:

```bash
setx GRADLE_HOME "C:\Gradle\gradle-8.x.x" /M
setx PATH "%PATH%;%GRADLE_HOME%\bin" /M
```

4. Restart CMD and verify:

```bash
gradle -v
```

---

# Part 9: Create Gradle Files

## `settings.gradle`

```groovy
rootProject.name = 'demo-project'
```

## `build.gradle`

```groovy
plugins {
    id 'java'
}

group = 'com.example'
version = '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'com.google.code.gson:gson:2.10.1'
    testImplementation 'junit:junit:4.13.2'
}

java {
    sourceCompatibility = JavaVersion.VERSION_21
    targetCompatibility = JavaVersion.VERSION_21
}
```

> If you get version errors, use `JavaVersion.VERSION_17` instead.

---

# Part 10: Build & Run (Gradle)

```bash
cd demo-project
gradle build
gradle test
```

Run the JAR:

```bash
java -cp build/libs/demo-project-1.0-SNAPSHOT.jar com.example.App
```

## Common Gradle Commands

| Command | What it does |
|---------|--------------|
| `gradle build` | Compile, test, package |
| `gradle test` | Run tests only |
| `gradle clean` | Delete `build/` folder |
| `gradle clean build` | Clean + rebuild |
| `gradle dependencies` | Show dependency tree |

---

# Part 11: Maven vs Gradle — Dependencies

| Maven (`pom.xml`) | Gradle (`build.gradle`) |
|-------------------|-------------------------|
| `implementation` scope (default) | `implementation 'group:artifact:version'` |
| `test` scope | `testImplementation 'group:artifact:version'` |

**Maven:**

```xml
<dependency>
  <groupId>com.google.code.gson</groupId>
  <artifactId>gson</artifactId>
  <version>2.10.1</version>
</dependency>
```

**Gradle:**

```groovy
implementation 'com.google.code.gson:gson:2.10.1'
```

---

# Quick Reference

| Tool | Verify | Output JAR path |
|------|--------|-----------------|
| Maven | `mvn -v` | `target/demo-project-1.0-SNAPSHOT.jar` |
| Gradle | `gradle -v` | `build/libs/demo-project-1.0-SNAPSHOT.jar` |

| Variable | Example value |
|----------|---------------|
| `JAVA_HOME` | `C:\Program Files\Java\jdk-21` |
| `MAVEN_HOME` | `C:\Program Files\Apache\Maven\apache-maven-3.9.6` |
| `GRADLE_HOME` | `C:\Gradle\gradle-8.x.x` |
