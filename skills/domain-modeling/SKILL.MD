---
name: domain-modeling
description: Build and sharpen the project's domain model and terminology. Manage CONTEXT.md and write Architecture Decision Records (ADRs).

# Domain Modeling & Architecture Records

Actively build and sharpen the project's domain model as you design. Challenge fuzzy terms and update documentation immediately.

## 1. Domain Glossary (`CONTEXT.md`)

`CONTEXT.md` is the project's ubiquitous language. It is a glossary, NOT a spec or scratchpad. Create it lazily at the repo root when the first term is resolved.

### Rules during the session:
- **Challenge:** If the user says "account", ask: "Do you mean Customer or User?"
- **Update Inline:** When a term is resolved, update `CONTEXT.md` immediately.

### CONTEXT.md Format:
Be opinionated. Pick the best word and list others under `_Avoid_`.
```md
# Project Context
{One sentence description of what this context is}

## Language

**Invoice**:
A request for payment sent to a customer after delivery.
_Avoid_: Bill, payment request

**Customer**:
A person or organization that places orders.
_Avoid_: Client, buyer, account

(Note for multi-context repos: If CONTEXT-MAP.md exists at the root, find the specific context like src/billing/CONTEXT.md and update that instead.)

## 2. Architecture Decision Records (ADRs)
Only offer to create an ADR in docs/adr/ when ALL THREE of these are true:

Hard to reverse: The cost of changing your mind later is meaningful (e.g., Database choice).

Surprising without context: A future reader will wonder "why did they do it this way?"

A real trade-off: There were genuine alternatives and you picked one.

If the decision is easy to reverse, DO NOT write an ADR.

**Immutability Rule:** Never delete or change the content of an old ADR. If a decision changes, write a new ADR and update the old one's status to `superseded by [New ADR Number]`.

ADR Format (docs/adr/0001-slug.md):
Number them sequentially (0001, 0002). Keep it extremely short. Do not fill out bloated templates.

Markdown
# {Short title of the decision}

**Status:** proposed | accepted | deprecated | superseded by ADR-0005

{1-3 sentences: what's the context, what did we decide, and why.}

**Considered Options:**
- {Only include if rejected alternatives are worth remembering}