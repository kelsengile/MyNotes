[Previous](./[14]-Rewriting-History.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[16]-Git-Best-Practices.md)

*Maintaining History*

# Lesson 15 - Submodules And Monorepos

## 15.1 What Is A Submodule

A submodule is a Git repository embedded inside another Git repository as a subdirectory, keeping its own independent history while still being referenced, at a specific commit, from the parent repository. This is useful when a project depends on another project's source code directly — a shared library maintained in its own repository, for instance — and needs to pin to a specific, known-good version of it rather than duplicating the code or depending on it being installed separately.

```
my-app/
├── src/
├── libs/
│   └── shared-ui/     <- its own repo, pinned at a specific commit
└── .gitmodules
```

---

## 15.2 Working With Submodules

A submodule is added with `git submodule add <url> <path>`, which clones the target repository into the given path and records exactly which commit it's pinned to in the parent repository. Because a plain `git clone` of the parent repository doesn't automatically download submodule contents, collaborators typically need an extra step (`git submodule update --init --recursive`) to fully populate them. Updating a submodule to a newer commit of the dependency, and committing that updated reference in the parent repository, is how the parent project consciously "upgrades" its pinned dependency version over time.

```bash
git submodule add https://github.com/example/shared-ui.git libs/shared-ui
git commit -m "Add shared-ui as a submodule"

# a collaborator cloning the parent repo:
git clone https://github.com/example/my-app.git
git submodule update --init --recursive

# upgrading the pinned version later:
cd libs/shared-ui
git pull origin main
cd ../..
git add libs/shared-ui
git commit -m "Upgrade shared-ui to latest main"
```

---

## 15.3 Monorepos

A monorepo takes the opposite approach to organizing multiple related projects: rather than splitting them into separate repositories linked by submodules, a monorepo keeps many projects (or many services within a larger system) together in a single repository with a single shared history. This makes it trivial to make coordinated changes across multiple projects in one atomic commit, and to see the full history of how they've evolved together, but it can require specialized tooling to keep build and test times reasonable as the repository grows very large, since naive tools may try to process the entire codebase for every change.

```
company-monorepo/
├── services/
│   ├── billing/
│   ├── notifications/
│   └── api-gateway/
├── packages/
│   └── shared-types/
└── .git/            <- one shared history for everything above
```

A single commit here could update `shared-types` and both services that depend on it, all atomically.

---

## 15.4 Choosing An Approach

Whether to split a codebase into multiple repositories (potentially linked with submodules) or keep it together as a monorepo depends heavily on team structure and how tightly the pieces are related. Submodules and multi-repo setups fit naturally when different teams or organizations own genuinely separate projects with their own release cycles and need clear boundaries between them. Monorepos fit well when a single team or organization owns several closely related, frequently co-changing pieces and values the simplicity of coordinated changes and shared tooling over strict separation. Many large organizations use a mix of both, depending on how tightly coupled a given set of projects actually is.

| Situation | Better fit |
|---|---|
| Independent teams, separate release cycles | Multiple repos (with submodules if code is shared) |
| One team, tightly coupled services that change together | Monorepo |

[Previous](./[14]-Rewriting-History.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[16]-Git-Best-Practices.md)
