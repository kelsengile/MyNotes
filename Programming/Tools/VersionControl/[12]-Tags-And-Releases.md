[Previous](./[11]-Stashing-And-Cherry-Picking.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[13]-Undoing-Changes.md)

*Advanced Techniques*

# Lesson 12 - Tags And Releases

## 12.1 What Is A Tag

A tag is a fixed, permanent reference to a specific commit, typically used to mark a meaningful point in a project's history, like a published release. Unlike a branch, which moves forward automatically as new commits are added, a tag always points to exactly the same commit once created — `git tag v1.2.0` marks the current commit permanently as `v1.2.0`, giving that specific state of the project a memorable, stable name that can be referred to long after other work has moved on.

---

## 12.2 Lightweight vs Annotated Tags

Git supports two kinds of tags. A **lightweight tag** is simply a name pointing directly at a commit, with no additional information — quick to create, but with little more than the pointer itself. An **annotated tag** is stored as its own full object in the repository, including the tagger's name, date, and a message, similar to a commit, and can even be cryptographically signed to verify its authenticity. Annotated tags (created with `git tag -a v1.2.0 -m "Release 1.2.0"`) are generally recommended for marking real releases, since they carry meaningful metadata about the release itself.

---

## 12.3 Semantic Versioning

Semantic versioning is a widely adopted convention for naming releases as `MAJOR.MINOR.PATCH` (for example, `2.4.1`), where each number communicates something specific about the change. The **major** version increases for incompatible, breaking changes; the **minor** version increases for new, backward-compatible functionality; and the **patch** version increases for backward-compatible bug fixes. Following this convention lets anyone depending on a project understand, just from the version number, how significant an update is and whether it's likely to require any changes on their end.

---

## 12.4 Creating Releases

Beyond a plain Git tag, most hosting platforms offer a dedicated "release" feature built on top of tags, letting a maintainer attach release notes, compiled binaries, or other downloadable assets to a specific tagged version. Publishing a release typically means finalizing the code for that version, tagging the corresponding commit, writing release notes summarizing what changed since the last release, and publishing it through the hosting platform so users and downstream projects can find and depend on that specific, stable version rather than the ever-moving main branch.

[Previous](./[11]-Stashing-And-Cherry-Picking.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[13]-Undoing-Changes.md)
