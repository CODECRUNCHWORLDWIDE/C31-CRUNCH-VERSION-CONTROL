# Week 4 — Rewriting History

> **Goal:** Stop treating your commit log as a permanent record and start treating it as a *draft*. By Sunday you can amend the last commit, replay any commit with `cherry-pick`, reshape a whole branch with interactive `rebase`, and recover anything you "lost" with `reflog` — and you know exactly when rewriting is safe and when it will wreck a teammate's day.

Weeks 1–3 taught you to *build* history: commit, branch, merge, push. Week 4 teaches you to *edit* it. The tools here — `commit --amend`, `reset`, `rebase -i`, `cherry-pick`, `reflog` — are the ones that scare people, because a wrong move looks like it destroyed work. The secret this week hands you: **Git almost never destroys anything.** Every commit you make lives in the object database and stays reachable through the reflog for weeks, even after you "delete" the branch that pointed at it. Once you internalise that, rewriting history stops being terrifying and becomes a normal editing step you do before every code review.

## Learning objectives

By the end of this week, you will be able to:

- **Amend** the most recent commit — fix its message, add a forgotten file, or drop a stray one — and explain why amend creates a *new* commit rather than editing the old one.
- **Distinguish** the three resets (`--soft`, `--mixed`, `--hard`) by what each does to HEAD, the index, and the working tree — and predict the result before running them.
- **Run** an interactive rebase to squash, reorder, drop, edit, reword, and `fixup` commits, including the `--autosquash` workflow that automates the boring parts.
- **Cherry-pick** one or more commits from anywhere in the repo onto your current branch, and resolve the conflicts that follow.
- **Recover** a commit, a branch, or a whole repo state after a bad rebase or hard reset using `git reflog` and `git reset --hard <sha>`.
- **Decide** when rewriting is safe (local, un-pushed, unshared history) and when it is dangerous (anything a teammate has already pulled), and clean up messy work *before* it becomes a pull request.

## Prerequisites

- **Weeks 1–3 of C31** completed. You must be comfortable with `commit`, `log`, `branch`, `checkout`/`switch`, `merge`, and pushing to a remote.
- Git **2.40+** (`git --version`). Older Git works but a few flags (`--autosquash` defaults, `git switch`) differ.
- A scratch repository you can trash. Every exercise this week says "in a repo you don't care about" for a reason — you *will* rewrite history, and you should practise where mistakes are free.

Set an editor for interactive rebase before you start, or the `rebase -i` screen will drop you into `vi` unprepared:

```bash
git config --global core.editor "nano"     # or "code --wait", "vim", etc.
```

## Topics covered

- `git commit --amend` — the "I forgot something" button, and why it rewrites the SHA
- The three trees revisited: HEAD, index, working tree — and the three resets that move them
- `reset --soft` vs `--mixed` vs `--hard` — a decision table you can memorise
- Interactive rebase: `pick`, `reword`, `edit`, `squash`, `fixup`, `drop`, and reordering
- `git commit --fixup` + `git rebase -i --autosquash` — the professional cleanup flow
- `cherry-pick` — replaying commits across branches, ranges, and `--continue`/`--abort`
- `git reflog` — the safety net; how HEAD's movement log lets you undo almost anything
- The Golden Rule of Rebasing: never rewrite history other people depend on

## Weekly schedule

The schedule below adds up to approximately **34 hours**. Treat it as a target, not a contract.

