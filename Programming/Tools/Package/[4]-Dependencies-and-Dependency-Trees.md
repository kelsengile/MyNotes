[Previous](./[3]-Installing-Updating-And-Removing-Packages.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[5]-Versioning-And-Semantic-Versioning.md)

---

# Lesson 4 - Dependencies And Dependency Trees

## 4.1 What Is a Dependency?

A **dependency** is a package that another package or project needs in order to work. If your project uses a package called `web-framework`, then `web-framework` is your **direct dependency**.

---

## 4.2 Transitive (Indirect) Dependencies

If `web-framework` itself relies on a package called `http-parser`, then `http-parser` is a **transitive dependency** of your project — you didn't install it directly, but you need it anyway because something you installed depends on it.

```
your-project
└── web-framework          (direct dependency)
    ├── http-parser         (transitive dependency)
    └── router-lib          (transitive dependency)
        └── path-matcher     (transitive dependency, two levels deep)
```

This branching structure is called a **dependency tree**. In real projects, a handful of direct dependencies can easily expand into hundreds of transitive ones.

---

## 4.3 Viewing a Dependency Tree

Most package managers can print the tree for you:

```bash
npm ls
npm ls --all         # include every transitive dependency

pip show flask        # shows direct info, not a full tree
pipdeptree             # third-party tool for a full Python dependency tree

cargo tree
```

---

## 4.4 Why Dependency Trees Get Complicated

Two different packages in your tree might depend on **different versions** of the same package. For example:

```
your-project
├── package-a → needs utils@^1.0.0
└── package-b → needs utils@^2.0.0
```

The package manager has to figure out how to satisfy both — sometimes by installing two separate copies of `utils` (npm often does this), and sometimes by failing with a conflict error if the ecosystem doesn't support multiple versions side by side (more on resolving this in file [14]).

---

## 4.5 Dependency Depth and "Dependency Bloat"

Because dependencies pull in their own dependencies, installing one package can result in dozens or hundreds of packages being downloaded. This is sometimes called **dependency bloat**, and it has real costs:

- Larger install sizes and slower installs
- More surface area for security vulnerabilities (file [13])
- Harder-to-audit codebases
- More potential for version conflicts

---

## 4.6 Peer Dependencies

Some ecosystems (notably npm) have a concept of a **peer dependency** — a package that expects *you* (not itself) to install a compatible version of another package. This is common for plugins that need to match the version of the tool they plug into (e.g. a React component library expects your project to already have React installed).

```json
{
  "peerDependencies": {
    "react": ">=18.0.0"
  }
}
```

---

## 4.7 Optional Dependencies

Some dependencies are only needed for certain features and can be skipped if unused, letting the core package stay lightweight. These are usually marked explicitly in the manifest (e.g. npm's `optionalDependencies`).

---

[Previous](./[3]-Installing-Updating-And-Removing-Packages.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[5]-Versioning-And-Semantic-Versioning.md)
