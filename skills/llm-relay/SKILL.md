---
name: llm-relay
description: Coordinate two LLM agents on one repository through a file-based handoff relay with READY markers, single-writer phases, blocking watches, and one-time handoff IDs. Use when a user wants two agents (for example a reviewer and an implementation owner) to collaborate asynchronously without a shared chat.
---

# LLM Relay

Use this skill when two agents must collaborate on one codebase through files
instead of a shared conversation: typically one reviewer and one implementation
owner. The relay is a strict single-writer handoff protocol. It works because
each side always knows exactly whose turn it is and what to do next.

## Roles and files

- Each agent owns exactly one communication file and only ever writes there.
  Example: the reviewer writes `fromreviewer.md`, the implementer writes
  `fromimplementer.md`.
- Never edit, overwrite, or reformat the other agent's file. Reading is always
  allowed; writing is never allowed.
- Fix roles at the start. The implementation owner is the only agent that edits
  implementation files. Reviewers run read-only checks and send findings back.

## Handoff format

- Every message block has a monotonically increasing four-digit ID: `0001`,
  `0002`, `0003`, ...
- The sender writes the entire block first, and only then writes the handoff
  marker as the absolute last non-empty line:
  - the reviewer concludes with `READY_FOR_IMPLEMENTER: NNNN`
  - the implementer concludes with `READY_FOR_REVIEWER: NNNN`
- A receiver may only begin when the expected complete marker is the last
  non-empty line of the other file. A file modification time alone is only a
  wake-up signal, not permission to start.

## Waiting

- After writing your marker, stop all work on implementation and on your own
  communication file, then block on a watch of the other agent's file until the
  expected marker appears.
- Keep the LLM session active around the blocking tool call, using a polling
  loop or `inotifywait` inside the tool call. A detached background process
  cannot wake the agent by itself.
- Process each handoff ID exactly once. Remember the last processed ID in your
  own working memory; never record it by editing the other agent's file.
- If a marker is wrong, duplicated, or skips an expected ID, do not guess. Write
  the problem in your own file and hand the relay back.

## Message template

Every handoff should answer, in order:

1. What I did or propose.
2. Which files I changed.
3. Which commands and tests I ran, with results.
4. Known problems, assumptions, and open decisions.
5. What I want reviewed.

Keep handoffs scoped. Prefer one reviewable step over a large batch, and let the
reviewer gate later phases before they start.

## Evidence discipline

- Report negative and mismatch tests explicitly. Do not claim coverage from a
  test you did not run.
- Report divergences, failures, and skipped cases as data instead of hiding or
  filtering them.
- Do not declare a winner from one seed, one run, or one hand-picked setting.
  Report spread, and pre-register thresholds and configs before final runs.
- Keep result files out of version control; commit frozen configs and
  pre-registrations before the runs that consume them.
- Make machine-readable output strict (no `NaN`/`Infinity`, use `null`) and
  record provenance so mismatched or stale inputs can be detected.
- Commit logical units separately, and state explicitly when part of the
  workspace is outside the repository under version control.

## Quick checklist

- [ ] Roles fixed; each agent writes only its own file.
- [ ] Handoff IDs increment by one; last processed ID tracked locally.
- [ ] Marker written last, after every other write.
- [ ] Receiver waits for the exact marker, then processes that ID once.
- [ ] Blocking watch keeps the session alive around the tool call.
- [ ] Reviewer stays read-only; implementer owns implementation commits.
- [ ] The handoff answers the five template points and hands control back.