| Day       | Focus                                        | Lectures | Exercises | Challenges | Quiz/Read | Homework | Mini-Project | Self-Study | Daily Total |
|-----------|----------------------------------------------|---------:|----------:|-----------:|----------:|---------:|-------------:|-----------:|------------:|
| Monday    | Amend + the three resets                     |    2h    |    1.5h   |     0h     |    0.5h   |   1h     |     0h       |    0.5h    |     5.5h    |
| Tuesday   | Danger zones; shared vs. local history        |    1h    |    1.5h   |     1h     |    0.5h   |   1h     |     0h       |    0.5h    |     5.5h    |
| Wednesday | Interactive rebase (squash/reorder/edit)     |    2h    |    2h     |     1h     |    0.5h   |   1h     |     0h       |    0h      |     6.5h    |
| Thursday  | `fixup` + `--autosquash`; cherry-pick        |    2h    |    1.5h   |     1h     |    0.5h   |   1h     |     0h       |    0.5h    |     6.5h    |
| Friday    | Reflog + total recovery                      |    0h    |    1.5h   |     0h     |    0.5h   |   1h     |     2h       |    0.5h    |     6h      |
| Saturday  | Mini-project (rewrite a feature branch)      |    0h    |    0h     |     0h     |    0h     |   0.5h   |     3h       |    0h      |     3.5h    |
| Sunday    | Quiz + reflection                            |    0h    |    0h     |     0h     |    0.5h   |   0h     |     0h       |    0h      |     0.5h    |
| **Total** |                                              | **7h**   | **9.5h**  | **4h**     | **3.5h**  | **5.5h** | **7h**       | **2.5h**   | **34h**     |

## How to navigate this week

Work top to bottom. Each lecture has an exercise that drills it; the challenges and mini-project combine everything.

| # | File | What's inside | Time |
|---|------|---------------|------|
| 1 | [lecture-notes/01-amend-and-reset.md](./lecture-notes/01-amend-and-reset.md) | Amend, the three trees, and soft/mixed/hard reset — plus the danger zone | ~2h |
| 2 | [lecture-notes/02-interactive-rebase.md](./lecture-notes/02-interactive-rebase.md) | Squash, reorder, edit, drop, `fixup`, and `--autosquash` | ~2h |
| 3 | [lecture-notes/03-cherry-pick-and-reflog.md](./lecture-notes/03-cherry-pick-and-reflog.md) | Cherry-pick across branches + total recovery with `reflog` | ~2h |
| 4 | [exercises/exercise-01-amend-and-reset.md](./exercises/exercise-01-amend-and-reset.md) | Amend a commit; walk all three resets | ~40m |
| 5 | [exercises/exercise-02-clean-up-with-interactive-rebase.md](./exercises/exercise-02-clean-up-with-interactive-rebase.md) | Turn six sloppy commits into three clean ones | ~50m |
| 6 | [exercises/exercise-03-cherry-pick-and-reflog-recovery.md](./exercises/exercise-03-cherry-pick-and-reflog-recovery.md) | Cherry-pick a fix; recover a discarded commit | ~45m |
| 7 | [challenges/challenge-01-rewrite-messy-branch.md](./challenges/challenge-01-rewrite-messy-branch.md) | Rewrite a messy branch into a reviewable history | ~75m |
| 8 | [challenges/challenge-02-recover-after-bad-rebase.md](./challenges/challenge-02-recover-after-bad-rebase.md) | Rescue a repo after a botched rebase | ~60m |
| 9 | [mini-project/README.md](./mini-project/README.md) | Rewrite a real messy feature branch, review-ready | ~7h |
| 10 | [homework.md](./homework.md) | Six practice problems (~5.5h) | ~5.5h |
| 11 | [quiz.md](./quiz.md) | 12 self-check questions + answer key | ~30m |
| 12 | [resources.md](./resources.md) | Official docs + the best external explainers | — |

## By the end of this week you can…

- Fix the last commit without making a "fix typo" commit.
- Explain, cold, the difference between `reset --soft`, `--mixed`, and `--hard`.
- Take a branch of ten "wip" commits and hand your reviewer three logical ones.
- Move a hotfix from `main` onto your feature branch without merging everything.
- Recover a commit you deleted an hour ago, from memory, in under a minute.
- Say — and mean — "I rebased, but only my own un-pushed work."

## A word of caution before you start

Everything this week is safe **on local, un-pushed commits**. The one rule that turns these tools from useful to dangerous is sharing: once you `push` a commit and someone pulls it, rewriting that commit forces everyone else to untangle divergent history. Lectures 1 and 3 both hammer this, and the quiz tests it. Rewrite freely in your own sandbox; think twice before rewriting anything already on a shared branch.

---

*Part of [C31 · Crunch Version Control](../../README.md) · GPL-3.0. Found an error? Open an issue or PR.*

*Up next: [Week 5 — Pull Requests & Review](../week-05-pull-requests-and-review/) — where your newly clean history becomes a reviewable PR.*
