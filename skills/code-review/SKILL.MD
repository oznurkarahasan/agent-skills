---
name: code-review
description: Multi-axis code review evaluating both Spec (Correctness) and Standards (Architecture/Style). Use before merging PRs, committing large changes, or evaluating another agent's code.
---

# Code Review & Quality Audit

Every review must evaluate the code across two independent domains:
1. **The Spec Axis:** Does it faithfully implement the requested feature/fix?
2. **The Standards Axis:** Does it adhere to clean architecture, security, and repository conventions?

Do not let a clean architecture blind you to a missed requirement, and do not let functional correctness blind you to a structural mess.

---

## 1. The Feedback Format (Severity Labels)

AI agents often present minor styling issues with the same urgency as critical vulnerabilities. You MUST prefix every piece of feedback with one of these severity labels to establish clear communication and maintain team morale:

| Prefix | Meaning | Author Action |
| :--- | :--- | :--- |
| **Critical:** | Security vulnerability, data loss, or completely breaks the spec. | MUST fix to merge. |
| *(no prefix)* | Structural flaw, bug, or clear violation of repo standards. | MUST fix to merge. |
| **Optional:** | Worth considering for better health, but not strictly required. | Author's discretion. |
| **Nit:** | Minor cosmetic issue, naming preference, or formatting. | Author may ignore. |
| **Praise:** | Elegant solution, clean architecture, or exceptional simplification. | Smile and keep up the good work :) |

*Rule:* Do not just look for flaws. If the author wrote a particularly clean module, effectively simplified a complex branch, or added a clever optimization, explicitly call it out using the **Praise:** label. If you have one structural problem and ten nits, the structural problem IS the review. Do not bury real issues under cosmetic nits.
---

## 2. The Spec Axis (Correctness)

Evaluate the diff against the originating issue, prompt, or `CONTEXT.md`:
- **Missing Requirements:** What was asked for but not implemented?
- **Scope Creep:** What behavior is in the code that was NEVER asked for? (Flag speculative generality).
- **Edge Cases:** Are nulls, empty states, and boundary conditions handled?

---

## 3. The Standards Axis (Architecture & Smells)

Evaluate the structure. Instead of saying "this is complex", identify the specific code smell and propose the mechanical refactoring move:

- **Mysterious Name:** The name doesn't reveal intent. → *Remedy: Propose a precise name.*
- **Shotgun Surgery:** One logical change forces edits across many files. → *Remedy: Consolidate the logic into one module.*
- **Feature Envy:** A method reaches into another object's data more than its own. → *Remedy: Move the method onto the data it envies.*
- **Primitive Obsession:** Passing raw strings/ints instead of a domain concept. → *Remedy: Create a dedicated type/struct.*
- **Repeated Switches:** The same `if/switch` cascade appears in multiple places. → *Remedy: Replace with polymorphism or a dispatcher.*
- **Dead Code:** Leftover `_unused` variables, commented-out code, or unreachable branches. → *Remedy: Delete them explicitly.*

---

## 4. Security & Dependencies

- **Untrusted Data:** Is input from users, APIs, or the DB validated at the boundary before use?
- **Secrets:** Are any tokens, keys, or credentials hardcoded in the diff? (Flag as **Critical**).
- **Dependencies:** If `package.json`, `Cargo.toml`, or `requirements.txt` is modified:
  - Is the new dependency strictly necessary, or does the standard library suffice?
  - Was it bundled with unrelated code changes? (Dependency bumps must be isolated).

---

## 5. Anti-Rationalization & Red Flags

| Rationalization | Reality |
| :--- | :--- |
| "The tests pass, so it is good to merge." | Tests do not catch poor architecture, tight coupling, or security flaws. |
| "I will fix the mess in a future refactoring PR." | Deferred cleanup rarely happens. Block the merge until the code is clean. |
| "The file is getting large, but it's just a small addition." | Do not normalize architectural drift. Extract helpers or split the module first, then add. |
| "I'll output a summary of 20 bullet points." | Long lists are ignored. Prioritize the top 3 highest-leverage structural/spec issues. |

---

## 6. Verification Gate

To complete a review, output your findings grouped by axis.
- [ ] Every comment has a Severity Prefix.
- [ ] Unrequested scope creep is flagged.
- [ ] Structural flaws are named using Fowler's smells with concrete remedies.
- [ ] No dead code or commented-out blocks are left behind.