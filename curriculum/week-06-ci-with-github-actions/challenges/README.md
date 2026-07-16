# Week 6 — Challenges

Two open-ended problems. Unlike the exercises, these have **no single right answer** — they hand you a goal and constraints, and you design the solution. Expect to read the docs, make trade-offs, and defend your choices.

| # | Challenge | The problem | Time |
|--:|-----------|-------------|-----:|
| 1 | [challenge-01-broken-code-cannot-merge.md](./challenge-01-broken-code-cannot-merge.md) | Configure a repo so broken code *literally* cannot merge — then adversarially try to sneak past your own gate | ~1h |
| 2 | [challenge-02-lint-test-coverage-gates.md](./challenge-02-lint-test-coverage-gates.md) | Build a PR pipeline with lint + test + coverage gates, each independently blocking | ~1.5h |

## How your work is judged

There's no autograder. Judge yourself (or have a peer judge) against each challenge's **success criteria** and **red-team** section. The spirit of both challenges is the same as the week's goal: *a machine, not a human, decides whether code is allowed onto the trunk.*

## Ground rules

- Use a **public** repo so Actions minutes are free.
- Everything must be **reproducible from the repo** — the workflow files and protection settings (exported ruleset JSON, or a documented click-path) live in the repo, so someone else could recreate your setup.
- Prefer **free tooling** that ships with the runner or installs in seconds.
- Write a short `NOTES.md` in each challenge's directory explaining your design decisions and what you tried to break.
