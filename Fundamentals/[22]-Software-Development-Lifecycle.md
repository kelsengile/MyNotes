[Previous](./[21]-Deployment-DevOps-Basics.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[23]-Supporting-Math-Foundations-optional.md)

# Lesson 22 - Software Development Lifecycle

## 22.1 Requirements & Planning

Before a single line of code is written, most successful software projects invest time understanding *what* needs to be built and *why* — poor requirements are one of the most common root causes of project failure, far more so than poor coding.

### The Software Development Lifecycle (SDLC) — A Bird's-Eye View

Requirements and planning are typically the first phase in a broader lifecycle:

```
Requirements → Design → Implementation → Testing → Deployment → Maintenance
     ▲                                                                │
     └────────────────────────── (feedback loops back) ───────────────┘
```

Different methodologies (Section 22.2) structure how these phases relate — sequentially (Waterfall) or iteratively/continuously (Agile) — but nearly all software passes through some version of these stages.

### Types of Requirements

- **Functional requirements** — what the system should *do*: specific features, behaviors, and capabilities (e.g., "users can reset their password via email").
- **Non-functional requirements** — *how well* the system should do it: performance, scalability, security, availability, usability, maintainability (e.g., "the login page must respond within 200ms at p95," "the system must support 10,000 concurrent users").
- **Business requirements** — the high-level goals and constraints driving the project (e.g., regulatory compliance, revenue targets, market positioning).
- **Constraints** — fixed limitations the solution must work within (budget, deadline, required technology stack, legal/regulatory rules).

### Gathering Requirements

- **Stakeholder interviews** — talking directly with the people who will use or are funding the software to understand their actual needs (which are sometimes different from what they initially ask for).
- **User stories** — short, structured descriptions of a feature from an end user's perspective, commonly in the format:
  > *"As a [type of user], I want [some goal], so that [some benefit/reason]."*
  >
  > Example: *"As a returning customer, I want to save my shipping address, so that I don't have to re-enter it on every order."*
- **Use cases** — more detailed, step-by-step descriptions of how a user interacts with the system to accomplish a specific goal, often including alternate/error paths.
- **Acceptance criteria** — specific, testable conditions that define when a requirement/user story is considered "done" and correctly implemented.
  ```
  User story: As a user, I want to reset my password via email.

  Acceptance criteria:
  - Given a valid registered email, a reset link is sent within 1 minute
  - The reset link expires after 30 minutes
  - An invalid/unregistered email shows a generic confirmation (no account enumeration)
  - The new password must meet the site's password policy
  ```
- **Prototyping/mockups** — low-fidelity sketches or interactive wireframes that let stakeholders react to a concrete idea before real development effort is invested, surfacing misunderstandings early and cheaply.

### Common Pitfalls in Requirements Gathering

- **Ambiguity** — vague requirements ("the app should be fast" or "make it user-friendly") that different people interpret differently; push for specific, measurable criteria.
- **Scope creep** — the gradual, uncontrolled expansion of a project's requirements beyond what was originally planned, often without corresponding adjustments to timeline or budget.
- **Gold-plating** — adding unrequested features or excessive polish beyond what's actually needed, consuming time/budget without proportional value.
- **Assuming instead of asking** — filling gaps in unclear requirements with assumptions rather than clarifying with stakeholders, risking building the wrong thing entirely.
- **Requirements that describe implementation, not need** — a stakeholder saying "add a dropdown menu" may actually need "let users filter results," and fixating on the literal ask can foreclose better solutions.

### Planning

Once requirements are reasonably understood, planning translates them into an actionable path forward.

- **Scoping** — deciding what will (and explicitly will *not*) be included in a given release/milestone, to keep the effort bounded and achievable.
- **Estimation** — predicting how long tasks will take and what resources they'll require. Common techniques:
  - **Story points** — a relative, unitless measure of effort/complexity/uncertainty (often using a Fibonacci-like scale: 1, 2, 3, 5, 8, 13...) rather than a direct time estimate, which tends to be more consistent across a team over time.
  - **T-shirt sizing** — a coarse relative estimate (S/M/L/XL) useful for early, high-level planning before details are known.
  - **Three-point estimation** — combining optimistic, pessimistic, and most-likely estimates into a weighted average to account for uncertainty.
