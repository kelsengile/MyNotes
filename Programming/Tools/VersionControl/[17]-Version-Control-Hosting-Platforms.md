[Previous](./[16]-Git-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[18]-Choosing-And-Exploring-Version-Control-Tools.md)

*Best Practices And Beyond*

# Lesson 17 - Version Control Hosting Platforms

## 17.1 GitHub

GitHub is the most widely used Git hosting platform, offering remote repository hosting alongside pull requests, code review tools, issue tracking, project boards, and automation through GitHub Actions. Its enormous user base makes it the default home for most open-source projects, and features like forking (Lesson 8) and pull requests are deeply built into how the platform expects collaboration to happen. Beyond code hosting, GitHub has become a broader hub for developer collaboration, documentation, and even package distribution.

A minimal GitHub Actions workflow that runs tests on every push, for example, might look like:

```yaml
name: Run Tests
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm install
      - run: npm test
```

---

## 17.2 GitLab

GitLab offers similar core functionality to GitHub — repository hosting, merge requests (GitLab's term for pull requests), issue tracking, and built-in CI/CD pipelines — but places a strong emphasis on being a complete, end-to-end DevOps platform covering the entire software delivery lifecycle in one product. GitLab is also notable for offering a fully-featured self-hosted (on-premises) version alongside its cloud-hosted offering, which appeals to organizations with strict requirements about where their code and infrastructure live.

---

## 17.3 Bitbucket

Bitbucket, developed by Atlassian, integrates tightly with Atlassian's other tools, particularly Jira for issue tracking and Confluence for documentation, making it a natural choice for teams already invested in that ecosystem. It supports the same core Git hosting, pull request, and pipeline features as its competitors, with its main differentiator being how seamlessly it connects to the rest of the Atlassian product suite rather than any fundamentally different approach to version control itself.

| Platform | Term for pull request | Standout feature |
|---|---|---|
| GitHub | Pull request | Largest open-source community, GitHub Actions |
| GitLab | Merge request | End-to-end DevOps platform, strong self-hosted option |
| Bitbucket | Pull request | Deep Jira and Confluence integration |

---

## 17.4 Self-Hosted Options

Organizations that want full control over their infrastructure, need to meet strict compliance or data residency requirements, or simply prefer not to depend on a third-party platform can self-host their own Git server instead of using a cloud platform. Options range from self-hosted versions of GitLab and Bitbucket to lighter-weight tools like Gitea, or even a bare Git server with no web interface at all, relying purely on SSH access. Self-hosting trades the convenience and integrations of a managed platform for complete control over the repository's infrastructure, security, and long-term availability.

A bare Git server with no web interface is really just:

```bash
# on the server
git init --bare /srv/git/my-project.git

# on a developer's machine
git remote add origin ssh://user@server:/srv/git/my-project.git
git push origin main
```

[Previous](./[16]-Git-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[18]-Choosing-And-Exploring-Version-Control-Tools.md)
