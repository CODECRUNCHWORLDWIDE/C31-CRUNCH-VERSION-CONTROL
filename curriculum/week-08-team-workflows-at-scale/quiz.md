# Week 8 — Quiz

Fourteen questions. Lectures closed. Aim for 12/14 before you call the capstone done.

---

**Q1.** The single biggest driver of merge pain across all branching strategies is:

- A) The number of contributors.
- B) How long branches live away from the mainline.
- C) Whether you use squash or merge commits.
- D) The programming language.

---

**Q2.** Trunk-based development merges incomplete features safely by using:

- A) Long-lived `feature/*` branches.
- B) A `develop` branch.
- C) Feature flags that default to off.
- D) Release branches.

---

**Q3.** Which strategy has *native* branch types for patching an already-shipped version while the next one is in development?

- A) Trunk-based development.
- B) GitHub flow.
- C) GitFlow.
- D) None of them.

---

**Q4.** GitHub flow is best described as:

- A) The same as GitFlow but renamed.
- B) Trunk-based development with a mandatory pull-request checkpoint.
- C) A strategy requiring a `develop` and a `release` branch.
- D) A strategy with no code review.

---

**Q5.** You deploy a web app ~15 times a day, have strong CI, and only one version is ever live. The best-fit strategy is:

- A) GitFlow, for safety.
- B) Trunk-based (or GitHub flow), for integration speed.
- C) A separate `develop` branch per engineer.
- D) No branching at all.

---

**Q6.** "Require a pull request before merging," on its own, does **not** guarantee:

- A) That direct pushes to the branch are blocked.
- B) That the change arrived via a PR.
- C) That anyone reviewed it or that CI passed.
- D) That the branch exists.

---

**Q7.** In branch protection, the rule people most often forget — leaving a hole in the gate — is:

- A) Require linear history.
- B) Include administrators.
- C) Require signed commits.
- D) Block force pushes.

---

**Q8.** In a `CODEOWNERS` file, when two patterns match the same file, the owner is decided by:

- A) The most specific pattern.
- B) The first matching pattern.
- C) The last matching pattern.
- D) Alphabetical order of owners.

---

**Q9.** For a `CODEOWNERS` entry to actually assign a reviewer, the listed owner must:

- A) Be an organization admin.
- B) Have write access to the repo.
- C) Have signed a CLA.
- D) Be listed in `.github/config.yml`.

---

**Q10.** A change that is trivial in a monorepo but painful in multi-repo is:

- A) Restricting access to one service.
- B) An atomic change to a shared API and all its callers at once.
- C) Releasing one service independently.
- D) Keeping the clone size small.

---

**Q11.** Under Semantic Versioning, bumping `1.4.2 → 2.0.0` signals:

- A) A backward-compatible bug fix.
- B) A backward-compatible new feature.
- C) An incompatible (breaking) change.
- D) Nothing; it's just a bigger number.

---

**Q12.** A commit `feat!: rename the config key` (note the `!`) triggers which SemVer bump?

- A) PATCH.
- B) MINOR.
- C) MAJOR.
- D) None.

---

**Q13.** Given commits since `v1.4.0`: one `fix:`, one `feat:`, and one `chore:`. The next version is:

- A) `v1.4.1`
- B) `v1.5.0`
- C) `v2.0.0`
- D) `v1.4.0`

---

**Q14.** The practical difference between `release-please` and `semantic-release` is:

- A) One uses SemVer and the other doesn't.
- B) `release-please` opens a Release PR you merge to trigger the release; `semantic-release` releases immediately on a releasable push.
- C) `semantic-release` can't generate changelogs.
- D) They only differ in language support.

---

## Answer key

<details>
<summary>Reveal after attempting</summary>

1. **B** — branch lifetime; long-lived branches accrue merge debt that compounds.
2. **C** — feature flags let you merge off-by-default code instead of hiding it on a long branch.
3. **C** — GitFlow's `release/*` and `hotfix/*` branches handle parallel versioned releases.
4. **B** — GitHub flow keeps an always-deployable `main` and short branches, adding a required PR checkpoint.
5. **B** — continuous deploy + strong CI + one live version is the trunk-based / GitHub flow sweet spot; GitFlow would be over-engineering.
6. **C** — requiring a PR blocks direct pushes but does not by itself require review or passing CI; those are separate rules.
7. **B** — "Include administrators"; without it, admins bypass the gate.
8. **C** — last matching pattern wins (put general rules first, specific last).
9. **B** — owners must have write access or GitHub ignores them.
10. **B** — a monorepo lets one atomic PR change an API and all callers; multi-repo needs coordinated cross-repo PRs.
11. **C** — MAJOR means an incompatible change.
12. **C** — the `!` marks a breaking change → MAJOR.
13. **B** — a `feat` dominates a `fix`; `chore` doesn't bump → MINOR → `v1.5.0`.
14. **B** — the trigger model differs: Release PR (human "go") vs. immediate release on push.

</details>

If you scored 12+: ship the capstone. 9–11: re-read the lecture behind each miss. <9: re-read all three lectures before the mini-project.
