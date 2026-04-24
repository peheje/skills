# red-green-tdd

`red-green-tdd` is a portable Agent Skill that keeps coding agents honest about tests without turning every change into ceremony. It asks agents to prove tests have signal before treating them as coverage, then keep implementation focused and unrelated cleanup out of the cycle.

It is intended for Codex, Claude Code, Cursor, OpenCode, and other Agent Skills compatible harnesses.

## Install

Install the repository:

```sh
npx skills add <owner>/<repo>
```

If your CLI supports selecting a single skill from a repository, install only this skill:

```sh
npx skills add <owner>/<repo> --skill red-green-tdd
```

## Usage

Expected usage:

> When implementing behavior or fixing bugs, use the red-green-tdd skill.

Tiny example prompt:

```text
Use the red-green-tdd skill to fix the validation bug in the signup form.
```
