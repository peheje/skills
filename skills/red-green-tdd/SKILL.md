---
name: red-green-tdd
description: Use practical Red-Green-Refactor TDD for code changes. Use whenever implementing behavior, fixing bugs, changing logic, or adding tests. Prefer proving tests fail for the intended reason before relying on them as coverage.
---

# Core principle

A test is not strong evidence unless it has been observed failing for the intended reason at least once.

Before claiming a behavior is covered, prove the relevant test would catch the missing or incorrect behavior. The cleanest route is classic Red-Green-Refactor, but temporary production changes are allowed when they are the simplest way to validate an existing or newly written test.

# Trigger conditions

Use this skill whenever:
- implementing new behavior
- fixing a bug
- changing existing logic
- adding validation
- changing edge-case behavior
- refactoring behavior-sensitive code
- writing or updating tests for production behavior

Do not use for:
- pure documentation edits
- formatting-only changes
- deleting dead comments
- non-behavioral config edits, unless tests are relevant

# Workflow

Use small vertical slices.

One cycle is:

1. RED
   - Choose exactly one observable behavior.
   - Write or identify the smallest test that covers that behavior.
   - Run the smallest relevant test command.
   - Verify the test can fail for the intended behavioral reason before relying on it.
   - If the test fails naturally, capture or summarize that failure.
   - If the test already passes, optionally make the smallest temporary production-code change needed to force the expected failure, run the test, verify the failure, then undo the temporary change and confirm the test returns to green.
   - If the failure is caused by syntax, imports, setup, fixtures, mocks, snapshots, or environment, fix that first. It is not useful Red.

2. GREEN
   - Make the smallest production-code change that can pass the test.
   - No speculative abstraction.
   - No broad rewrites.
   - No unrelated cleanup.
   - No "while I am here" changes.
   - Rerun the same test.
   - Confirm it passes.

3. REFACTOR
   - Refactor only after Green.
   - Preserve behavior.
   - Run relevant tests again.
   - If behavior changes, restart from Red.

# Avoided shortcuts

The agent should not:
- claim a test is useful without running it
- mock the behavior under test
- keep temporary production changes used only to prove Red
- change the test during Green unless the test is wrong or incomplete
- add test-only production hooks unless explicitly approved
- add broad abstractions before the test requires them
- batch many behavior changes into one cycle unless explicitly instructed
- satisfy tests by weakening assertions
- ignore failing unrelated tests without reporting them

# Useful Red

A useful Red means:
- the test was actually run
- the test failed
- the failure is shown or summarized precisely
- the failure is caused by the missing or incorrect production behavior
- the failure would plausibly pass after implementing the intended behavior

# Not useful Red examples

Not useful Red includes:
- test passes immediately
- test fails because the test file does not compile
- test fails because imports are wrong
- test fails because fixture setup is broken
- test fails because a mock was not configured
- test fails because the test asserts implementation details unrelated to behavior
- test fails because the test runner or environment is misconfigured
- snapshot diff with unclear behavioral meaning

# Legacy code exception

For legacy code, characterization tests are allowed to capture existing behavior.

But:
- new behavior should still have a test whose failure mode is understood
- bug fixes should have a failing test, or a temporary production-code mutation that proves the test catches the bug
- refactors require tests proving behavior is preserved

# Spike exception

Spikes are allowed only when explicitly requested or clearly marked as disposable.

Rules:
- spike code must not be treated as final implementation
- after the spike, discard or isolate spike code
- return to the normal workflow before production implementation

# Required reporting format

For every cycle, report:

- Behavior under test:
- Test file/change:
- Proof command:
- Proof result:
- Why this proves the test is useful:
- Minimal production change:
- Green command:
- Green result:
- Refactor, if any:
- Final test command:

If useful Red cannot be produced, report:

- Attempted behavior:
- Command run:
- Actual result:
- Why this does not prove the test is useful:
- Next corrective step:

# Stop conditions

Stop and ask/clarify only when:
- the intended behavior is ambiguous
- no relevant test command can be identified
- the test environment is broken in a way unrelated to the change
- implementing the behavior safely requires a product/design decision

# Final checklist

Before claiming completion, verify:
- Did I prove the test can fail for the intended reason?
- Did I undo any temporary production changes used only to prove the test?
- Did I make the smallest production change?
- Did I rerun and observe Green after the real change?
- Did I avoid unrelated cleanup?
- Did I report commands and results?
