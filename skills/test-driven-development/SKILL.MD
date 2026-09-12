---
name: test-driven-development
description: Test-driven development with red-green-refactor discipline. Use when writing new logic, adding features, or fixing bugs.
---

# Test-Driven Development (TDD)

Tests are proof — "seems right" is not done. Write behavior-focused tests against public interfaces before touching implementation code.

## 1. Discover the Stack First

Before writing tests, inspect the repository to identify the active build and test runner:
- Check manifests: `Cargo.toml`, `pyproject.toml`, `package.json`, `go.mod`, `Makefile`, etc.
- Identify focused test execution (single test) vs full-suite test execution.
- Never guess test runners (e.g. do not default to `npm test` without verification).

---

## 2. Core Rules of the Loop

RED                 GREEN               REFACTOR
Write focused       Write minimal       Clean code, keep
failing test   ──→  code to pass   ──→  tests passing   ──→  (repeat)
│                   │                   │
FAILS               PASSES              PASSES

1. **Red Before Green:** Always write the failing test first. A test that passes on the first run proves nothing.
2. **One Vertical Slice at a Time:** Do NOT write all tests first and implementations later. Write one test, implement it, verify green, then proceed to the next slice.
3. **Test at Seams (Public Boundaries):** Test through public interfaces. Do not test private functions or internal state.
4. **No Tautological Assertions:** The expected value must come from an independent literal or known spec, never by recomputing the logic identically to the implementation (`expect(add(a,b)).toBe(a+b)` is strictly forbidden).

### Spike/Prototype Exception
When exploring a completely new library or testing feasibility (a "spike"), do NOT apply TDD. Write throwaway code quickly to discover boundaries. However, once the architecture is clear, you MUST throw away the spike code and rebuild the permanent solution from scratch using the TDD loop.

---

## 3. The Prove-It Pattern (Bug Fixes)

When fixing a bug:
1. Write a focused reproduction test demonstrating the reported defect.
2. Run the test and confirm it **FAILS** (proves the bug exists).
3. Apply the minimal fix in implementation code.
4. Run the test and confirm it **PASSES** (proves the fix works).
5. Run the full test suite to guarantee zero regressions.

---

## 4. Test Design Principles

- **DAMP over DRY:** Prioritize descriptive, self-contained test scenarios over aggressive helper extraction.
- **State over Interactions:** Assert on visible outputs and state transitions, not internal call counts or mock invocation orders.
- **Mock at Boundaries Only:**
  - Mock *True External* dependencies (Payment gateways, 3rd party APIs).
  - Do NOT mock internal collaborators, business modules, or logic you control.

---

## 5. Anti-Rationalization Table

| Rationalization | Reality |
| :--- | :--- |
| "This change is too simple to test." | Simple logic grows complex. The test establishes and locks the invariant. |
| "I will write the test after implementing." | Post-hoc tests evaluate implementation details, not observable behavior. |
| "I verified it manually in the terminal/UI." | Manual checks do not persist in CI to catch future regressions. |
| "Mocking this is too difficult." | The interface has poor seam design. Refactor the interface to inject dependencies. |
| "Let me re-run the test suite to be sure." | Running tests repeatedly without code changes adds zero confidence. |

---

## 6. Verification Gate

A task is not complete until:
- [ ] Every new behavior or bug fix has a corresponding test.
- [ ] Focused tests failed first, then passed after minimal implementation.
- [ ] Expected values use explicit, independent test fixtures or literals.
- [ ] The repository's full test suite runs clean with no broken or skipped tests.