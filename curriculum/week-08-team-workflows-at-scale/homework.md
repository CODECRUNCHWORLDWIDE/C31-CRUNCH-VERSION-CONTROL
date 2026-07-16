# Week 8 — Homework

Six problems, ~4 hours total. Each reinforces one lecture. Commit each to your portfolio under `c31-week-08/homework/`.

---

## Problem 1 — Strategy field study (40 min)

Pick **three real open-source projects** you use or admire (check their GitHub). For each, infer the branching strategy from evidence: do they have a `develop` branch? release branches? do PRs merge straight to `main`? how long do branches live?

**Acceptance:**

- `field-study.md` with a short section per project: the strategy you think they use, and the **evidence** for it (branch list, PR history, release tags).
- One sentence per project on whether the strategy fits their release cadence.
- One project where you'd argue they should switch, and to what.

---

## Problem 2 — Protection rule audit (35 min)

Take a repo you own (or fork a small one). Enumerate every branch-protection rule available and, for each, write one line: *what does turning this on block, and when would you want it?*

**Acceptance:**

- `protection-audit.md` listing all the rules from Lecture 2 §1, each with a "blocks / use when" note.
- Your recommended baseline set for a 4-person team, with a one-line justification.
- An explicit note on why "Include administrators" is on or off in your recommendation.

---

## Problem 3 — CODEOWNERS puzzle (30 min)

Given this `CODEOWNERS`:

```gitignore
*              @org/maintainers
/web/          @org/frontend
/web/checkout/ @org/payments
*.md           @org/docs
```

For each path, state **who is the required owner** (remember: last match wins):

1. `/web/checkout/cart.js`
2. `/web/checkout/README.md`
3. `/web/home.js`
4. `/services/api.py`
5. `/README.md`

**Acceptance:**

- `codeowners-puzzle.md` with your answer *and* the rule that decided it for each path.
- One sentence: why did `/web/checkout/README.md` surprise you (or not)?

---

## Problem 4 — Monorepo vs multi-repo memo (40 min)

Your company has 5 services that share two libraries and are released on different schedules by three teams. Write a **one-page recommendation**: monorepo or multi-repo?

**Acceptance:**

- `repo-shape.md`: your recommendation, three concrete reasons, and the single biggest cost you're accepting.
- A "what would flip my decision" paragraph (Lecture 2 §5).

---

## Problem 5 — Version the changes (30 min)

Given a repo at `v3.4.2` and this set of commits since the tag, compute the **next version** and explain:

```
fix: handle empty response body
docs: update the README badge
feat: add CSV export
chore: bump lockfile
feat!: rename the --output flag to --out
```

**Acceptance:**

- `versioning.md` with the next version number and a one-line reason per commit (does it bump? which level?).
- The final version and *which single commit* determined the MAJOR/MINOR/PATCH level.

---

## Problem 6 — Write ten Conventional Commits (25 min)

Here are ten change descriptions in plain English. Rewrite each as a valid Conventional Commit message (correct type, optional scope, `!`/`BREAKING CHANGE` where needed):

1. Fixed a crash when the user list is empty.
2. Added dark mode to the settings page.
3. Updated the contributing guide.
4. Removed the deprecated `/v1/login` endpoint (callers must move to `/v2/login`).
5. Sped up the search index build.
6. Reformatted the codebase with the new linter.
7. Added dependency caching to the CI test workflow.
8. Added a new `--dry-run` flag to the deploy command.
9. Fixed a typo in an error message.
10. Changed the default timeout from 30s to 10s (this can break slow callers).

**Acceptance:**

- `commits.md` with all ten rewritten, and for each, the SemVer effect (none / PATCH / MINOR / MAJOR).

---

## Time budget

| Problem | Time |
|--------:|----:|
| 1 | 40 min |
| 2 | 35 min |
| 3 | 30 min |
| 4 | 40 min |
| 5 | 30 min |
| 6 | 25 min |
| **Total** | **~3.5 h** |

After homework, take the [quiz](./quiz.md) and then ship the [capstone mini-project](./mini-project/README.md).
