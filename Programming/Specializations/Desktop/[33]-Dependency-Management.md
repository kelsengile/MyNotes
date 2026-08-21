[Previous](./[32]-App-Architecture-Patterns.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[34]-Version-Control-with-Git.md)

*Architecture & Best Practices*

# Lesson 33 - Dependency Management & Package Managers

## 33.1 What a Package Manager Does

A package manager resolves, downloads, and tracks the libraries a project depends on — NuGet (.NET), npm (Node/Electron), Cargo (Rust/Tauri), vcpkg/Conan (C++/Qt). Each maintains a manifest (declared dependencies) and a lockfile (exact resolved versions), so any machine building the project gets an identical dependency tree.

---

## 33.2 Semantic Versioning

Most ecosystems follow **semver**: `MAJOR.MINOR.PATCH`. A patch bump fixes bugs with no API changes; a minor bump adds functionality compatibly; a major bump can break existing usage. Dependency ranges (`^2.1.0`, `~2.1.0`) tell the package manager how much version drift is acceptable — understanding these ranges prevents an "unrelated" update from silently breaking your build.

---

## 33.3 Transitive Dependencies and Conflicts

Your direct dependencies bring their own dependencies (transitive dependencies), which can occasionally conflict — two libraries requiring incompatible versions of a third. Package managers attempt automatic resolution, but sometimes require manual intervention (pinning a version, or replacing a conflicting library).

---

## 33.4 Auditing and Updating

Regularly check for outdated or vulnerable dependencies (`npm audit`, `dotnet list package --vulnerable`, `cargo audit`) rather than letting them drift indefinitely — security patches in dependencies are one of the most common ways desktop apps become exploitable (Lesson 45). Update deliberately, in small batches, running your test suite (Lesson 35) after each update rather than bumping everything at once.

[Previous](./[32]-App-Architecture-Patterns.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[34]-Version-Control-with-Git.md)
