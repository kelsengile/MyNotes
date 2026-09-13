[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)

# Gradle

Gradle is a build automation tool for Java, Kotlin, and Android projects that also manages dependencies. Rather than a single command-line install, dependencies are declared in a build script and Gradle downloads and wires them up as part of building your project.

Download [https://gradle.org/install/](https://gradle.org/install/)

---

## What Is Gradle?

Gradle reads a `build.gradle` (or `build.gradle.kts` for Kotlin) file describing your project's dependencies and how to build it. It downloads dependencies from a repository like Maven Central, caches them locally, and compiles your project — all through one `gradle` command.

---

## Core Commands

```bash
./gradlew build              # Build the project (downloads dependencies as needed)
./gradlew run                # Run the project's main application
./gradlew test               # Run tests
./gradlew dependencies       # Show the full dependency tree
./gradlew clean              # Remove build output
```

`gradlew` is the "Gradle Wrapper" — a script committed to the project so everyone builds with the exact same Gradle version, without installing Gradle globally.

---

## Declaring Dependencies

Dependencies go in `build.gradle`, under a `dependencies` block:

```groovy
dependencies {
    implementation 'com.google.guava:guava:32.1.2-jre'
    testImplementation 'junit:junit:4.13.2'
}
```

`implementation` means the dependency is used in production code; `testImplementation` scopes it to tests only.

---

## Repositories

Gradle needs to know where to download dependencies from — usually Maven Central:

```groovy
repositories {
    mavenCentral()
}
```

---

## Example Walkthrough

```bash
./gradlew build
./gradlew test
./gradlew run
```

Downloads dependencies and compiles the project, runs its test suite, then runs the application.

[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)
