# Week 5 — Exercises

Three guided reps, ~35–40 min each. Do them **in order** — each builds on the last, and together they walk one change through a full review cycle.

1. **[Exercise 1 — Open a PR from a feature branch](exercise-01-open-a-pr.md)** — branch, commit, push, and open a well-formed pull request.
2. **[Exercise 2 — Review a PR with comments + a suggestion](exercise-02-review-a-pr.md)** — leave line comments, propose a one-click suggestion, pick a verdict.
3. **[Exercise 3 — Address feedback and merge](exercise-03-address-feedback-and-merge.md)** — apply suggestions, push fixes, resolve threads, and merge clean.

## What you need

- A GitHub account and `gh` installed and authenticated (`gh auth login`). Check with `gh auth status`.
- A **review partner** for the full effect. No partner? Use a second GitHub account, or review your own PR from a second browser profile — GitHub won't let you formally *approve* your own PR, but you can leave comments and suggestions, which is enough to learn the mechanics. Notes in each exercise cover the solo path.
- Everything works from the **web UI** too. The `gh` commands are the fast path; the web steps are given alongside.

## The running example

All three exercises operate on one tiny repo you'll create in Exercise 1: a two-function Python module with a small, deliberate rough edge for the reviewer to catch. Don't overthink the code — the *code* is not the point. The **workflow** is the point: opening, reviewing, and merging a PR the way a real team does.

## Suggested workflow

- Do Exercise 1 fully (you end with an open PR).
- If you have a partner, swap repos for Exercise 2 and review *theirs*. Solo, review your own.
- Come back to your own PR for Exercise 3 and take it to merge.
- Keep the repo — the mini-project reuses this exact setup.
