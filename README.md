# skills

Portable [Agent Skills](https://skills.sh/) for coding agents: Codex, Claude
Code, Cursor, OpenCode, Pi, and other Agent Skills compatible harnesses.

This repository was previously named `red-green-tdd`; old links redirect here.

## Skills

| skill | what it does |
|---|---|
| [`llm-relay`](skills/llm-relay/SKILL.md) | Coordinate two LLM agents on one repository through a file-based handoff relay with `READY_FOR_*: NNNN` markers, single-writer phases, blocking watches, and one-time handoff IDs. |
| [`red-green-tdd`](skills/red-green-tdd/SKILL.md) | Prove tests have signal before treating them as coverage, then keep the implementation change focused. |
| [`ubuntu-interactive-sudo`](skills/ubuntu-interactive-sudo/SKILL.md) | Use Ubuntu's graphical polkit prompt with a terminal-attached `sudo` fallback for administrator tasks. |

## Install

Install the whole collection:

```sh
npx skills add peheje/skills
```

Install a single skill:

```sh
npx skills add peheje/skills --skill llm-relay
npx skills add peheje/skills --skill red-green-tdd
npx skills add peheje/skills --skill ubuntu-interactive-sudo
```

## Usage

Ask your agent to use a skill by name, for example:

```text
Use the llm-relay skill to run a reviewer/implementer handoff on this repo.
```

## Layout

```text
skills/
  llm-relay/SKILL.md
  red-green-tdd/SKILL.md
  ubuntu-interactive-sudo/SKILL.md
```

## License

MIT
