# Mini-Project — Bisect a Bug, Cut a Release

> Build a small program with a real history, plant and then **hunt down** a regression with `git bisect`, fix it, and ship the fix as a properly versioned, tagged **GitHub Release** — notes and artifact included. This is the week in one deliverable.

**Estimated time:** 4–5 hours, spread across the back half of the week.

This mini-project ties together everything from Week 7: you'll use `bisect` to find a bug the way you would in a real codebase, apply semantic versioning to decide the new version number, create an **annotated tag**, and cut the release with `gh release` — the full "find it, fix it, ship it" loop that professional teams run every week.

You'll need a GitHub account and the GitHub CLI (`gh`) authenticated (`gh auth login`). Everything else is free.

---

## Deliverable

A public GitHub repo named `c31-w7-release-lab` containing:

1. A small working program (any language) with a **testable behaviour**.
2. A history of **20+ commits**, with **one planted regression** in the middle.
3. A `HUNT.md` documenting the bisect that found the bug (endpoints, `bisect log`, culprit SHA, step count).
4. A fix commit that resolves the regression.
5. At least **two annotated tags** following SemVer (e.g. `v1.0.0` before the bug era, `v1.1.0` for the fix).
6. A **GitHub Release** on the fix tag, with generated or hand-written notes and **one attached build artifact** (a zip/tarball of the program, or a checksums file).
7. A `README.md` explaining what the program does and how to run it.

---

## Milestones

### Milestone 1 — Build the program and a clean history (1h)

Write something small with a behaviour you can test from the command line — a CSV sorter, a temperature converter, a checksum tool, anything. Commit it, then make **15–20 more commits** of realistic-looking evolution (features, doc updates, small refactors). Tag an early good commit `v1.0.0` with an **annotated** tag:

```bash
git tag -a v1.0.0 -m "First stable release"
git push --follow-tags
```

### Milestone 2 — Plant a regression (30m)

Somewhere in the middle of the history (before you've added the last several commits), introduce a subtle bug — one that breaks your testable behaviour but doesn't crash loudly. Keep committing normal-looking changes on top so the bug is buried. Record privately (not in the repo) which commit you broke, so you can check bisect's answer.

### Milestone 3 — Hunt it with bisect (1h)

Pretend you don't know where the bug is. Write a check script (outside the repo), then:

```bash
git bisect start
git bisect bad HEAD
git bisect good v1.0.0
git bisect run /tmp/check.sh
```

Save `git bisect log`. Confirm bisect's "first bad commit" matches what you planted. Write it all up in `HUNT.md`. Then `git bisect reset`.

### Milestone 4 — Fix and version (30m)

Fix the bug on `main` in a clear commit. Decide the new version by **SemVer**: a bug fix with no new features and no breaking changes is a **PATCH** or **MINOR** bump — justify your choice in `HUNT.md`. Create the annotated tag:

```bash
git tag -a v1.1.0 -m "Fix export ordering regression (found via bisect)"
git push --follow-tags
```

### Milestone 5 — Build an artifact and cut the release (1h)

Produce a distributable artifact (even a simple `tar.gz` of the source plus a `checksums.txt` counts), then release it:

```bash
tar -czf app-v1.1.0.tar.gz app.py README.md
sha256sum app-v1.1.0.tar.gz > checksums.txt

gh release create v1.1.0 \
  --title "v1.1.0 — Export ordering fix" \
  --generate-notes \
  app-v1.1.0.tar.gz checksums.txt

gh release view v1.1.0     # verify it published
```

---

## Acceptance criteria

- [ ] The repo has 20+ commits and a clearly planted-then-fixed regression.
- [ ] `HUNT.md` shows the bisect endpoints, `bisect log`, the culprit SHA/message, and the step count (~`log2(N)`).
- [ ] Bisect's answer matches the commit you actually broke.
- [ ] There are **two annotated tags** (verify with `git show <tag>` showing a tagger and message — not lightweight).
- [ ] A GitHub Release exists on the fix tag with notes and **at least one attached artifact**.
- [ ] `HUNT.md` justifies the version bump against the SemVer rules.

---

## Rubric

| Criterion | Weight | "Great" looks like |
|-----------|------:|--------------------|
| Bisect hunt | 30% | Automated `bisect run`, correct culprit, honest step count and log |
| SemVer reasoning | 20% | Version bump defended against MAJOR/MINOR/PATCH rules |
| Annotated tags | 15% | Both tags annotated, pushed, and verifiable with `git show` |
| Release quality | 20% | Release has meaningful notes and a real, downloadable artifact |
| Documentation | 15% | `README.md` + `HUNT.md` let a stranger reproduce the whole thing |

---

## Why this matters

This is the loop every shipping team runs: something breaks, you find *which change* broke it, you fix it, and you ship a named version other people can trust. Do it once here on a toy repo and the muscle transfers directly to production. In Week 8 you'll wrap this loop in a team workflow — protected branches, required CI, and a release process the whole team follows.

---

When done: push everything, confirm the release page renders on GitHub, and move on to [Week 8 — Team Workflows at Scale](../../week-08-team-workflows-at-scale/), the capstone.
