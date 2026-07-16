# Week 8 — Team Workflows at Scale (Capstone)

> **Goal:** Design and run a protected, CI-gated workflow that a real team could ship from. By the end of the week you own a repository with a chosen branching model, branch protection, required reviews via CODEOWNERS, CI gates that block bad merges, and an automated, versioned release — all documented as a **runbook** a new teammate could follow on day one.

Seven weeks built the individual skills: you can branch, merge, rebase, review a pull request, and gate a repo with CI. Week 8 is where those pieces stop being isolated tricks and become a **system** — the set of agreements a team makes so that dozens of people can push to the same codebase every day without stepping on each other or shipping broken code.

There is no single "correct" workflow. Trunk-based development, GitFlow, and GitHub flow each optimize for a different reality — release cadence, team size, how scary a rollback is. The senior skill is not memorizing one; it's **choosing** the right one for a given team and defending the choice with trade-offs. This week you compare all three, lock a repo down with protection and CODEOWNERS, wire CI as a hard merge gate, automate releases with Conventional Commits and SemVer, and finally assemble everything into a capstone: a small team's end-to-end workflow, run for real and written up as a runbook.

Everything here is free. Public GitHub repositories get unlimited Actions minutes, branch protection, and CODEOWNERS at no cost.

---

## How to navigate this week

Work top to bottom. The lectures build the decision framework, the exercises turn each decision into repo configuration, the challenges force trade-offs under messy constraints, and the capstone assembles the whole thing into one runnable, documented workflow.

| # | Piece | What it delivers | Time |
|--:|-------|------------------|-----:|
| 1 | [lecture-notes/01-branching-strategies-compared.md](./lecture-notes/01-branching-strategies-compared.md) | Trunk-based dev vs GitFlow vs GitHub flow — how each works, what it optimizes for, and the trade-offs that pick between them | ~2h |
| 2 | [lecture-notes/02-protected-branches-and-ownership.md](./lecture-notes/02-protected-branches-and-ownership.md) | Branch protection, CODEOWNERS, required reviews, and the monorepo vs multi-repo decision | ~2h |
| 3 | [lecture-notes/03-releases-at-scale.md](./lecture-notes/03-releases-at-scale.md) | Semantic Versioning, Conventional Commits, changelogs, and automated releases | ~2h |
| 4 | [exercises/exercise-01-protect-and-own.md](./exercises/exercise-01-protect-and-own.md) | Configure branch protection + a CODEOWNERS file and prove they block an unreviewed merge | ~50m |
| 5 | [exercises/exercise-02-choose-a-strategy.md](./exercises/exercise-02-choose-a-strategy.md) | Pick and justify a branching strategy for a given team scenario | ~45m |
| 6 | [exercises/exercise-03-automate-a-release.md](./exercises/exercise-03-automate-a-release.md) | Automate a versioned release with a GitHub Actions workflow | ~55m |
| 7 | [challenges/challenge-01-migrate-to-trunk.md](./challenges/challenge-01-migrate-to-trunk.md) | Migrate a chaotic long-lived-branch repo to trunk-based development | ~1.5h |
| 8 | [challenges/challenge-02-monorepo-policy.md](./challenges/challenge-02-monorepo-policy.md) | Design a contribution policy for a multi-team monorepo | ~1.5h |
| 9 | [mini-project/README.md](./mini-project/README.md) | **Capstone:** design and run a small team's end-to-end workflow, documented as a runbook | ~5h |
| 10 | [homework.md](./homework.md) | Practice tasks that reinforce each lecture | ~4h |
| 11 | [quiz.md](./quiz.md) | 14 self-check questions with an answer key | ~40m |
| 12 | [resources.md](./resources.md) | Official docs + high-signal external reading | — |

Approximate total: **~20 hours.** Treat it as a target, not a stopwatch.

---

## Prerequisites

- **Weeks 1–7 of C31** completed — you can branch, rebase, run a pull-request review, and write a GitHub Actions workflow that gates merges.
- A **GitHub account** and at least one repo you own (public is easiest — protection and Actions are free).
- **`git` 2.40+** and the **GitHub CLI `gh`** installed and authenticated (`gh auth status`).
- A GitHub **organization** OR a second collaborator account is ideal for the reviews/CODEOWNERS work. Solo? The lectures explain the single-maintainer fallbacks, and every exercise has a solo path.

---

## By the end of this week you can…

- **Compare** trunk-based development, GitFlow, and GitHub flow and pick the right one for a given team, cadence, and risk profile — and say *why* in trade-off terms, not vibes.
- **Protect** a branch: require pull requests, required status checks, linear history, and review approvals, and understand exactly what each rule blocks.
- **Assign ownership** with a `CODEOWNERS` file so the right people are auto-requested as required reviewers on the paths they own.
- **Decide** between a monorepo and multiple repos for a given team, knowing the tooling and policy cost of each.
- **Version** software with Semantic Versioning, write **Conventional Commits**, and generate a changelog and a tagged release **automatically** from commit history.
- **Design and run** a complete team workflow — branching model, protection, CI gates, review rules, and a release — and hand it off as a runbook someone else could operate.

---

## Up next

This is the final week of C31. Your capstone runbook is the artifact you carry into every other Code Crunch track — C15 DevOps, C6 Security, and any team project assumes you can stand up a workflow like this. Ship it, then browse the [full curriculum](../../README.md) for where to go next.

---

*Part of C31 · Crunch Version Control · GPL-3.0. Found an error? Open an issue or PR.*
