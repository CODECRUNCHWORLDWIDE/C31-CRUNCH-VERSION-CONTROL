# Week 6 — Exercises

Three guided reps. Do them in order — each builds on the repo from the one before. Together they take you from "no CI" to "a required check that blocks a merge." Keep everything in one throwaway GitHub repo you create for the week (public, so Actions minutes are free).

| # | Exercise | You practice | Time |
|--:|----------|--------------|-----:|
| 1 | [exercise-01-first-workflow-on-push.md](./exercise-01-first-workflow-on-push.md) | Writing a workflow that runs a test suite on push and PR | ~45m |
| 2 | [exercise-02-matrix-and-caching.md](./exercise-02-matrix-and-caching.md) | Adding a version/OS matrix and caching dependencies | ~50m |
| 3 | [exercise-03-required-status-check.md](./exercise-03-required-status-check.md) | Making a check required via branch protection and proving it blocks a merge | ~40m |

## Before you start

- A GitHub account, `git` 2.40+, and `gh` authenticated (`gh auth status`).
- Node.js 18+ **or** Python 3.11+ locally. The exercises show Node commands with Python equivalents noted; use whichever you know.
- Create the week's repo now:

```bash
mkdir cc-ci-week6 && cd cc-ci-week6
git init
gh repo create cc-ci-week6 --public --source=. --remote=origin
```

## Done-with-the-week checklist

- [ ] Exercise 1: a green check appears on a push and on a PR.
- [ ] Exercise 2: the Actions tab shows multiple matrix jobs; a second run is faster thanks to a cache hit.
- [ ] Exercise 3: a PR with a failing test shows a **disabled** merge button; fixing the test enables it.

Commit each exercise's files to the repo — you'll reuse them in the challenges and mini-project.
