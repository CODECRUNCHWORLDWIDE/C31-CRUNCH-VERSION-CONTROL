# Week 7 — Exercises

Three exercises, ~40–55 min each. Type the commands, read the output, and commit your artifacts. These build the muscle memory the lectures describe.

1. **[Exercise 1 — Write a pre-commit hook](exercise-01-write-a-pre-commit-hook.md)** — build a real `pre-commit` hook that blocks bad commits, and prove it fires.
2. **[Exercise 2 — Find a bug with `git bisect run`](exercise-02-bisect-run-to-find-a-bug.md)** — plant a regression across many commits, then let bisect drive a test to the exact culprit.
3. **[Exercise 3 — Parallel work with worktrees](exercise-03-parallel-work-with-worktrees.md)** — handle an "urgent hotfix" mid-feature without stashing, using `git worktree`.

## Suggested workflow

- Do them **in order** — Exercise 2 assumes you're comfortable making a run of commits, and Exercise 3 assumes you can branch cleanly.
- Open the exercise file in your editor and a fresh terminal side by side.
- Type each command yourself. Read every line of output before moving on.
- When a command surprises you, stop and check the man page (`git help <command>`) before continuing.

The goal isn't to finish fast — it's that hooks, bisect, and worktrees become tools you reach for without looking them up.
