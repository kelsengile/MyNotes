[Previous](./%5B7%5D-Local-vs-Global-vs-Project-Level-Packages.md) | [Table of Contents](./[0]-Introduction-to-Package.md) | [Next](./[9]-Creating-Your-Own-Package.md)

---

# Lesson 8 - Package Registries

## 8.1 What Is a Registry?

A **registry** is a server (or network of servers) that hosts packages so package managers can search for, download, and publish them. When you run `npm install`, npm is talking to the npm registry behind the scenes to fetch the package you asked for.

---

## 8.2 Major Public Registries

| Language | Registry | URL |
|---|---|---|
| JavaScript | npm registry | npmjs.com |
| Python | PyPI (Python Package Index) | pypi.org |
| Rust | crates.io | crates.io |
| Ruby | RubyGems | rubygems.org |
| Java | Maven Central | central.sonatype.com |
| Go | Go Module Proxy | proxy.golang.org |
| PHP | Packagist | packagist.org |
| .NET | NuGet Gallery | nuget.org |

---

## 8.3 What a Registry Actually Stores

For each published package, a registry typically stores:

- Every published version of the package
- The package's manifest metadata (name, description, license, dependencies)
- The actual code, usually as a compressed archive (tarball)
- Download/checksum information for integrity verification
- Sometimes download statistics, README content, and links to source repositories

---

## 8.4 How Package Managers Talk to Registries

When you run an install command, the package manager:

1. Looks up the package name in the registry's index
2. Finds a version that satisfies your version range
3. Downloads the package archive
4. Verifies its integrity (checksum)
5. Extracts it into your project (or dependency cache)
6. Repeats for every dependency in the tree

---

## 8.5 Searching a Registry

Most registries have a web UI for browsing and searching, plus a CLI equivalent:

```bash
npm search http-client
pip index versions requests    # (support varies by pip version)
```

Browsing a registry's website is often the easiest way to evaluate a package before installing it — check its download count, last update date, open issues, and documentation quality.

---

## 8.6 Registry Mirrors and Proxies

Large organizations sometimes run a **mirror** or **proxy** of a public registry — a local cache that speeds up installs and provides a fallback if the public registry is temporarily unavailable. This is different from a fully private registry (covered in file [12]), which hosts packages that were never published publicly at all.

---

## 8.7 Trust and the Public Registry Model

Anyone can publish to most public registries, which is part of what makes ecosystems like npm and PyPI so large — but it also means quality and safety vary widely. Some things worth checking before adopting a package:

- How many other projects depend on it (popularity/adoption)
- How recently it was updated
- Whether it has open, unresolved security advisories
- Whether the maintainer is a known individual, team, or organization

(File [13] covers security and supply-chain risk in more depth.)

---

[Previous](./%5B7%5D-Local-vs-Global-vs-Project-Level-Packages.md) | [Table of Contents](./[0]-Introduction-to-Package.md) | [Next](./[9]-Creating-Your-Own-Package.md)