- **Prioritization** — deciding what to build first, since rarely can everything be built at once. Common frameworks:
  - **MoSCoW** — Must have, Should have, Could have, Won't have (this time).
  - **Value vs. effort matrix** — prioritizing items that deliver high value for relatively low implementation effort.
  - **RICE scoring** — Reach, Impact, Confidence, Effort — a more quantitative prioritization framework often used in product management.
- **Risk assessment** — identifying what could go wrong (technical unknowns, external dependencies, unclear requirements) early, so mitigation plans can be made before they become emergencies.
- **Roadmapping** — a higher-level, longer-term view of planned work and milestones, communicating direction to stakeholders without committing to the fine-grained detail of a sprint/iteration plan.

### Documentation Artifacts

Depending on project size and methodology, planning may produce: a **Product Requirements Document (PRD)**, a **backlog** of prioritized work items, **technical design documents** for significant features, and **architecture decision records (ADRs)** capturing the reasoning behind significant technical choices for future reference.

---

## 22.2 Agile, Scrum, Kanban Basics

### Waterfall (For Contrast)

The traditional, sequential model: requirements → design → implementation → testing → deployment, each phase completed fully before the next begins, with limited feedback loops back to earlier phases. Works reasonably well when requirements are well-understood and unlikely to change, but struggles to accommodate the kind of changing understanding that's common in most real-world software projects.

### Agile — The Underlying Philosophy

Agile isn't a single specific process — it's a set of values and principles (from the 2001 **Agile Manifesto**) favoring:

- **Individuals and interactions** over processes and tools.
- **Working software** over comprehensive documentation.
- **Customer collaboration** over contract negotiation.
- **Responding to change** over following a fixed plan.

In practice, this translates to: building and delivering software in small, frequent increments; continuously gathering feedback; and adapting plans as understanding improves, rather than trying to nail down every detail upfront. **Scrum** and **Kanban** are two of the most widely used concrete frameworks that implement Agile principles.

### Scrum

A structured framework organizing work into fixed-length iterations called **sprints**.

**Key elements:**

| Element | Description |
|---|---|
| **Sprint** | A fixed time period (commonly 1–4 weeks) during which a defined set of work is completed |
| **Product Backlog** | The full, prioritized list of everything that might be built for the product |
| **Sprint Backlog** | The specific subset of backlog items selected for the current sprint |
| **Sprint Planning** | A meeting at the start of a sprint to decide what will be worked on |
| **Daily Standup** | A brief (~15 min) daily check-in: what was done, what's next, any blockers |
| **Sprint Review** | A meeting at the end of a sprint to demo completed work to stakeholders |
| **Sprint Retrospective** | A meeting reflecting on the sprint's process itself — what went well, what to improve |

**Roles:**
- **Product Owner** — represents stakeholder/customer interests, owns and prioritizes the backlog.
- **Scrum Master** — facilitates the process, removes blockers, and helps the team follow Scrum practices (not a traditional manager role — more of a coach/facilitator).
- **Development Team** — the people actually building the product.

```
Sprint Cycle (e.g., 2 weeks):

Sprint Planning → [ Daily Standups throughout ] → Sprint Review → Retrospective → (repeat)
```

**Why teams use Scrum:** provides structure and predictable rhythm, regular checkpoints for feedback and course-correction, and clear visibility into progress — while still remaining far more adaptive than Waterfall.

### Kanban

