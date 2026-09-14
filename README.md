# Agent Skills

This repository was created with inspiration from [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) and [mattpocock/skills](https://github.com/mattpocock/skills).

---

## Skills

### 1. `intent-discovery`
**When:** The user request is ambiguous, lacks constraints, or says "interview me."

Extracts what the user *actually* wants before any code is written. Uses a one-question-at-a-time interview loop until ~95% confidence. Produces a 6-line Intent summary (Outcome, User, Why now, Success, Constraint, Out of scope) that must be explicitly confirmed before work begins.

Key rules: no batching questions, no producing specs before confirmation, Fast-Track requires all 4 criteria to be *explicitly written* in the request.

---

### 2. `domain-modeling`
**When:** Discussing terminology, resolving naming conflicts, or making a hard-to-reverse architectural decision.

Builds and maintains `CONTEXT.md` — the project's single source of truth for language. Also governs Architecture Decision Records (ADRs): when to write them, how to format them, and the immutability rule (old ADRs are never edited, only superseded).

Key rules: update `CONTEXT.md` immediately when a term is resolved, only write an ADR when the decision is hard-to-reverse, surprising, and involves a real trade-off.

---

### 3. `codebase-design`
**When:** Designing a new module, API, or deciding where to place a system boundary.

Enforces deep module design (small interface, large implementation), dependency injection, and explicit seam definition. Guides where to place boundaries so the system stays testable and changeable.

Key rules: prefer deep modules over shallow ones, never let a module reach into another's internals, define the public seam before writing implementation.

---

### 4. `test-driven-development`
**When:** Implementing a feature, modifying logic, or writing a regression test for a bug fix.

Enforces the Red-Green-Refactor loop. Tests are written *at the seam* before implementation code. No writing tests after the fact (exception: throwaway spike code).

Key rules: write the failing test first, make it pass with the minimum code, then refactor. Never skip Red.

---

### 5. `diagnosing-bugs`
**When:** A bug is reported, a test fails, the build breaks, or behavior doesn't match expectations.

Follows a 6-phase systematic loop: build a tight feedback loop → minimize reproduction → hypothesize (3–5 ranked options) → instrument & localize → fix with a regression test → cleanup. Never guesses without a reproducible failure signal.

Key rules: establish a red test *before* forming hypotheses, fix root causes not symptoms, remove all `[DEBUG-...]` tags before closing.

---

### 6. `code-review`
**When:** Reviewing a PR, checking existing code, or evaluating another agent's output.

Evaluates code on two independent axes: **Spec** (does it do what was asked?) and **Standards** (is the architecture clean?). Every comment carries a severity label: `Critical`, *(none)*, `Optional`, `Nit`, or `Praise`.

Security is a first-class axis (OWASP baseline): injection, authz, XSS, secrets, and dependencies are checked on every review.

Key rules: prioritize the top 3 issues, never bury real problems under nits, always include Praise for genuinely good work.

---

### 7. `git-conventions`
**When:** About to run `git commit`, create a branch, open a PR, or asked about naming.

Enforces Conventional Commits format (`type(scope): summary`), atomic commits, branch naming (`feat/`, `fix/`, `chore/`, etc.), and a pre-commit hygiene gate (diff review, secret scan, debug tag removal).

After every commit, the agent outputs a structured **Change Summary**: what changed, what was deliberately left untouched, and any potential concerns.

Key rules: one commit = one logical change, commit early as "save points," no WIP commits merged, no vague messages.

---

### 8. `deployment`
**When:** Configuring a pipeline, triggering a deploy, debugging CI, or discussing rollback and observability.

Defines the full pipeline contract (6 mandatory gates: build, unit, integration, lint, secret scan, CVE audit), environment promotion model (`dev → staging → production`, one direction only), rollback procedures per deploy type, and observability requirements (structured logs, error rate metric, alert threshold, health check).

Includes the **"Day 2" Rule**: a deploy is not complete until `.env.example`, `README.md`, and `CONTEXT.md` reflect the current state of the system.

Key rules: no gate may be bypassed, define rollback *before* deploying, no shipping on Fridays, a feature without observability is not done.
