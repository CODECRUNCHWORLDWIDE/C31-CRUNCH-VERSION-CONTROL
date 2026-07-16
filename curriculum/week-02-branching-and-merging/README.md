# Week 2 — Branching & Merging

> *A branch is not a copy of your project. It is a 41-byte file containing the hash of one commit. Once that clicks, branching stops being scary and merging stops being magic.*

Welcome to Week 2 of **C31 · Crunch Version Control**. Week 1 taught you the object model — blobs, trees, commits, and the three trees (working tree, index, `HEAD`). This week you put that model to work: you'll create branches, move between them, merge them back together, and resolve the conflicts that merging sometimes forces. By the end you will **branch, merge, and resolve conflicts without panic** — the single skill that separates people who "know some Git" from people a team can hand a repository to.

## Learning objectives

By the end of this week, you will be able to:

- **Explain** that a branch is a movable pointer to a commit, that `HEAD` is a pointer to your current branch, and predict exactly what `git switch`, `git checkout`, and `git branch -d` do to those pointers *before* you run them.
- **Create, switch, rename, and delete** branches with both the modern (`git switch`, `git branch`) and legacy (`git checkout`) commands, and know why the modern ones exist.
- **Merge** two branches and tell the difference between a **fast-forward** merge (no new commit) and a **three-way** merge (a new merge commit with two parents) — and force either one on demand.
- **Read** the anatomy of a merge conflict: the `<<<<<<<`, `=======`, `>>>>>>>` markers, "ours" vs. "theirs", and the index's three stages.
- **Resolve** a conflict by hand, with a mergetool, or by aborting — and choose the right one for the situation.
- **Reason** about *when* to merge vs. rebase (a preview; rebase is Week 4) so you don't cargo-cult either one.

## Prerequisites

- **Week 1** completed: you can `init`, `add`, `commit`, and read `git log`, `git status`, and `git cat-file`.
- Git **2.40 or newer** (`git --version`). We use `git switch` and `git restore`, which stabilized in 2.23 and are the recommended commands as of 2.40+.
- A terminal, a text editor, and about **12 hours** across the week.

Everything this week runs **locally**. No GitHub account needed until Week 3 — you can complete every exercise, challenge, and the mini-project offline in a scratch repository.

## The week's goal

> **Goal:** Branch, merge, and resolve conflicts without panic — because you understand that every one of those operations is just Git moving pointers and comparing three snapshots.

## How to navigate this week

Work top to bottom. The lectures build the mental model; the exercises drill it; the challenges and mini-project make you decide, not just follow steps.

| # | File | What's inside | Time |
|---|------|---------------|-----:|
| 1 | [lecture-notes/01-branches-and-heads.md](./lecture-notes/01-branches-and-heads.md) | Branches as movable pointers, `HEAD`, `switch`/`checkout`, create & delete | ~2h |
| 2 | [lecture-notes/02-merging-fast-forward-and-three-way.md](./lecture-notes/02-merging-fast-forward-and-three-way.md) | Fast-forward vs. three-way merges, merge commits, merge-vs-rebase preview | ~2h |
| 3 | [lecture-notes/03-conflicts-anatomy-and-resolution.md](./lecture-notes/03-conflicts-anatomy-and-resolution.md) | Conflict markers decoded, resolving, mergetools, aborting | ~2h |
| 4 | [exercises/exercise-01-branch-and-fast-forward.md](./exercises/exercise-01-branch-and-fast-forward.md) | Create/switch branches and do a fast-forward merge | ~40m |
| 5 | [exercises/exercise-02-three-way-merge.md](./exercises/exercise-02-three-way-merge.md) | Force a three-way merge and dissect the merge commit | ~45m |
| 6 | [exercises/exercise-03-resolve-a-conflict.md](./exercises/exercise-03-resolve-a-conflict.md) | Create and resolve a real conflict by hand | ~50m |
| 7 | [challenges/challenge-01-untangle-branches.md](./challenges/challenge-01-untangle-branches.md) | Untangle a knot of branches into a clean history | ~1.5h |
| 8 | [challenges/challenge-02-choose-a-merge-strategy.md](./challenges/challenge-02-choose-a-merge-strategy.md) | Given a scenario, pick and justify a merge strategy | ~1h |
| 9 | [mini-project/README.md](./mini-project/README.md) | Merge three feature branches into `main`, cleanly | ~3h |
| — | [homework.md](./homework.md) | Six practice problems | ~5h |
| — | [quiz.md](./quiz.md) | 12 self-check questions + answer key | ~30m |
| — | [resources.md](./resources.md) | Curated docs + visual learning tools | — |

## Weekly schedule

Roughly **13 hours**. Treat it as a target, not a contract.

| Day | Focus | Lectures | Exercises | Challenges | Project | Quiz/HW | Total |
|-----|-------|---------:|----------:|-----------:|--------:|--------:|------:|
| Mon | Branches & `HEAD` | 2h | 0.7h | 0h | 0h | 0.5h | 3.2h |
| Tue | Merging | 2h | 0.75h | 0h | 0h | 0.5h | 3.25h |
| Wed | Conflicts | 2h | 0.85h | 0h | 0h | 0.5h | 4.2h |
| Thu | Challenges | 0h | 0h | 2.5h | 0h | 1h | 3.5h |
| Fri–Sat | Mini-project | 0h | 0h | 0h | 3h | 1h | 4h |
| Sun | Quiz + review | 0h | 0h | 0h | 0h | 0.5h | 0.5h |

## By the end of this week you can…

- Draw the commit graph of any small repo from memory and point to where each branch pointer and `HEAD` sit.
- Choose fast-forward vs. `--no-ff` deliberately and say why.
- Walk into a conflict, read the markers, and resolve it in under two minutes instead of deleting the repo and re-cloning.
- Merge multiple feature branches into `main` and end with a history a reviewer can actually read.

## Up next

[Week 3 — Remotes & GitHub](../week-03-remotes-and-github/) — where branches learn to travel between machines.

---

*Part of the Code Crunch Worldwide open curriculum · GPL-3.0 · If you find errors, please open an issue or PR.*
