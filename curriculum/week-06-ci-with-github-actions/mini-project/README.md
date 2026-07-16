# Mini-Project — A Repo Where Broken Code Can't Reach `main`

> Build one real, protected repository that ties the whole week together: a working project with a test suite, a CI workflow that runs on every push and PR, and branch protection that makes the CI check a **hard merge requirement**. When you're done, you can hand the repo to anyone, dare them to merge failing code into `main`, and they can't.

**Estimated time:** ~3 hours.

This is the capstone of Week 6 and the concrete artifact you'll point to as proof you can run a CI-gated repo. It's deliberately small in code and serious in configuration — the difficulty is in the *protection*, not the program.

---

## Goal

A public GitHub repository, `cc-ci-gate`, in which:

- A small program exists with a **meaningful test suite** (not a placeholder single assertion).
- A **CI workflow** runs the tests on every push to `main` and every pull request, across a small matrix, with dependency caching.
- **Branch protection** requires the CI check, requires a PR, requires branches be up to date, and **applies to admins**.
- The protection is **reproducible from the repo** (exported ruleset JSON or a documented settings file).
- A short `README.md` explains what the repo is and shows the gate working.

---

## Requirements

1. **The project.** Any language. At least one non-trivial module and **at least 4 tests** covering real behaviour (including at least one edge case). If you reuse the Exercise 1–2 project, expand its tests to qualify.
2. **The workflow** (`.github/workflows/ci.yml`):
   - Triggers on `push` to `main` and on `pull_request`.
   - Runs across a matrix of **at least two runtime versions**.
   - Uses **dependency caching**.
   - Sets **least-privilege `permissions`** (`contents: read` is enough for tests).
   - Produces a clearly named required check.
3. **The protection** on `main`:
   - Require the CI status check to pass.
   - Require a pull request before merging.
   - Require branches to be up to date before merging.
   - Do not allow bypassing (admins included).
4. **The proof.** A section in `README.md` (or a `PROOF.md`) with evidence — `gh pr checks` transcripts or screenshots — showing a failing PR blocked and a passing PR merged.

---

## Milestones

Work in this order; each is a natural commit/PR.

### Milestone 1 — Project + tests (45m)
Create the repo, write the module and its ≥4 tests, confirm they pass locally, push. No CI yet.

```bash
gh repo create cc-ci-gate --public --clone && cd cc-ci-gate
# add your module + tests
npm test         # or pytest -q — all green locally
git add -A && git commit -m "Project with test suite" && git push
```

### Milestone 2 — CI workflow (45m)
Add `ci.yml` with matrix + caching + least-privilege permissions. Push and confirm green runs in the Actions tab. Deliberately break a test on a branch, open a PR, watch the check go red, then fix it — proving the workflow reacts.

### Milestone 3 — Branch protection (30m)
Add the protection rule on `main` (UI or `gh api`). Export it into the repo:

```bash
# list rulesets, then export the one you made
gh api repos/OWNER/cc-ci-gate/rulesets | jq '.[].id'
gh api repos/OWNER/cc-ci-gate/rulesets/<id> > ruleset.json
git add ruleset.json && git commit -m "Vendor branch protection ruleset"
```

### Milestone 4 — Prove the gate (30m)
Run the break-it/fix-it ritual one final time as formal evidence. Capture:
- A PR with a failing test → check red, **merge button disabled**.
- The same PR fixed → check green, **merge succeeds**.
Paste the `gh pr checks` output (or screenshots) into your proof section.

### Milestone 5 — Document + polish (30m)
Write the `README.md`: what the repo is, how CI is configured, how the gate is enforced, and the proof. Re-read it as if you'd never seen the repo.

---

## Acceptance criteria

- [ ] `cc-ci-gate` is public and has a program with **≥4 real tests**.
- [ ] `.github/workflows/ci.yml` triggers on push + PR, runs a **matrix (≥2 versions)**, and **caches** dependencies.
- [ ] The workflow sets `permissions:` to least privilege.
- [ ] `main` requires the CI check, requires a PR, requires up-to-date branches, and **cannot be bypassed by admins**.
- [ ] `ruleset.json` (or equivalent) captures the protection in the repo.
- [ ] The README shows a failing PR **blocked** and a passing PR **merged**.
- [ ] You (as owner) genuinely cannot merge a red PR.

---

## Rubric

| Criterion | Weight | "Great" looks like |
|-----------|------:|--------------------|
| Gate actually blocks | 30% | A red required check disables merge for everyone, admins included |
| Workflow quality | 25% | Matrix + caching + least-privilege perms, clean and readable |
| Test suite | 15% | ≥4 tests covering real behaviour and an edge case, not filler |
| Reproducibility | 15% | Protection vendored in the repo; someone could recreate it |
| Proof + docs | 15% | README clearly demonstrates the gate working, red then green |

---

## Why this matters

This repo *is* the outcome the whole C31 course builds toward — the syllabus promises you'll "run a protected, CI-gated repository a real team could ship from," and this is the smallest complete instance of one. In Week 7 you'll add advanced Git tooling on top; in Week 8 you'll scale this exact pattern into a full team workflow with release process. Keep `cc-ci-gate` — you'll extend it.

When done: push everything and start [Week 7 — Advanced Git](../../week-07-advanced-git/).
