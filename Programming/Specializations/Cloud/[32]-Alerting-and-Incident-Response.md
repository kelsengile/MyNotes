[Previous](./[31]-Distributed-Tracing.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[33]-Cloud-Security-Fundamentals.md)

*Monitoring & Observability*

# Lesson 32 - Alerting & Incident Response

## 32.1 Alerting Basics

An **alert** automatically notifies a human when a metric crosses a defined threshold or an anomaly is detected — e.g. error rate above 5%, latency above 500ms, disk usage above 90%. Alerts are typically configured on top of the metrics covered in Lesson 30, and route notifications through channels like email, SMS, Slack, or a dedicated on-call tool (e.g. PagerDuty, Opsgenie). Good alerting balances sensitivity against **alert fatigue** — too many low-value alerts train responders to ignore them, which is dangerous when a real incident occurs. Alerts should generally be **actionable**: if there's nothing a person can or should do about it, it likely shouldn't page someone.

---

## 32.2 On-call and Incident Response

An **on-call rotation** designates who is responsible for responding to alerts at any given time, usually rotating among team members on a schedule. When a significant alert fires, it typically triggers an **incident** — a structured response process:

1. **Detect** — an alert or user report signals something is wrong.
2. **Triage** — assess severity and assemble the right people.
3. **Mitigate** — take immediate action to reduce user impact (e.g. rollback, failover, scale up), even before the root cause is fully understood.
4. **Resolve** — confirm the issue is fully fixed.

Severity levels (e.g. SEV1 = critical outage, SEV3 = minor issue) help set expectations for response urgency and who needs to be involved.

---

## 32.3 Postmortems

After an incident is resolved, a **postmortem** (or "retrospective") documents what happened, why, and what will change to prevent recurrence. A good postmortem is **blameless** — it focuses on systemic and process failures rather than individual fault, since blaming people discourages honest reporting and open discussion in the future. Typical postmortem sections include a timeline of events, root cause analysis, customer/business impact, and concrete follow-up action items with owners and deadlines. Treating incidents as learning opportunities, rather than events to be minimized or hidden, is a core part of a mature operational culture.

---

[Previous](./[31]-Distributed-Tracing.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[33]-Cloud-Security-Fundamentals.md)
