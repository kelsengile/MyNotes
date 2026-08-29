[Previous](./[28]-Build-and-Deployment-Automation.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[30]-Logging-and-Metrics.md)

*CI/CD & DevOps*

# Lesson 29 - Blue-Green & Canary Deployments

## 29.1 Blue-Green Deployments

**Blue-green deployment** maintains two identical production environments — "blue" (currently live) and "green" (the new version). You deploy and fully test the new version in the green environment while blue continues serving all real traffic. Once green is verified healthy, traffic is switched over all at once (typically at the load balancer or DNS level) from blue to green. If something goes wrong, you can instantly switch back to blue since it's still running unchanged. The trade-off is cost — you're running two full production-sized environments during the switch.

---

## 29.2 Canary Deployments

**Canary deployment** releases a new version to a small subset of traffic first (e.g. 5%), monitors it closely for errors or performance regressions, and gradually increases that percentage (25%, 50%, 100%) as confidence grows. This limits the "blast radius" of a bad release — only a small fraction of users are affected if something goes wrong, and the rollout can be automatically halted or reversed based on real production metrics before it ever reaches everyone. Canary releases require good metrics and monitoring (Lesson 30) to detect problems quickly during the rollout.

---

## 29.3 Rollbacks and Risk Mitigation

Both patterns exist to reduce deployment risk by avoiding "all traffic switches to untested code all at once." Key practices supporting safe deployments:

- **Automated health checks** — a deployment automatically halts or rolls back if error rates or latency spike beyond a threshold.
- **Fast rollback paths** — being able to revert to the previous version quickly (blue-green's instant switch-back, or simply redeploying the prior artifact version).
- **Feature flags** — decoupling code deployment from feature exposure, letting you turn a feature on/off for specific users without a new deployment at all.
- **Observability during rollout** — dashboards and alerts specifically watched during and shortly after every deployment.

Kubernetes Deployments (Lesson 25) support rolling updates natively, and can be configured to implement canary-style rollouts with additional tooling.

---

[Previous](./[28]-Build-and-Deployment-Automation.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[30]-Logging-and-Metrics.md)
