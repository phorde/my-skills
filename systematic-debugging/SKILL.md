---
name: systematic-debugging
description: Use when encountering any bug, test failure, runtime error, or unexpected behavior before proposing or applying fixes. Enforces the 4-phase scientific root-cause investigation. Forbids blind guessing and symptom patches.
---

# Systematic Debugging

Eliminate guesswork. Find the single root cause before touching any production code.

---

## The Iron Law

```
NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST
```

If you have not verified the root cause, you are strictly forbidden from editing production code or proposing fixes.

---

## When to Activate

Activate immediately when:
- A unit, integration, or e2e test fails.
- A runtime exception, crash, or unexpected HTTP status occurs.
- Output deviates from the specification.
- A previous "fix" didn't solve the issue or introduced a regression.

Do **NOT** skip this process when:
- The issue looks "trivial" or "obvious" (assumptions create recurring bugs).
- You are under time pressure (guessing causes thrashing and rework).

---

## The 4-Phase Diagnostic Method

```
[Phase 1: Root Cause] ──▶ [Phase 2: Pattern] ──▶ [Phase 3: Hypothesis] ──▶ [Phase 4: Fix & Verify]
```

### Phase 1: Root Cause Investigation
1. **Reproduce Minimally**:
   - Isolate the smallest deterministic test or script that reliably triggers the failure.
   - Capture exact error messages, stack traces, and relevant state snapshots.
2. **Trace the Data Flow**:
   - Trace backwards from the point of failure to where the invalid state originated.
   - Do not stop at the symptom (e.g., `null pointer error`). Identify *why* the pointer was null in the first place.
3. **Inspect Recent Changes**:
   - Check `git diff` or recent commits that touched the affected subsystem.

### Phase 2: Pattern Analysis
1. **Locate Identical Patterns**:
   - Search the codebase for similar call sites or patterns that might suffer from the identical defect.
2. **Understand the Violated Invariant**:
   - Identify which contract, precondition, or state invariant was breached.

### Phase 3: Falsifiable Hypothesis Testing
1. **Formulate Single Hypotheses**:
   - State clearly: *"I hypothesize the failure is caused by X because Y."*
2. **Test Without Code Churn**:
   - Prove or disprove using targeted log statements, debugger inspection, or temporary assertions.
   - Change **ONE** variable at a time. If refuted, revert the diagnostic probe and form the next hypothesis.

### Phase 4: Implementation & Regression Verification
1. **Red Test First**:
   - Write an automated regression test reproducing the exact failure. The test MUST fail.
2. **Surgical Fix**:
   - Apply the minimal code change necessary to satisfy the invariant at its root cause.
   - Never apply speculative cleanup or unrelated changes in the same patch.
3. **Full Suite Verification**:
   - Run the regression test: verify it passes (Green).
   - Run the entire project test suite: verify zero collateral regressions.
