# Week 2 — Exercises

Three guided reps, ~40–50 min each. **Type the commands; don't paste.** Muscle memory is the point — you want your fingers to know `git switch -c` before your brain has to think about it.

1. **[Exercise 1 — Branch and fast-forward](exercise-01-branch-and-fast-forward.md)** — create and switch branches, then do a fast-forward merge and watch the pointer slide.
2. **[Exercise 2 — Force a three-way merge](exercise-02-three-way-merge.md)** — make two branches diverge, merge them, and dissect the merge commit's two parents.
3. **[Exercise 3 — Resolve a real conflict](exercise-03-resolve-a-conflict.md)** — deliberately create a conflict and resolve it by hand, then with a tool.

Run them **in order** — each assumes you're fluent in the previous one.

## Suggested workflow

- Open a fresh terminal in a throwaway directory (`~/git-w2/`). These exercises create scratch repos you can delete afterward.
- Keep `git lg` (the alias from Lecture 1: `git config --global alias.lg "log --oneline --graph --all --decorate"`) running mentally after every step. *Watch the pointers move.*
- After each `git merge`, run `git log --oneline --graph` and confirm the shape matches what you predicted.
- If something confuses you, `git merge --abort` (or delete the scratch repo) and redo it slowly. Nothing here is precious.

The goal isn't to finish fast — it's to reach the point where you can *predict Git's output before you press Enter.*
