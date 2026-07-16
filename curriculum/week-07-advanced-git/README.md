# Week 7 — Advanced Git

> *The daily commands got you a working repo. The power tools get you out of trouble — and let you ship. This week is the toolbox you open when the ordinary moves aren't enough: automate checks, work in parallel, find the needle in 300 commits, and cut a release people can trust.*

Welcome to Week 7 of **C31 · Crunch Version Control**. You can already branch, merge, rebase, review, and gate a repo with CI. This week adds the tools that separate "competent with Git" from "unstuck under pressure": **hooks** to automate what discipline can't, **`bisect`** to pin down a regression by binary search, **worktrees** to work on two branches at once, **submodules** (and when to run from them), and **tags + releases** to ship a named version. You'll reach for each of these rarely — but when the moment comes, they turn a lost day into five minutes.

## The week's goal

**Reach for hooks, `bisect`, worktrees, submodules, tags, and releases when they fit — and know when they don't.** By Sunday you will have written a hook that blocks bad commits, driven `git bisect run` to an exact culprit across a long history, juggled two worktrees without stashing, and cut a real semver-tagged GitHub Release from the command line.

## Learning objectives

By the end of this week, you will be able to:

- **Write** a client-side hook (a working `pre-commit`), explain what each common hook does, and know why an unskippable rule belongs in CI or a server-side hook — not just `pre-commit`.
- **Find** the commit that introduced a bug with `git bisect`, both by hand and automatically with `git bisect run`, using the exit-code contract (0 good, 125 skip, else bad) and handling un-buildable or flaky commits.
- **Use** `git worktree` to check out multiple branches at once, sharing one object database, and say why worktrees beat stashing for interruptions.
- **Decide** whether **submodules** fit a problem — and prefer package managers, `git subtree`, or vendoring when they don't.
- **Version** software with **semantic versioning**, create and push **annotated** tags, and understand `git describe`.
- **Cut** a GitHub Release from a tag with `gh release`, attaching notes and build artifacts.

## Prerequisites

- Weeks 1–6 of C31 complete: you're comfortable with commits, branches, remotes, rebasing, PRs, and GitHub Actions.
- **Git 2.40+** (`git --version`) and the **GitHub CLI** authenticated (`gh auth login`). Install guides are in [`resources.md`](./resources.md).
- A GitHub account you can push to (the mini-project publishes a real release).

## Topics covered

- Hooks: the `.git/hooks/` mechanism, client vs. server side, blocking vs. reactive, a real `pre-commit`, and sharing hooks across a team
- `git bisect`: binary search over history, `good`/`bad`/`skip`, and full automation with `git bisect run`
- `git worktree`: multiple working directories, the one-branch-per-worktree rule, and clean teardown
- Submodules: the pointer model, the cloning trap, the pitfalls — and the alternatives (`subtree`, package managers, vendoring)
- Tags: lightweight vs. annotated, pushing tags, `git describe`
- Semantic versioning and GitHub Releases with `gh release`

## How to navigate this week

Work top to bottom. Each piece assumes the ones above it.

| # | File | What's inside | Time |
|--:|------|---------------|-----:|
| 1 | [lecture-notes/01-hooks-and-bisect.md](./lecture-notes/01-hooks-and-bisect.md) | Hooks (client + server, a real `pre-commit`) and finding regressions with `git bisect` | ~2h |
| 2 | [lecture-notes/02-worktrees-and-submodules.md](./lecture-notes/02-worktrees-and-submodules.md) | `git worktree` for parallel work; submodules and when to avoid them | ~2h |
| 3 | [lecture-notes/03-tags-and-releases.md](./lecture-notes/03-tags-and-releases.md) | SemVer, annotated tags, GitHub Releases, and `gh release` | ~2h |
| 4 | [exercises/exercise-01-write-a-pre-commit-hook.md](./exercises/exercise-01-write-a-pre-commit-hook.md) | Build a `pre-commit` hook and watch it block bad commits | ~45m |
| 5 | [exercises/exercise-02-bisect-run-to-find-a-bug.md](./exercises/exercise-02-bisect-run-to-find-a-bug.md) | Plant a regression, then let `git bisect run` find it | ~50m |
| 6 | [exercises/exercise-03-parallel-work-with-worktrees.md](./exercises/exercise-03-parallel-work-with-worktrees.md) | Handle an urgent hotfix mid-feature with a worktree | ~40m |
| 7 | [challenges/challenge-01-automate-repo-checks-with-hooks.md](./challenges/challenge-01-automate-repo-checks-with-hooks.md) | Design a shareable, team-grade hook setup | ~1.5h |
| 8 | [challenges/challenge-02-track-down-a-regression-with-bisect.md](./challenges/challenge-02-track-down-a-regression-with-bisect.md) | Hunt a subtle bug through a long, noisy history | ~1.5h |
| 9 | [mini-project/README.md](./mini-project/README.md) | Bisect a planted bug and cut a tagged, semver GitHub Release | ~4–5h |
| 10 | [homework.md](./homework.md) | Six practice problems across all six tools | ~5h |
| 11 | [quiz.md](./quiz.md) | 13 self-check questions with an answer key | ~30m |
| 12 | [resources.md](./resources.md) | Official docs + the best free deep-dives | — |

Indexes: [exercises/README.md](./exercises/README.md) · [challenges/README.md](./challenges/README.md).

## By the end of this week you can…

- Write a hook that stops a class of mistake before it ever lands — and explain why CI is the backstop the hook can't be.
- Point `git bisect run` at a regression buried in hundreds of commits and get the exact culprit in a handful of steps.
- Keep two branches checked out at once and switch contexts without a single `git stash`.
- Choose deliberately between a submodule, a subtree, a package, or vendoring — and defend the call.
- Turn a fixed commit into a named, semver-tagged GitHub Release with notes and a downloadable artifact.

## Up next

[Week 8 — Team Workflows at Scale](../week-08-team-workflows-at-scale/) — the capstone, where hooks, CI, tags, and releases become one repeatable process a real team ships from.

---

*Part of the Code Crunch Worldwide open curriculum · GPL-3.0. Found an error? Open an issue or PR.*
