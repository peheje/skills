---
name: red-green-tdd
description: Use practical Red-Green-Refactor discipline for code changes. Prove tests have signal before relying on them as coverage, then keep implementation focused.
---

# Principle

A test is useful evidence only if it has been shown to catch the behavior it claims to cover.

Prefer classic Red-Green-Refactor when it is natural. If the test already passes, it is acceptable to temporarily change production code to prove the test fails for the intended reason, then undo that temporary change before continuing.

# Workflow

1. Pick one observable behavior.
2. Write or identify the smallest relevant test.
3. Run the smallest useful test command.
4. Prove the test has signal:
   - natural failure is best
   - temporary production-code mutation is allowed
   - setup, import, mock, or environment failures do not count
5. Make the focused production change.
6. Rerun the relevant test and confirm it passes.
7. Refactor only when useful, then rerun relevant tests.

# Guardrails

- Do not claim coverage from a test you did not run.
- Do not keep temporary mutations used only to prove a test.
- Do not weaken assertions to get green.
- Do not mock away the behavior under test.
- Do not mix unrelated cleanup into the behavior change.
- Report unrelated failures instead of hiding them.

# Report

Briefly report:
- Behavior:
- Test/proof command:
- Proof result:
- Change made:
- Final test result:
