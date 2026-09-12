[Previous](./[8]-Collaborative-Workflows.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[10]-Rebasing.md)

*Collaboration*

# Lesson 9 - Pull Requests And Code Review

## 9.1 What Is A Pull Request

A pull request (called a "merge request" on some platforms) is a formal proposal to merge changes from one branch into another, opened through a hosting platform like GitHub or GitLab rather than through Git itself. It packages up all the commits on a branch, shows the combined diff against the target branch, and provides a dedicated space for discussion, automated checks, and approval before anything actually gets merged. Pull requests are the mechanism that turns the feature branch and forking workflows from Lesson 8 into an actual review-and-approval process.

A pull request typically shows a title, a summary, and a checklist of automated status checks:

```
Pull Request #142: Add discount code support to checkout

Adds a "Discount code" field to the checkout form and validates
it against the promotions table before applying it to the total.

✔ build passed
✔ tests passed (48/48)
✔ 1 approval
```

---

## 9.2 The Code Review Process

Once a pull request is opened, one or more reviewers examine the proposed changes — reading the diff, checking that the logic is correct, looking for edge cases, and considering how the change fits into the broader codebase. Reviewers typically leave comments on specific lines to ask questions or request adjustments, and the author revises their branch in response; since the branch is still just a regular Git branch, new commits pushed to it automatically update the same pull request. This cycle continues until reviewers are satisfied and approve the change, at which point it's ready to merge.

A typical inline review comment, and the follow-up fix, might look like:

```
Reviewer comment on line 34:
  "What happens if `discount_code` is an empty string here?"

Author's follow-up commit:
  Handle empty discount code as 'no discount' instead of erroring
```

---

## 9.3 Review Etiquette

Good code review is a skill in its own right. As a reviewer, it helps to be specific and constructive rather than vague ("this could cause a null reference if the list is empty" is more useful than "this looks wrong"), to distinguish must-fix issues from optional suggestions, and to assume good intent rather than framing feedback as criticism of the author personally. As an author, it helps to keep pull requests reasonably small and focused, since a smaller, well-scoped change is far easier and faster to review thoroughly than a sprawling one, and to respond to feedback without treating it as a personal judgment — code review works best as a collaborative search for the best solution, not a gatekeeping exercise.

| Vague feedback | Specific, actionable feedback |
|---|---|
| "This looks wrong." | "This could throw a null reference if `list` is empty — worth a guard clause?" |
| "Style." | "Nit (optional): the rest of the file uses `snake_case` for variables." |
| "Fix this." | "Must-fix: this query isn't parameterized, which opens up SQL injection." |

---

## 9.4 Merging A Pull Request

Once a pull request is approved, it can be merged into the target branch, typically directly from the hosting platform's interface rather than the command line. Platforms usually offer a few merge strategies: a standard merge commit (preserving the full branch history, as in Lesson 6), a squash merge (combining all the branch's commits into a single commit on the target branch, producing a cleaner history), or a rebase merge (replaying the branch's commits individually on top of the target branch, discussed further in Lesson 10). After merging, the source branch is typically deleted, since its work now lives permanently in the target branch's history.

```
Before merge (3 commits on the branch):        After squash merge:
  wip                                            Add discount code support to checkout (#142)
  fix typo
  actually fix it
```

[Previous](./[8]-Collaborative-Workflows.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[10]-Rebasing.md)
