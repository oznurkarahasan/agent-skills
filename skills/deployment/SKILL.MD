---
name: deployment
description: Governs how code moves from a passing test suite to production. Covers CI pipeline contracts, environment promotion rules, rollback procedures, and observability requirements. Use when configuring CI/CD, deploying a release, or debugging a failed pipeline.
triggers:
  - "When setting up or modifying a CI/CD pipeline."
  - "When deploying to staging or production."
  - "When a deployment fails or needs to be rolled back."
---

# Deployment & CI/CD

Passing tests are a necessary condition for shipping — not a sufficient one. This skill governs the full pipeline from green test suite to live production traffic.

---

## 1. The Pipeline Contract (Non-Negotiable Gates)

Every commit that reaches `main` MUST pass all of these gates automatically before deployment begins. No gate may be bypassed manually.

| Gate | What it checks | Blocks deploy if... |
| :--- | :--- | :--- |
| **Build** | Code compiles / bundles without errors | Any error or warning-as-error |
| **Unit Tests** | Fast, isolated logic tests | Any failure |
| **Integration Tests** | DB, API, or service boundary tests | Any failure |
| **Lint / Format** | Code style and static analysis | Any violation flagged as error |
| **Secret Scan** | No credentials in the diff | Any match |
| **Dependency Audit** | Known CVEs in the dependency tree | High or Critical severity CVEs |

> **Rule:** If a gate is flaky (fails non-deterministically), fix the test — do not re-run until it passes. Flaky gates erode trust in the entire pipeline.

---

## 2. Environment Promotion Model

Code flows in one direction only: `dev → staging → production`. It never jumps stages.

```
[feature branch] → [main] → [staging] → [production]
                    (auto)     (auto)      (manual approval)
```

- **Staging** must be a production-equivalent environment: same infra, same config shape, real (sanitized) data or realistic fixtures.
- **Production deploys** require a human approval step. Fully automated production deploys are only acceptable with a mature rollback system and sub-1% error rate history.
- **Never deploy on Fridays** or before a holiday unless a critical security patch demands it.

---

## 3. Pre-Deployment Checklist

Before triggering a production deploy, verify:

- [ ] All pipeline gates are green on the exact commit being deployed.
- [ ] The change has been running on staging for a minimum soak time (e.g., 30 minutes for low-risk, 24h for schema migrations).
- [ ] A rollback path is defined and tested (see §4).
- [ ] A monitoring dashboard or alert is open and ready.
- [ ] On-call engineer is notified if the change is high-risk.

---

## 4. Rollback Procedure

Define the rollback strategy *before* deploying, not after something breaks.

| Deploy type | Rollback method |
| :--- | :--- |
| Stateless service | Redeploy previous image tag / git SHA |
| DB schema migration | Forward-only migration with a compensating migration already written |
| Feature flag rollout | Toggle flag to `off` in config/flag service |
| Infra change (Terraform/Pulumi) | `plan` the revert first, then `apply` |

**Rule:** If there is no defined rollback path, the deploy is not ready. Block it.

---

## 5. Observability Requirements

A feature is not "done" until it can be observed in production. Before shipping, verify:

- **Structured Logs:** Key operations emit JSON-structured logs with a correlation/trace ID.
- **Error Rate Metric:** A metric exists for the error rate of the new code path.
- **Alert Threshold:** An alert fires if the error rate exceeds the baseline (e.g., > 1% over 5 minutes).
- **Health Check:** The service's `/health` or equivalent endpoint reflects the new component's status.

If none of these exist and the feature is non-trivial, add them as part of the same PR — not a follow-up ticket.

---

## 6. Red Flags

- Merging directly to `main` and bypassing CI to "save time."
- Deploying a DB migration with no rollback migration written.
- "It works on my machine" as a justification to skip staging.
- Disabling a failing test to unblock a deploy.
- Shipping a feature with no logs, no metrics, and no alert.
- A pipeline that takes longer than 15 minutes without parallelization being investigated.

---

## 7. Documentation & Handoff (The "Day 2" Rule)

Deploying the code is only half the job. The project must be handed off cleanly so another developer (or agent) can run it tomorrow.

Before declaring a deployment or major feature complete, the agent MUST verify:
- **Environment Variables:** All required secrets and API keys are documented in a `.env.example` file (with dummy values, never real secrets).
- **README.md State:** The setup instructions, build commands, and deployment steps are accurate and up-to-date.
- **Architectural Drift:** If a new database, external service, or major library was added, `CONTEXT.md` or the README is updated to reflect this.

**Rule:** Do not leave a project in a "works only on my machine" state. If the setup steps in the README no longer work after your changes, you have broken the build.