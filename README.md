# C31 · Crunch Version Control

> A free, open-source **8-week version-control track** for engineers who can write code but treat Git as a set of memorized incantations. From "what actually happens on `git commit`?" to running a protected, CI-gated repository a real team could ship from — branches, pull requests, rebases you're not afraid of, GitHub Actions, and release workflows. The version-control foundation every other Code Crunch course assumes.

[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](LICENSE)
[![Git · GitHub · Actions](https://img.shields.io/badge/stack-Git_·_GitHub_·_Actions-2463EB.svg)](#stack)
[![Built in the open](https://img.shields.io/badge/built-in%20the%20open-2463EB.svg)](https://github.com/CODECRUNCHWORLDWIDE)

C31 is a Tier-1 foundation course (8 weeks) because version-control fluency is a *prerequisite*, not a specialty. Every other track — from C1 Python to C24 Robotics — assumes you can branch, review, and ship without fear. This course makes that true.

---

## Pathway summary

- **Full-time:** 8 weeks · ~30 hrs/week · ~240 hours
- **Working-engineer pace:** 4 months · ~15 hrs/week
- **Evening pace:** 8 months · ~7 hrs/week

See [`SYLLABUS.md`](SYLLABUS.md).

---

## What you will be able to do at the end of 8 weeks

- **Explain Git's model** — the object database (blobs, trees, commits), the three trees (working tree, index, HEAD), and refs — well enough to predict what any command will do *before* you run it.
- **Branch and merge** with intent: fast-forward vs. three-way merges, resolving conflicts calmly, and knowing when to merge vs. rebase.
- **Work with remotes and GitHub**: clone, fetch, push, track branches, fork, and keep a fork in sync.
- **Rewrite history safely**: `amend`, interactive `rebase`, `cherry-pick`, and recovering anything with `reflog`.
- **Run a professional pull-request workflow**: small PRs, meaningful reviews, draft PRs, and GitHub flow.
- **Gate a repo with CI**: GitHub Actions that run tests on every push, required status checks, and branch protection.
- **Reach for the advanced tools when they fit**: hooks, `bisect`, `worktree`, submodules, tags, and releases.
- **Design a team workflow**: trunk-based development, protected branches, and a repeatable release process.

---

## Curriculum (8 weeks)

| Week | Topic | You leave able to… |
|------|-------|--------------------|
| 1 | [Git Fundamentals](curriculum/week-01-git-fundamentals/) | Init, stage, commit, and read the object model + the three trees. |
| 2 | [Branching & Merging](curriculum/week-02-branching-and-merging/) | Create branches, merge, and resolve conflicts without panic. |
| 3 | [Remotes & GitHub](curriculum/week-03-remotes-and-github/) | Push/pull/fetch, track branches, fork, and sync a fork. |
| 4 | [Rewriting History](curriculum/week-04-rewriting-history/) | Amend, interactive rebase, cherry-pick, and recover with reflog. |
| 5 | [Pull Requests & Review](curriculum/week-05-pull-requests-and-review/) | Run GitHub flow: small PRs, reviews, and draft PRs. |
| 6 | [CI with GitHub Actions](curriculum/week-06-ci-with-github-actions/) | Build a workflow that tests every push and gates merges. |
| 7 | [Advanced Git](curriculum/week-07-advanced-git/) | Use hooks, bisect, worktrees, submodules, tags, and releases. |
| 8 | [Team Workflows at Scale](curriculum/week-08-team-workflows-at-scale/) | Design trunk-based dev, protected branches, and releases (capstone). |

---

## How to navigate a week

Every week folder holds the same structure:

- **`README.md`** — the week overview + how the pieces fit + the week's goal.
- **`lecture-notes/`** — 3 lectures (~2 hrs each), the conceptual core.
- **`exercises/`** — 3 short, guided reps to build muscle memory.
- **`challenges/`** — 2 open-ended problems with no single right answer.
- **`mini-project/`** — one build that ties the week together.
- **`homework.md`**, **`quiz.md`**, **`resources.md`** — practice, self-check, and further reading.

---

## Stack

Git (2.40+), GitHub, GitHub Actions, GitHub CLI (`gh`). Everything is free and works on macOS, Linux, and Windows (WSL recommended).

---

*Part of the Code Crunch Worldwide open curriculum · GPL-3.0 · [Browse all courses](https://codecrunchglobal.vercel.app/courses)*
