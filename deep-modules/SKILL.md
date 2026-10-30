---
name: deep-modules
description: Architecture design principles inspired by John Ousterhout's Philosophy of Software Design. Enforces deep modules with simple interfaces hiding high complexity, eliminates shallow pass-through abstractions, forbids classitis/file-splattering, and defines errors out of existence.
---

# Deep Modules Architecture

Build robust software by maximizing implementation depth while minimizing interface complexity.

---

## 1. Deep vs. Shallow Modules

```
       DEEP MODULE                      SHALLOW MODULE
   ┌───────────────────┐             ┌────────────────────────┐
   │  Simple Interface │             │ Wide/Complex Interface │
   ├───────────────────┤             ├────────────────────────┤
   │                   │             │   Thin Implementation  │
   │    Substantial    │             └────────────────────────┘
   │    Complexity     │               (Cost exceeds benefit)
   │     Hidden        │
   │                   │
   └───────────────────┘
```

- **Deep Module**: An interface with 2 or 3 intuitive functions that hides intricate parsing, caching, state coordination, or algorithmic work behind it.
- **Shallow Module (Banned)**: A class or function whose interface takes as much effort to understand and wire up as the code inside it does.

---

## 2. Eliminate "Classitis" and File Splattering

AI agents frequently create premature indirection and fragmentation. Prevent this:

- **Ban Shallow Pass-Throughs**: If a function or class does nothing except call another single function with the identical parameters without adding domain value, **delete it**.
- **Co-locate Related Concepts**: Do not split a cohesive 50-line domain concept across 5 separate files (`Entity`, `Dto`, `Mapper`, `Service`, `Repository`). Keep related logic together until real complexity demands separation.
- **Resist Premature Abstraction**: Three concrete implementations justify an interface; zero implementations do not. Never write abstract interfaces that have exactly one implementation unless required for runtime dependency injection in testing.

---

## 3. Information Hiding

A module must conceal its design decisions from external callers:
- **Internal Data Structures**: Callers must never rely on internal list/map representations or raw JSON schemas.
- **Third-Party Adapters**: Encapsulate vendor SDKs (e.g., Stripe, OpenAI, Resend) behind an internal domain-specific interface so third-party schema churn never propagates into core business logic.

---

## 4. Define Errors Out of Existence

Design APIs so that special cases and error states disappear or are handled naturally:
- **Empty Collections Over Nulls**: Return empty arrays/slices instead of `null` or `undefined` to eliminate null checks across all callers.
- **Idempotent Operations**: Design mutations (e.g., `deleteItem`, `cancelSubscription`) so calling them twice produces the same clean outcome without raising error flags.
- **Tolerant Inputs, Strict Outputs**: Accept flexible parameters where unambiguous, but always emit strictly formatted, validated outputs.

---

## 5. Defensive Validation at the Boundaries

- Enforce rigorous validation (e.g., Zod, Pydantic, schema guards) strictly at system boundaries: HTTP requests, webhooks, external LLM payloads, and environment configurations.
- Once sanitized data enters the internal domain core, avoid repetitive redundant checks.
