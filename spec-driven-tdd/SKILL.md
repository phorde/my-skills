---
name: spec-driven-tdd
description: Autonomous Spec-Driven Development (SDD) and Test-Driven Development (TDD) engine. Performs evidence-based stack brainstorming, parametric domain modeling (anti-hardcoding), single-document specifications (PRD.md), zero-bloat clarification, and continuous autonomous execution until 100% completion.
---

# Spec-Driven TDD (Autonomous, Evidence-Based & Parametric)

Transform product vision into resilient, extensible software by selecting the right technical stack, designing formal parametric contracts (SDD), and executing autonomously to completion via strict test-driven engineering (TDD).

---

## STAGE 0: STACK BRAINSTORMING & EVIDENCE-BASED ARCHITECTURE

When initiating a new application or subsystem from scratch:

### 1. Evidence-Based Familiarity First
- **Scan the Local Environment**: Before defaulting blindly to a generic stack, inspect the workspace for runtimes, tooling, and existing code patterns (e.g., Bun/TypeScript, Python, Go, Rust, Docker Compose, SQLite, PostgreSQL).
- **Honor Developer Velocity**: A stack the developer already masters delivers 5x fewer regressions than an unfamiliar framework.

### 2. Dispassionate Technical Evaluation (The "Why" Matrix)
- Do not default to complex distributed architectures or SPAs when a simpler, more robust alternative exists:
  - If the domain demands blistering concurrency, tiny memory footprint, and single-binary deployment: evaluate **Go (Fiber/Echo)** or **Rust**.
  - If the domain is heavily data-oriented, algorithmic, or medical RAG/AI pipeline: evaluate **Python (FastAPI)**.
  - If the domain requires rapid full-stack monoliths with batteries-included auth and ORM: evaluate **Laravel** or **Rails**.
  - If the domain demands fine-grained reactivity and component ecosystem: evaluate **TypeScript/Next.js/Bun**.
- **Propose the Trade-Off**: If an alternative stack offers overwhelming superiority in performance, simplicity, or reliability for that specific domain, present a concise comparison (Option A: Recommended Familiar Stack vs. Option B: High-Leverage Alternative) before locking down the specification.

---

## STAGE I: SPEC-DRIVEN ANALYSIS & PARAMETRIC MODELING

Once the stack is established:

### 1. Anti-Hardcoding & Domain Parameterization
- **Instances are Seeds, Not Code**: Never hardcode entities cited as examples in the user prompt (e.g., specific hospitals, organizations, categories, formulas).
- **Parameterization by Design**: Model the domain dynamically (e.g., `EntityProfile`, a `GlobalPrompt` with per-entity overrides, dynamic field schemas, configurable character thresholds). Seed the user's specific examples as default records, not immutable TypeScript constants.

### 2. Silent Extraction & Zero-Bloat Inquiry
- **Look Before You Ask**: Read the prompt and existing code thoroughly. It is strictly forbidden to ask questions about facts, rules, or limits already provided.
- **Inquiry Boundary**:
  - If the prompt is self-contained: Ask **ZERO** questions. Formulate the specification and proceed.
  - If there are structural, irreversible forks (e.g., centralized DB vs. local-first offline PWA, auth architecture): Ask only the minimal essential questions, always providing a concrete recommended option.

### 3. The Single Source of Truth (`PRD.md`)
- Consolidate all domain models, state machines, normative acceptance criteria (`DOC-*`, `SEC-*`, `DATA-*`), and synthetic test catalogs (`E01-*`) into a **single, authoritative `PRD.md`** at the project root.
- **Zero Ceremony Slop**: Do not pollute the repository with sprawling planning directories (`tasks/*.md`, `001-day1.md`, `AGENTS.md`, `PLAN.md`). Everything lives in `PRD.md`.
- Divide the roadmap into **Vertical Functional Slices (Cycles 1 to N)**.

---

## STAGE II: CONTINUOUS AUTONOMOUS EXECUTION (Run to Completion)

Once the `PRD.md` is approved:

### 1. Autonomous Engine
- Do **NOT** stop after every step, file, or small change to ask for permission.
- Proceed through the vertical delivery cycles sequentially using subagent delegation and model routing.
- Execute autonomously until the entire scoped milestone is fully implemented, verified, and demonstrably working.

### 2. The Strict TDD Construction Loop (Per Cycle)
For every functional obligation in the cycle:
1. **Verified Red**:
   - Write automated tests derived directly from the PRD criteria and synthetic cases.
   - Assert on real domain behavior and outputs (no tautological mocks).
   - Run the test in the shell: it MUST execute and fail on an assertion (`Expected X, got Y`). If it fails on a syntax/import error, fix the test first.
2. **Minimal Green**:
   - Write the exact production code needed to satisfy the test.
   - **Test Immutability**: Modifying the test to fit broken code is strictly prohibited. The test is the contract.
   - Zero YAGNI: No speculative features or unrequested complexity.
3. **Refactor & Suite Pass**:
   - Run the entire global repository test suite to guarantee zero regressions.
   - Clean up code, remove dead paths, and adhere to deep interfaces.

### 3. Completion Gate
- Stop execution **ONLY** when:
  a) All acceptance criteria of the target cycles are verified green.
  b) The global test suite passes cleanly with zero warnings or errors.
  c) A concise report is prepared summarizing the delivered capabilities, executed tests, and instructions to run.
