---
name: llm-relay
description: Coordinate two separate LLM agents on one repository through a file-based handoff relay with READY markers, single-writer phases, blocking watches, and one-time handoff IDs. Use when the user wants two agents (for example a reviewer and an implementation owner) to collaborate asynchronously without a shared chat.
---

# LLM Relay

Two **separate** agents collaborate on one codebase through files instead of a
shared chat: typically a reviewer and an implementation owner. The relay is a
strict single-writer handoff, and it only works because each side always knows
whose turn it is and that the other side is a different agent.

## 1. Roles: ask the user, never assume

Before any work, **ask the user which agent is the reviewer and which is the
implementer**, and confirm they are two independent agent sessions. Wait for the
answer; do not guess it from context.

- **Implementer** is the only agent that edits implementation files.
- **Reviewer** runs read-only checks and reports findings.
- **One agent never takes both roles.** Do not do the other side's work "to save
  time", and do not spawn a subagent to cover the other role. A single agent
  playing both sides creates duplicate writers and conflicting commits.
- If the user has not named both sides, stop and ask before doing anything else.

## 2. Each side owns exactly one file

Each agent owns exactly one communication file and writes only there (for
example `fromreviewer.md` and `fromimplementer.md`). Reading the other file is
always allowed; writing to it never is.

## 3. Turn-taking

- Every message block carries a monotonically increasing four-digit ID:
  `0001`, `0002`, ...
- Write the whole block first, then write the marker as the absolute last
  non-empty line: the reviewer ends with `READY_FOR_IMPLEMENTER: NNNN`, the
  implementer ends with `READY_FOR_REVIEWER: NNNN`.
- Start only when the expected complete marker is the last non-empty line of the
  other file. A recent modification time is only a wake-up signal, not
  permission to start.
- After writing your marker, stop work on implementation and on your own file,
  then block on a watch of the other file until the expected marker appears.
  Keep the session alive around the blocking call (a polling loop or
  `inotifywait`); a detached background process cannot wake the agent.
- Process each handoff ID exactly once, and track the last processed ID in your
  own working memory — never by editing the other agent's file.
- If a marker is missing, wrong, duplicated, or skips an expected ID, do not
  guess: write the problem in your own file and hand the relay back.

## 4. Handoff content

Every handoff answers, in order:

1. What I did or propose.
2. Which files I changed.
3. Which commands and tests I ran, with results.
4. Known problems, assumptions, and open decisions.
5. What I want reviewed.

Keep handoffs scoped. Prefer one reviewable step over a large batch, and let the
reviewer gate later phases before they start.

## 5. Evidence discipline

- Report negative and mismatch tests explicitly; never claim coverage from a
  test you did not run.
- Report divergences, failures, and skipped cases as data instead of hiding or
  filtering them.
- Do not declare a winner from one seed, one run, or one hand-picked setting.
  Report spread, and pre-register thresholds and configs before final runs.
- Keep result files out of version control; commit frozen configs and
  pre-registrations before the runs that consume them.
- Make machine-readable output strict (no `NaN`/`Infinity`; use `null`) and
  record provenance so stale or mismatched inputs can be detected.
- Commit logical units separately, and state explicitly when part of the
  workspace is outside version control.

## Checklist

- [ ] The user named both roles; I am exactly one of them.
- [ ] I write only my own file.
- [ ] The marker is the last non-empty line, and IDs increment by one.
- [ ] I waited for the exact marker, then processed that ID once.
- [ ] The handoff covers the five content points.
