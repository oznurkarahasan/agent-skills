# Agent Skills Core Context

A collection of language-agnostic engineering skills and discipline protocols used to direct AI coding agents. This context defines the terminology used across all `SKILL.md` files.

## Language

**Skill**:
A modular, independent markdown file (`SKILL.md`) that enforces a specific engineering discipline, workflow, or anti-rationalization check on an AI agent.
_Avoid_: Prompt, instruction set, system rule, command.

**Intent**:
The validated, concrete outcome a user actually wants, usually extracted via a 6-line summary (Outcome, User, Success, Constraints, Out of scope). Extracted by the `intent-discovery` skill.
_Avoid_: Requirement, PRD, user story, ask.

**Domain Glossary**:
The single source of truth for a project's ubiquitous language, always stored as `CONTEXT.md`. Regulated by the `domain-modeling` skill.
_Avoid_: Dictionary, terms sheet, vocabulary list.

**Seam**:
A place where behavior can be altered without editing code in that place; the exact location where a module's public interface lives and where tests are written. Regulated by `codebase-design` and `test-driven-development`.
_Avoid_: Boundary (too overloaded), endpoint.

**Tight Feedback Loop**:
A single, deterministic, fast-executing command (script, test, or curl) that consistently goes RED on a specific bug. Mandatory before hypothesizing in `diagnosing-bugs`.
_Avoid_: Repro steps, test case.

**Spike**:
Throwaway, exploratory code written to learn a new framework or feasibility. Exempt from strict `test-driven-development`, but must be discarded before final implementation.
_Avoid_: Prototype (unless it's UI-only), draft.

## Relationships

- A **Skill** regulates one specific phase of development.
- The `intent-discovery` skill produces the **Intent**.
- The `domain-modeling` skill writes and updates the **Domain Glossary**.
- `test-driven-development` requires tests to be written exactly at a **Seam**.
- `diagnosing-bugs` requires a **Tight Feedback Loop** before generating hypotheses.

## Flagged ambiguities

- "Rule" vs "Skill". Resolved: "Skill" is the canonical term for the modular markdown files in this repository. "Rules" are the specific constraints inside those files.
- "Repro" vs "Tight Feedback Loop". Resolved: A "repro" is just a set of manual steps. A "Tight Feedback Loop" is an automated, executable command that goes red. The latter is required.