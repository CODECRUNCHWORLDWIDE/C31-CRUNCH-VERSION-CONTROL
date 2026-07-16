# Week 1 — Exercises

Three exercises, ~40–50 min each. Type the commands yourself — don't paste. The goal is to *see* the object model move, not to finish fast.

1. **[Exercise 1 — Init and inspect commits](exercise-01-init-and-inspect-commits.md)** — make a repo, make commits, and read what actually got stored.
2. **[Exercise 2 — Selective staging and reading diffs](exercise-02-selective-staging-and-diffs.md)** — craft a commit hunk-by-hunk with `git add -p`, and read all three kinds of diff.
3. **[Exercise 3 — Explore the object database](exercise-03-explore-the-object-database.md)** — walk `commit → tree → blob` with plumbing commands.

Run them in order — each builds on the previous. Do them after the matching lecture:

| Exercise | Do it after | Reinforces |
|----------|-------------|-----------|
| 1 | Lecture 2 | The three trees; commits as snapshots |
| 2 | Lecture 2 | The index as a real, precise draft |
| 3 | Lecture 3 | The object graph + plumbing |

## Suggested workflow

- Open a fresh terminal in a scratch directory (e.g. `~/c31-w1/`).
- Keep the exercise open beside it.
- Type each command, then **predict the output before you read it**. The prediction is the point.
- When output surprises you, stop and re-read the relevant lecture section before continuing.

Muscle memory beats completion. If you only get through two, that's fine — do them well.
