# Week 3 — Remotes & GitHub

> *A commit that lives only on your laptop is one spilled coffee away from gone. This week your history learns to travel: to a server, to a teammate, and back — without losing a single object or overwriting anyone's work.*

Welcome to Week 3 of **C31 · Crunch Version Control**. Weeks 1 and 2 kept everything local — objects, the three trees, branches, merges. This week you connect your repository to the outside world. You'll learn what a *remote* actually is (just a named URL plus a set of cached branch pointers), how `clone`, `fetch`, `pull`, and `push` move objects across it, and how remote-tracking branches let Git tell you "you're 2 ahead, 1 behind" without guessing.

Then we meet **GitHub** — repos, HTTPS vs. SSH, personal access tokens, and the `gh` CLI — and the **fork model**: how you contribute to a project you can't push to directly, and how you keep your copy in sync as the original moves on. By Sunday you will have forked a real repository, pushed a branch, opened a contribution, and synced your fork with upstream — the exact loop that powers every open-source pull request on the planet.

## Learning objectives

By the end of this week, you will be able to:

- **Explain** what a remote is: a short name (like `origin`) bound to a URL, plus remote-tracking branches (`origin/main`) that mirror the server's state *as of your last contact*.
- **Clone** a repository and describe exactly what `git clone` sets up for you (the remote, the tracking branches, and your local `main`).
- **Distinguish** `git fetch` (download, touch nothing local) from `git pull` (fetch **then** integrate) and choose deliberately between them.
- **Push** commits to a remote, set upstream tracking with `-u`, and read the ahead/behind status Git reports.
- **Authenticate** to GitHub over HTTPS with a personal access token *or* over SSH with a key — and know which to pick and why.
- **Drive** GitHub from the terminal with the `gh` CLI: create repos, clone, and open pull requests without leaving the shell.
- **Fork** a repository, add the original as an `upstream` remote, and **sync** your fork when upstream advances.
- **Recover** safely from a rejected non-fast-forward push instead of reaching for `--force` and destroying a teammate's commits.

## Prerequisites

- **C31 Weeks 1–2** completed. You can init, stage, commit, branch, merge, and resolve a conflict without panic.
- **Git 2.40+** installed (`git --version`). Older versions work but a few flags differ; upgrades are free.
- A **free GitHub account**. Sign up at <https://github.com/join> if you don't have one — no paid plan is ever required for this course.
- The **GitHub CLI** (`gh`) installed. Install instructions are in `resources.md`; we use it from Lecture 2 onward.

Everything this week is free and works on macOS, Linux, and Windows (WSL recommended).

## Topics covered

- The remote as a concept: names, URLs, and the `.git/config` entries behind them
- `git clone` decomposed — what it creates and why you rarely run `git remote add` by hand
- Remote-tracking branches (`origin/main`) vs. local branches vs. the actual server
- `git fetch` vs. `git pull` — and why `pull` is `fetch` + `merge` (or `+ rebase`)
- `git push`, upstream tracking (`-u` / `--set-upstream`), and the ahead/behind report
- GitHub essentials: repositories, HTTPS vs. SSH URLs, personal access tokens, SSH keys
- The `gh` CLI as a faster path to repos, clones, and pull requests
- The fork model: `origin` (your fork) vs. `upstream` (the original) and how to keep them in sync
- Non-fast-forward rejections: what "the tip of your branch is behind" really means, and the safe fix

## Weekly schedule

The schedule below adds up to approximately **30 hours**. Treat it as a target, not a contract.

| Day       | Focus                                     | Lectures | Exercises | Challenges | Quiz/Read | Homework | Mini-Project | Daily Total |
|-----------|-------------------------------------------|---------:|----------:|-----------:|----------:|---------:|-------------:|------------:|
| Monday    | Remotes, `origin`, clone, fetch vs. pull  |    2h    |    1h     |     0h     |    0.5h   |   1h     |     0h       |     4.5h    |
| Tuesday   | Push, upstream tracking, GitHub + tokens  |    2h    |    1.5h   |     0h     |    0.5h   |   1h     |     0h       |     5h      |
| Wednesday | The fork model — forking, upstream, sync  |    2h    |    1.5h   |     0h     |    0.5h   |   1h     |     0h       |     5.5h    |
| Thursday  | Challenge 1 — recover a rejected push     |    0h    |    0h     |     1.5h   |    0.5h   |   1h     |     1h       |     4h      |
| Friday    | Challenge 2 — contribute through a fork   |    0h    |    0h     |     1.5h   |    0.5h   |   1h     |     1h       |     4h      |
| Saturday  | Mini-project — fork, contribute, sync     |    0h    |    0h     |     0h     |    0h     |   0h     |     4h       |     4h      |
| Sunday    | Quiz + reflection                         |    0h    |    0h     |     0h     |    0.5h   |   0h     |     2h       |     2.5h    |
| **Total** |                                           | **6h**   | **4h**    | **3h**     | **3h**    | **5h**   | **8h**       | **~29.5h**  |

