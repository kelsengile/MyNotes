[Previous](./[15]-Submodules-And-Monorepos.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[17]-Version-Control-Hosting-Platforms.md)

*Best Practices And Beyond*

# Lesson 16 - Git Best Practices

## 16.1 Commit Hygiene

Good commit hygiene means keeping commits small, focused, and self-contained — each commit should represent one logical change that could, in principle, be understood, reviewed, or reverted on its own. Avoid bundling unrelated changes (a bug fix and an unrelated refactor) into a single commit, and avoid the opposite extreme of committing so granularly that individual commits don't represent anything meaningful on their own. Combined with the clear commit messages discussed in Lesson 4, good commit hygiene turns a project's history into a genuinely useful resource rather than noise to scroll past.

---

## 16.2 Branch Naming Conventions

Consistent branch naming makes it immediately obvious what a branch is for and helps tooling (like automated checks tied to branch name patterns) work reliably. Common conventions prefix a branch name with its purpose, such as `feature/`, `fix/`, `chore/`, or `hotfix/`, followed by a short, descriptive, hyphenated name — for example, `fix/login-timeout` or `feature/dark-mode`. Some teams also include a ticket or issue number, like `feature/PROJ-482-dark-mode`, to link the branch directly back to its tracked task. The specific convention matters less than the team consistently following whatever convention it chooses.

---

## 16.3 .gitignore And Sensitive Data

Beyond keeping build artifacts and dependency folders out of a repository (Lesson 3), `.gitignore` plays a critical security role in keeping sensitive data — API keys, passwords, private certificates, local environment configuration — from ever being committed in the first place. Because Git's history is effectively permanent and widely copied once shared, a secret accidentally committed and later "removed" typically still exists in earlier commits and must be treated as compromised and rotated, not just deleted from the latest version. It's far cheaper to prevent secrets from being committed at all — through `.gitignore`, environment variable files kept outside version control, and pre-commit scanning tools — than to clean them up after the fact.

---

## 16.4 Protecting Important Branches

Most hosting platforms let a repository's owners configure branch protection rules on important branches (typically `main` or a release branch), enforcing requirements like mandatory code review before merging, passing automated tests, or disallowing direct pushes and force-pushes entirely. These protections turn the informal best practices covered throughout this Topic — small reviewed changes, a stable main branch, careful history rewriting — into rules the platform actually enforces, rather than conventions that rely purely on every contributor remembering and following them correctly.

[Previous](./[15]-Submodules-And-Monorepos.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[17]-Version-Control-Hosting-Platforms.md)
