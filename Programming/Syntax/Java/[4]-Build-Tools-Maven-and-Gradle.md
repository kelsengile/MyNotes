[Previous](./[3]-How-Java-Works.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[5]-Variables-and-Data-Types.md)

*Getting Started*

# Lesson 4 - Build Tools & Dependency Management (Maven, Gradle)

Real Java projects rarely consist of a single file compiled by hand. This lesson introduces the tools that manage compilation, dependencies, and packaging for you.

## 4.1 Why Build Tools?

As a project grows, you need to: compile many files in the right order, pull in third-party libraries, run tests, and package everything into a distributable artifact. Doing this manually with `javac` quickly becomes unmanageable. **Build tools** automate this entire process from a single command.

---

## 4.2 Project Structure Conventions

Both major Java build tools follow a **standard directory layout**, so tooling can find your code without extra configuration:

```
src/
  main/
    java/        <- your application source code
    resources/   <- config files, non-code assets
  test/
    java/        <- test source code
```

Following this convention (rather than inventing your own folder layout) is expected in nearly every Java project you'll encounter.

---

## 4.3 Maven Basics (pom.xml)

**Maven** describes a project using an XML file called `pom.xml` ("Project Object Model"). It lists your project's dependencies, plugins, and build configuration:

```xml
<project>
  <groupId>com.example</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0.0</version>
  <dependencies>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>5.10.0</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

Common commands: `mvn compile`, `mvn test`, `mvn package`.

---

## 4.4 Gradle Basics (build.gradle)

**Gradle** is a newer, more flexible build tool that uses a Groovy or Kotlin-based script (`build.gradle` or `build.gradle.kts`) instead of XML:

```groovy
plugins {
    id 'java'
}

dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter:5.10.0'
}
```

Common commands: `gradle build`, `gradle test`, `gradle run`.

---

## 4.5 Choosing Between Maven and Gradle

Both tools solve the same core problem — dependency management and build automation — but differ in style:

| | Maven | Gradle |
|---|---|---|
| Config format | XML | Groovy/Kotlin script |
| Verbosity | More verbose | More concise |
| Performance | Slower (less caching) | Faster (incremental builds) |
| Common in | Traditional enterprise apps | Android, modern Spring apps |

As a beginner, it's worth being comfortable reading both, since you'll encounter each in different codebases. Later in this course, the [Build Tools](./[104]-Packaging-and-Distribution.md) and Spring lessons will use these tools directly.

---

[Previous](./[3]-How-Java-Works.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[5]-Variables-and-Data-Types.md)