---
name: git-conventions
description: Enforces atomic commits, conventional commit messages, and branch naming rules. Use whenever you are about to run `git commit`, create a branch, or prepare a PR. Ensures version history stays readable and CI-parseable.
---

# Git Conventions

A readable git history is a first-class engineering asset. Every commit should be a self-contained, reviewable unit that explains *why* a change was made, not just *what* changed.

---

## 1. Branch Naming

Use the format: `<type>/<short-slug>`

| Type | When to use |
| :--- | :--- |
| `feat/` | A new user-facing capability |
| `fix/` | A bug fix |
| `refactor/` | Internal restructuring with no behavior change |
| `chore/` | Tooling, config, dependency updates, CI |
| `docs/` | Documentation-only changes |
| `test/` | Adding or fixing tests only |
| `spike/` | Throwaway exploratory branch — must be deleted after |

**Rules:**
- Use kebab-case: `feat/user-login`, not `feat/userLogin` or `feat/user_login`.
- Keep slugs short (2–4 words max).
- Never commit directly to `main` or `master`. Always work on a branch.

---

## 2. Commit Message Format (Conventional Commits)

Every commit message MUST follow this structure:

```
<type>(<scope>): <short summary>

[optional body]

[optional footer]
```

### Type (required)
Same vocabulary as branch types: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`.

### Scope (optional but recommended)
The module, feature area, or file most affected. Example: `feat(auth):`, `fix(invoice-parser):`.

### Short Summary (required)
- Imperative mood, present tense: *"add user login"*, not *"added"* or *"adds"*.
- 72 characters max.
- No period at the end.
- Must answer: *"If applied, this commit will \_\_\_."*

### Body (optional)
- Explain **why**, not what. The diff already shows what.
- Wrap at 72 characters.

### Footer (when applicable)
- Reference issues: `Closes #42`, `Fixes #17`.
- Flag breaking changes: `BREAKING CHANGE: removed X endpoint`.

**Examples:**

```
feat(auth): add JWT refresh token rotation

Tokens previously never expired once issued. This change adds
a 7-day rolling refresh window to reduce exposure from leaked tokens.

Closes #88
```

```
fix(invoice-parser): handle missing VAT field gracefully

Previously threw a NullPointerException when VAT was absent.
Now defaults to 0 with a warning log.
```

```
chore: upgrade eslint to v9
```

---

## 3. The Atomic Commit & Save Point Rule

One commit = one logical change.

**Commit Early:** Do not accumulate large uncommitted changes. The moment a test passes or a logical slice is complete, commit it as a "Save Point." If the next change breaks the code, you can revert cleanly.

**Anti-patterns to refuse:**
- `` fix stuff `` — no scope, no reason.
- `WIP` — work in progress commits must be squashed before merge.
- Mixing concerns: a single commit that adds a feature AND bumps a dependency.
- Committing commented-out code or `[DEBUG-...]` log tags.

---

## 4. Pre-Commit Hygiene (Agent Gate)

Before running `git commit`, the agent MUST perform these checks in order:

1. **Review Diff:** Run `git diff --staged` and read every line being committed.
2. **Secret Scan:** Ensure no tokens, API keys, passwords, or `.env` files are staged.
3. **Debug Tag Removal:** Ensure no `[DEBUG-...]` tags, `console.log("HERE")`, or commented-out code blocks remain in the diff.
4. **Message Check:** Verify the commit summary answers *"If applied, this commit will ___."*

If any check fails, fix the issue before committing. Do not override these gates.

---

## 5. Agent Reporting (Change Summary)

After completing a task and committing, present this structured summary to the user:

```
CHANGES MADE:
- [file or module]: [one-line description of the logic change]

THINGS I DIDN'T TOUCH (Scope Discipline):
- [file or component that seemed related but was deliberately left out]

POTENTIAL CONCERNS:
- [e.g., added a large dependency / strict schema validation / performance trade-off]
```

This proves scope discipline was exercised and no unsolicited renovations occurred. If there are no concerns, state: *"No concerns identified."* — do not omit the section.

---

## 6. Pull Request Discipline

- **PR title** = the squash commit message (Conventional Commits format).
- **PR description** must include:
  - **Why:** What problem does this solve?
  - **What:** Brief summary of the approach.
  - **How to test:** One command or step to verify the change locally.
- Keep PRs small. If a PR touches more than ~400 lines, consider splitting it.
- Do not merge a PR with failing CI. No exceptions.

---

## Red Flags

- A commit message that starts with `fix` but has no description of *what* was fixed.
- Squashing a multi-day feature into a single `feat: implement everything` commit.
- Branch names like `my-branch`, `test2`, or `oz-working`.
- Rebasing shared branches that others have already pulled.
- Committing generated files (`dist/`, `.next/`) or environment files (`.env`).
