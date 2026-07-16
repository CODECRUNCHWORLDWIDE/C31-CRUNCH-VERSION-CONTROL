# Week 4 — Exercises

Three exercises, ~40–50 min each. Do them in a **throwaway repo** — every one rewrites history, and you want your mistakes to be free. Type the commands, don't paste; the muscle memory is the point.

1. **[Exercise 1 — Amend and reset scenarios](exercise-01-amend-and-reset.md)** — fix the last commit, then walk all three resets and feel the difference.
2. **[Exercise 2 — Clean up a branch with interactive rebase](exercise-02-clean-up-with-interactive-rebase.md)** — turn six sloppy commits into three logical ones.
3. **[Exercise 3 — Cherry-pick + reflog recovery](exercise-03-cherry-pick-and-reflog-recovery.md)** — copy a fix onto another branch, then resurrect a commit you "destroyed."

Run them in order — each assumes the tools from the one before.

## Suggested workflow

- Open a fresh terminal in an empty scratch directory.
- Keep the matching lecture open beside you.
- After every history-changing command, run `git log --oneline` **and** `git reflog` and *read them*. Watching the pointers move is how the model sinks in.
- If a command's result surprises you, stop and re-read the lecture section before continuing.

## A safety habit worth forming now

Before any risky rewrite, note your current tip so you always have a way back:

```bash
git log --oneline -1        # or: git branch backup   (a throwaway safety branch)
```

You'll rarely need it — `git reflog` has your back — but the habit costs nothing and calms the nerves.