## How to navigate this week

Work top to bottom. Each piece assumes the ones above it.

| # | File | What's inside | Time |
|--:|------|---------------|-----:|
| 1 | [README.md](./README.md) | This overview | — |
| 2 | [resources.md](./resources.md) | Curated official docs + further reading | — |
| 3 | [lecture-notes/01-remotes-and-origin.md](./lecture-notes/01-remotes-and-origin.md) | Remotes, `origin`, clone, fetch vs. pull, remote-tracking branches | ~2h |
| 4 | [lecture-notes/02-push-and-github.md](./lecture-notes/02-push-and-github.md) | Push, upstream tracking, GitHub (repos, HTTPS vs. SSH, tokens, `gh`) | ~2h |
| 5 | [lecture-notes/03-the-fork-model.md](./lecture-notes/03-the-fork-model.md) | Forking, the `upstream` remote, keeping a fork in sync | ~2h |
| 6 | [exercises/README.md](./exercises/README.md) | Index of exercises | — |
| 7 | [exercises/exercise-01-clone-and-push.md](./exercises/exercise-01-clone-and-push.md) | Clone a repo and push a commit to a remote | ~40m |
| 8 | [exercises/exercise-02-track-and-pull.md](./exercises/exercise-02-track-and-pull.md) | Set up branch tracking and fetch/pull deliberately | ~40m |
| 9 | [exercises/exercise-03-fork-and-sync.md](./exercises/exercise-03-fork-and-sync.md) | Fork a repo and sync it with upstream | ~45m |
| 10 | [challenges/README.md](./challenges/README.md) | Index of challenges | — |
| 11 | [challenges/challenge-01-recover-rejected-push.md](./challenges/challenge-01-recover-rejected-push.md) | Recover safely from a rejected non-fast-forward push | ~75m |
| 12 | [challenges/challenge-02-contribute-through-a-fork.md](./challenges/challenge-02-contribute-through-a-fork.md) | Contribute a change to an upstream repo through a fork | ~75m |
| 13 | [mini-project/README.md](./mini-project/README.md) | Fork, contribute a change, and keep the fork in sync | ~8h |
| 14 | [homework.md](./homework.md) | Six practice problems | ~5h |
| 15 | [quiz.md](./quiz.md) | 12 self-check questions with an answer key | ~0.5h |

## By the end of this week you can…

- Clone any public repo and immediately explain what remote and tracking branches you got.
- Push a branch and set its upstream so future `git push` / `git pull` need no arguments.
- Choose `fetch` when you want to *look* and `pull` when you want to *integrate*.
- Authenticate to GitHub the modern way — a token or an SSH key — never a plaintext password.
- Fork a project, wire up `upstream`, contribute a change, and keep your fork current for the long haul.
- Read a "non-fast-forward" rejection calmly and fix it without `--force`-ing over someone's work.

## Stretch goals

- Read Git's own **"Working with Remotes"** chapter end to end: <https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes>
- Set up **both** an HTTPS token *and* an SSH key on the same machine, then compare the day-to-day feel.
- Configure `gh` as Git's credential helper (`gh auth setup-git`) and notice you stop being asked to log in.
- Skim the difference between `git pull --merge` and `git pull --rebase`; we go deep on rebase in Week 4.

## Up next

[Week 4 — Rewriting History](../week-04-rewriting-history/) — amend, interactive rebase, cherry-pick, and recovering anything with `reflog`. Once your fork is synced and your PR is open, you're ready.

---

*If you find errors, please open an issue or PR. Part of the Code Crunch Worldwide open curriculum · GPL-3.0.*
