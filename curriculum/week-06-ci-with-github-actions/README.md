# Week 6 — CI with GitHub Actions

> **Goal:** Build a workflow that runs your test suite on every push and pull request, then wire it into branch protection so **failing checks physically block merges to `main`.** By the end of the week you own a repo where broken code cannot reach the trunk.

Weeks 1–5 got you fluent with Git and a human review workflow: branches, remotes, history, and pull requests. But humans forget to run the tests. Reviewers approve code they didn't actually pull down and execute. The fix is **Continuous Integration** — a machine that checks out every push, runs your tests in a clean environment, and reports back before anyone can merge.

This week you learn the GitHub Actions model from the ground up (workflows, events, jobs, steps, runners), write a real workflow that tests on push and PR with matrix builds and dependency caching, and then close the gate with **required status checks** so the green check isn't just decoration — it's a merge requirement.

Everything here is free: GitHub Actions gives public repositories unlimited minutes, and every tool we use ships with the runner or installs in seconds.

---

## How to navigate this week

Work top to bottom. Lectures build the mental model, exercises drill it, challenges stress it, and the mini-project assembles the whole thing into one protected repo.

| # | Piece | What it delivers | Time |
|--:|-------|------------------|-----:|
| 1 | [lecture-notes/01-what-ci-is-and-the-actions-model.md](./lecture-notes/01-what-ci-is-and-the-actions-model.md) | What CI *is* and why it exists; the Actions object model — workflows, events, jobs, steps, runners | ~2h |
| 2 | [lecture-notes/02-writing-a-workflow.md](./lecture-notes/02-writing-a-workflow.md) | Author a real workflow: trigger on push/PR, run tests, matrix builds, dependency caching, secrets | ~2h |
| 3 | [lecture-notes/03-required-checks-and-branch-protection.md](./lecture-notes/03-required-checks-and-branch-protection.md) | Turn a green check into a *gate*: required status checks + branch protection rules | ~1.5h |
| 4 | [exercises/exercise-01-first-workflow-on-push.md](./exercises/exercise-01-first-workflow-on-push.md) | Write and commit your first workflow that runs a test suite on push | ~45m |
| 5 | [exercises/exercise-02-matrix-and-caching.md](./exercises/exercise-02-matrix-and-caching.md) | Add a build matrix across versions and cache dependencies | ~50m |
| 6 | [exercises/exercise-03-required-status-check.md](./exercises/exercise-03-required-status-check.md) | Make a status check *required* via branch protection and prove it blocks a merge | ~40m |
| 7 | [challenges/challenge-01-broken-code-cannot-merge.md](./challenges/challenge-01-broken-code-cannot-merge.md) | Configure a repo so broken code literally cannot merge — then try to break in | ~1h |
| 8 | [challenges/challenge-02-lint-test-coverage-gates.md](./challenges/challenge-02-lint-test-coverage-gates.md) | Build a PR pipeline with lint + test + coverage gates | ~1.5h |
| 9 | [mini-project/README.md](./mini-project/README.md) | A repo where broken code can't reach `main`: tests + protection wired end to end | ~3h |
| 10 | [homework.md](./homework.md) | Practice tasks that reinforce each lecture | ~4h |
| 11 | [quiz.md](./quiz.md) | 14 self-check questions with an answer key | ~40m |
| 12 | [resources.md](./resources.md) | Official docs + high-signal external reading | — |

Approximate total: **~18 hours.** Treat it as a target, not a stopwatch.

---

## Prerequisites

- **Weeks 1–5 of C31** completed — you can branch, push to a remote, open a pull request, and merge it.
- A **GitHub account** and a repo you own (public is easiest; Actions minutes are unlimited on public repos).
- **`git` 2.40+** and the **GitHub CLI `gh`** installed and authenticated (`gh auth status`).
- A tiny project in **any language** with at least one test. We use a small Node.js and a small Python example; pick whichever you're comfortable with — the CI concepts are identical.

---

## By the end of this week you can…

- **Explain** the Actions object model — how an *event* triggers a *workflow* whose *jobs* run on *runners*, each job a sequence of *steps* — and predict what a workflow will do before you push it.
- **Write** a `.github/workflows/*.yml` file that checks out your code, sets up a language runtime, installs dependencies, and runs your tests on every push and pull request.
- **Scale** a workflow with a **matrix** (test across multiple OS/runtime versions) and speed it up with **dependency caching**.
- **Use secrets** correctly — reference them without ever printing them, and understand why they're masked.
- **Require** a status check through branch protection so a red check blocks the merge button.
- **Diagnose** a failing run from its logs and fix the workflow or the code.

---

## Up next

[Week 7 — Advanced Git](../week-07-advanced-git/) — hooks, `bisect`, worktrees, submodules, tags, and releases.

---

*Part of C31 · Crunch Version Control · GPL-3.0. Found an error? Open an issue or PR.*
