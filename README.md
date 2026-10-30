# Yasa's Rules for Agents

This repository holds personal development guidelines for AI coding agents (Pi, Claude Code, Codex, etc). In the era where agentic development slowly becomes the norm, one must control their machines.

This is an opinionated set of rules. It works for me, but not neccesarily you, so please give the guidelines a read before installing.

## Layout

| File                                      | Purpose                                                                   |
| ----------------------------------------- | ------------------------------------------------------------------------- |
| `AGENTS.md`                               | The main rule file, loaded for every new session.                         |
| `rules/01-reasoning-before-action.md`     | Details for Rule 01: explain before mutating anything.                    |
| `rules/02-scope-discipline.md`            | Details for Rule 02: stay inside the given scope, stop before leaving it. |
| `rules/03-simplicity-and-architecture.md` | Details for Rule 03: think before structure, DRY, KISS.                   |
| `rules/04-complement-the-human.md`        | Details for Rule 04: complement the human, do not replace them.           |

## Install

Take note of where your agent of choice reads the global `AGENTS.md` (refer to their official docs). Either copy the files directly or symlink `AGENTS.md` and `rules`. I prefer the latter, since you can maintain copies for any number of agents in one place.

In case where the agent can't read a global `AGENTS.md`, simply instruct them like this:

```
Read and follow /path/to/AGENTS.md before any work.
```

## License?

_do I even need a license? ok fine..._

**WTFPL**, see [LICENSE.TXT](LICENSE.TXT) for full license text.
