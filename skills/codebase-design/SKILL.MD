---
name: codebase-design
description: Shared vocabulary and principles for designing deep modules, setting seams, and dependency classification.

# Codebase Design

Design **deep modules**: a lot of behaviour behind a small interface, placed at a clean seam, testable through that interface. The aim is leverage for callers, locality for maintainers, and testability for everyone.

## Core Glossary

- **Module**: Anything with an interface and an implementation (function, class, package). Avoid: component, service.
- **Interface**: Everything a caller must know to use the module: signatures, invariants, ordering, error modes, configuration.
- **Implementation**: The hidden body of code inside the module.
- **Seam**: A place where you can alter behaviour without editing in that place (where the interface lives).
- **Adapter**: A concrete implementation that satisfies an interface at a seam (e.g., SQLite adapter, In-memory mock).
- **Depth (Leverage)**: High capability/behaviour behind a small, simple interface.
- **Shallow Module (Anti-pattern)**: Large interface with very little actual logic (pass-through wrappers). Avoid these.

---

## Design Principles

1. **Design It Twice:**
   Your first interface idea is rarely the best. Before committing, consider at least two contrasting designs:
   - *Option A (Minimal):* 1–3 entry points, maximum leverage per call.
   - *Option B (Ergonomic):* Optimized for the most common caller, sane defaults.
   Compare their depth, testability, and seam placement before writing code.

2. **The Deletion Test:**
   Imagine deleting the module. If complexity vanishes, it was a pass-through (shallow). If complexity reappears across callers, it was earning its keep (deep).

3. **Seam Discipline:**
   - One adapter = hypothetical seam (indirection). Avoid unless necessary.
   - Two adapters = real seam (e.g., Production adapter + Test stand-in).
   - Do NOT expose internal implementation seams just for tests. The interface itself is the test surface.

4. **Designing for Testability:**
   - **Accept dependencies, don't create them:** Pass database clients, clock/time, or network callers in via parameters/constructors.
   - **Return values over side effects:** Prefer returning data to mutating global/external state.

---

## Dependency Classification & Testing Strategy

Classify external dependencies to determine how the module should be tested across its seam:

| Category | Description | Testing Strategy |
| :--- | :--- | :--- |
| **1. In-process** | Pure computation, memory-only state, no I/O. | Merge modules directly. Test through the public interface. No mocks/adapters needed. |
| **2. Local-substitutable** | DBs or filesystems with local stand-ins (e.g., SQLite, in-memory FS). | Test using the local stand-in. Keep the seam internal. |
| **3. Remote but owned** | Internal microservices, internal REST/gRPC APIs. | Define a **Port** (interface). Implement an in-memory adapter for tests and network adapter for production. *In large systems, use OpenAPI fakes or Contract Testing (e.g. Pact) to prevent mock drift.* |
| **4. True external** | Third-party services (Stripe, AWS S3, OAuth providers). | Inject an interface at the boundary. Use a mock adapter only for these tests. |

### Testing Rule: Replace, Don't Layer
- When refactoring or deepening shallow modules, **delete the old unit tests** that targeted internal shallow functions.
- Write new tests strictly at the deepened module's interface.
- Tests assert observable outcomes, not internal variables or call counts.