A more continuous, flow-based approach (originating from Toyota's manufacturing practices) with fewer prescribed rituals than Scrum.

**Core elements:**
- **Kanban board** — a visual board with columns representing stages of work (e.g., "To Do," "In Progress," "In Review," "Done"), with cards representing individual work items moving left to right.
- **Work in Progress (WIP) limits** — a cap on how many items can be in a given column/stage at once, preventing the team from starting too much simultaneously and encouraging finishing work before starting new work.
- **Continuous flow** — unlike Scrum's fixed sprints, work items move through the board continuously, one at a time, as capacity allows, rather than being batched into timed iterations.
- **Pull system** — new work is *pulled* into a stage only when there's capacity (respecting WIP limits), rather than being *pushed* onto the team regardless of current load.

```
┌─────────┐  ┌─────────────┐  ┌──────────┐  ┌──────┐
│  To Do  │  │ In Progress │  │In Review │  │ Done │
│         │  │  (WIP: 3)   │  │          │  │      │
│ [card]  │  │  [card]     │  │  [card]  │  │[card]│
│ [card]  │  │  [card]     │  │          │  │[card]│
│ [card]  │  │  [card]     │  │          │  │[card]│
└─────────┘  └─────────────┘  └──────────┘  └──────┘
```

### Scrum vs. Kanban

| | Scrum | Kanban |
|---|---|---|
| Structure | Fixed-length sprints | Continuous flow, no fixed iterations |
| Roles | Defined (Product Owner, Scrum Master, Dev Team) | No prescribed roles |
| Change during a cycle | Discouraged mid-sprint (scope is locked for the sprint) | Work can be reprioritized at any time |
| Cadence | Regular ceremonies (planning, standup, review, retro) | More flexible, fewer mandatory meetings |
| Best suited for | Teams building discrete, plannable features in batches | Teams handling a continuous stream of work (e.g., support/ops, maintenance-heavy work) |
| Metric of progress | Velocity (story points completed per sprint) | Cycle time / lead time (how long an item takes to move through the board) |

Many teams use a hybrid ("Scrumban"), borrowing Scrum's planning cadence with Kanban's visual flow and WIP limits.

### Key Agile Metrics

- **Velocity** — the average amount of work (often in story points) a Scrum team completes per sprint, used to forecast how much can realistically be planned for future sprints.
- **Burndown chart** — visualizes remaining work in a sprint over time, showing whether the team is on track to complete the sprint backlog.
- **Cycle time** — how long it takes an individual work item to move from start to completion (a core Kanban metric).
- **Lead time** — the total time from when a request is made to when it's delivered, including any time spent waiting before work begins.

### Common Agile Practices Beyond Scrum/Kanban

- **Backlog grooming/refinement** — periodically reviewing and refining upcoming backlog items (clarifying requirements, adding estimates, breaking down large items) so they're ready to be worked on when their turn comes.
- **Definition of Done** — an agreed-upon checklist (code reviewed, tested, documented, deployed to staging, etc.) that must be satisfied before a work item is considered truly complete — preventing "done" from meaning different things to different people.
- **Retrospectives as continuous improvement** — treating the *process itself* as something to iterate on, not just the product, is a defining and often underappreciated aspect of Agile practice.

---

## 22.3 Maintenance & Refactoring

Software isn't "finished" at initial release — most of a system's total lifetime (and cost) is typically spent in the **maintenance** phase, long after the first version ships.

### Types of Maintenance

| Type | Purpose |
|---|---|
| **Corrective** | Fixing bugs/defects discovered after release |
| **Adaptive** | Modifying the software to work with a changed environment (new OS version, updated dependency, changed regulations) |
| **Perfective** | Improving performance, usability, or adding enhancements based on user feedback, without fixing a "bug" per se |
| **Preventive** | Proactively improving code (refactoring, updating dependencies) to reduce the likelihood of future problems, before they cause visible issues |

### Technical Debt

A metaphor for the implied future cost of choosing an expedient, short-term solution now instead of a better, more thorough approach that would take longer. Like financial debt, it's not inherently bad — sometimes it's a reasonable trade-off to ship faster — but it accrues "interest": the longer suboptimal code remains, the more it slows down future work and the riskier it becomes to change.

**Common sources of technical debt:**
- Deliberate shortcuts taken to meet a deadline, with the intent to revisit later (but often not tracked or never revisited).
- Code that was reasonable when written but hasn't kept pace with the system's evolving requirements or scale.
- Accumulated small compromises (inconsistent patterns, missing tests, unclear naming) that individually seem minor but collectively make the codebase harder to work in.
- Outdated dependencies or architectural decisions that no longer fit current needs.

**Managing technical debt:** track it explicitly (e.g., as backlog items, not just tribal knowledge), communicate its cost to stakeholders in terms they care about (velocity, risk, bug rate), and allocate consistent, ongoing time to address it rather than treating it as something to "get to eventually."

### Refactoring

**Refactoring** is the process of restructuring existing code to improve its internal structure, readability, or design — **without changing its external, observable behavior**. It's distinct from adding features or fixing bugs; a refactor should leave the software doing exactly what it did before, just implemented better.

```python
# Before refactoring: a function doing too much, hard to test/read
def process_order(order):
    total = 0
    for item in order.items:
        total += item.price * item.quantity
    if order.customer.is_premium:
        total *= 0.9
    if total > 100:
        total -= 10
    send_email(order.customer.email, f"Your total is {total}")
    return total

# After refactoring: broken into small, focused, independently testable functions
def calculate_subtotal(items):
    return sum(item.price * item.quantity for item in items)

def apply_premium_discount(total, customer):
    return total * 0.9 if customer.is_premium else total

def apply_bulk_discount(total):
    return total - 10 if total > 100 else total

def process_order(order):
    total = calculate_subtotal(order.items)
    total = apply_premium_discount(total, order.customer)
    total = apply_bulk_discount(total)
    send_email(order.customer.email, f"Your total is {total}")
    return total
```

### Common Refactoring Techniques

- **Extract function/method** — pulling a chunk of logic out into its own well-named function, improving readability and reusability (as in the example above).
- **Rename variable/function** — improving clarity by giving things names that accurately describe their purpose.
- **Remove duplication** — consolidating repeated logic into a single shared implementation (see the DRY principle, Section 17.2).
- **Simplify conditionals** — replacing deeply nested or convoluted conditional logic with clearer structures (early returns, guard clauses, polymorphism instead of large `if/elif` chains).
- **Replace magic numbers/strings with named constants** — improving clarity and making future changes safer and more centralized.
- **Introduce/extract a class** — grouping related data and behavior together when a function or module has grown to handle too many responsibilities (see Single Responsibility Principle, Section 17.2).

### Why Refactoring Matters

- **Improves maintainability** — cleaner code is faster and safer to modify, extend, and debug over time.
- **Reduces bugs** — simpler, clearer code has fewer places for mistakes to hide, and is easier for reviewers to reason about correctly.
- **Enables new features** — poorly structured code often actively resists the addition of new functionality; refactoring first can make subsequent feature work faster overall, even though it takes time upfront.
- **Keeps technical debt in check** — regular, incremental refactoring prevents debt from silently compounding into a codebase that's prohibitively expensive to change.

### Refactoring Safely

- **Have a solid test suite first** — refactoring without tests is a common way to accidentally introduce bugs while believing you're only changing structure (see Section 18); tests provide the safety net that lets you change code with confidence.
- **Make small, incremental changes** — refactor in small, verifiable steps rather than one large rewrite, testing (and potentially committing) after each step.
- **Keep refactoring separate from feature work** — mixing "restructuring this code" with "adding new behavior" in the same change makes it much harder to review, and much harder to isolate the cause if something breaks.
- **Use version control as a safety net** — commit working states frequently so it's easy to compare against or revert to a known-good point if a refactor goes wrong.

### Refactor vs. Rewrite

When a codebase has accumulated significant technical debt or no longer fits its requirements, teams face a choice:

| | Refactor (incremental) | Rewrite (from scratch) |
|---|---|---|
| Risk | Lower — the system keeps working throughout | Higher — often takes longer than expected, and the old system must be maintained in parallel |
| Business continuity | Preserved throughout the process | Disrupted; new bugs may be reintroduced that the old system had already solved |
| Effort | Spread out over time | Concentrated, large upfront investment |
| When appropriate | Most situations — even significantly troubled codebases can usually be improved incrementally | Rare — typically only when the existing technology/architecture is fundamentally incompatible with current needs |

The industry's general wisdom (echoed famously in Joel Spolsky's essay on the topic) leans strongly toward incremental refactoring over full rewrites, since full rewrites frequently underestimate the accumulated, undocumented knowledge embedded in the existing system (edge cases, bug fixes, and business rules that aren't obvious from the outside) and take significantly longer than anticipated.

### Ongoing Maintenance Practices

- **Monitor production** (see Section 21.3) to catch issues proactively, often before users report them.
- **Keep dependencies updated** regularly, in small increments, rather than facing a painful, high-risk "big bang" upgrade after years of neglect.
- **Maintain documentation** alongside code changes, so it doesn't silently drift out of sync with what the system actually does.
- **Revisit and update tests** as behavior legitimately changes, keeping the test suite a reliable, trustworthy safety net rather than a source of noisy false failures.

[Previous](./[21]-Deployment-DevOps-Basics.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[23]-Supporting-Math-Foundations-optional.md)
