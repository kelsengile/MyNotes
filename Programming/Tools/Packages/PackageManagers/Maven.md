[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)

# Maven

Maven is a build tool and dependency manager for Java projects. Like Gradle, it doesn't install packages globally through a single command — instead, it reads a project configuration file and handles dependencies as part of building the project.

Download [https://maven.apache.org/install.html](https://maven.apache.org/install.html)

---

## What Is Maven?

Maven projects are configured through a `pom.xml` ("Project Object Model") file, which declares dependencies, the build process, and project metadata. Maven downloads declared dependencies from a repository (usually Maven Central) and caches them in a local `.m2` folder.

---

## Core Commands

```bash
mvn compile               # Compile the project's source code
mvn test                  # Run tests
mvn package               # Compile and package into a .jar or .war file
mvn install               # Install the package into your local repository
mvn clean                 # Remove build output
mvn dependency:tree       # Show the full dependency tree
```

---

## Declaring Dependencies

Dependencies are listed inside `pom.xml`:

```xml
<dependencies>
  <dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>32.1.2-jre</version>
  </dependency>
</dependencies>
```

Each dependency is identified by a `groupId`, `artifactId`, and `version` — together these point to an exact package in the repository.

---

## Maven vs. Gradle

Both build Java projects and manage dependencies from Maven Central. Maven configuration is XML-based and more rigid; Gradle uses a Groovy or Kotlin script, which is more flexible but has a steeper learning curve. Many Java projects today use one or the other, not both.

---

## Example Walkthrough

```bash
mvn clean
mvn package
java -jar target/my-app-1.0.jar
```

Cleans previous build output, compiles and packages the project into a `.jar`, then runs it.

[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)
