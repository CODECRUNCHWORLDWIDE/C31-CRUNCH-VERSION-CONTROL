# Week 6 — Quiz

Fourteen questions. Lectures closed. Aim for 12/14 before moving to Week 7. A mix of multiple-choice and short-answer — answer the short ones in a sentence.

---

**Q1.** In the GitHub Actions model, what triggers a workflow to run?

- A) A job
- B) A step
- C) An event
- D) A runner

---

**Q2.** Two jobs in the same workflow, with no `needs:` between them, will:

- A) Run sequentially, sharing a filesystem
- B) Run in parallel, on separate runners, not sharing a filesystem
- C) Run in parallel, sharing a filesystem
- D) Refuse to run — a workflow can have only one job

---

**Q3.** Where must a workflow file live for GitHub to find it?

- A) Anywhere in the repo, named `*.yml`
- B) In `.github/`
- C) In `.github/workflows/`
- D) In `.ci/`

---

**Q4.** What is the significance of a GitHub-hosted runner being *ephemeral*?

*(Short answer.)*

---

**Q5.** In CI, how does GitHub decide whether a `run:` step passed or failed?

- A) By scanning the log for the word "error"
- B) By the step's exit code (0 = pass, non-zero = fail)
- C) By whether the step printed anything to stderr
- D) By how long the step took

---

**Q6.** Why is `npm ci` preferred over `npm install` in a CI pipeline?

*(Short answer.)*

---

**Q7.** A matrix defines `os: [ubuntu-latest, windows-latest]` and `version: ['18', '20', '22']`. How many jobs run?

- A) 2
- B) 3
- C) 5
- D) 6

---

**Q8.** What does `fail-fast: false` do in a matrix?

- A) Makes each job time out faster
- B) Cancels all other combinations as soon as one fails
- C) Lets the other combinations finish even after one fails
- D) Retries failed combinations automatically

---

**Q9.** In `actions/cache`, what is the role of the `key`, and why does it usually include `hashFiles('package-lock.json')`?

*(Short answer.)*

---

**Q10.** A cache is a correctness tool: you should cache compiled test output so tests don't need to re-run.

- A) True
- B) False

---

**Q11.** How do you reference a repository secret named `API_TOKEN` in a workflow?

- A) `$API_TOKEN`
- B) `${{ env.API_TOKEN }}`
- C) `${{ secrets.API_TOKEN }}`
- D) `secrets/API_TOKEN`

---

**Q12.** What is the difference between a *status check* and a *required* status check?

*(Short answer.)*

---

**Q13.** You marked the check `test` as required, but PRs still merge while it's red. Which is the LEAST likely cause?

- A) The required check name doesn't match the check the workflow actually reports
- B) An admin is using the bypass option and admins aren't included in the rule
- C) The protection rule targets a different branch pattern than `main`
- D) The runner is `ubuntu-latest` instead of `windows-latest`

---

**Q14.** Explain the "check never ran" deadlock: how does requiring a check on a *path-filtered* workflow get a PR permanently stuck, and what's one fix?

*(Short answer.)*

---

## Answers

<details>
<summary>Reveal after attempting</summary>

1. **C** — an *event* (push, pull_request, schedule, etc.) triggers a workflow.
2. **B** — jobs run in parallel by default on separate runners and do **not** share a filesystem. Steps within one job share the machine; jobs do not.
3. **C** — `.github/workflows/`. Not just `.github/`, and the path is mandatory.
4. **Ephemeral = a fresh, clean VM per job, destroyed after.** Nothing persists between runs unless explicitly cached/uploaded — which is exactly what makes CI trustworthy: no leftover state can hide a bug.
5. **B** — purely the exit code. Exit 0 is pass; non-zero is fail. Printing "error" while exiting 0 is still a *passing* step.
6. **`npm ci` installs exactly the lockfile's versions and fails if the lockfile and `package.json` disagree** — a deterministic, reproducible install. `npm install` can drift and mutate the lockfile, which you don't want in a shared pipeline.
7. **D** — 6 (2 × 3, every OS crossed with every version).
8. **C** — it lets the remaining matrix combinations finish instead of being cancelled on the first failure, so you see *all* failures at once. The default (`true`) cancels siblings on first failure.
9. **The `key` is the exact cache identity; a match restores that cache.** Including `hashFiles('package-lock.json')` means the key changes whenever dependencies change, so a dependency change forces a fresh cache instead of restoring a stale one. `restore-keys` provides prefix fallbacks on a miss.
10. **B / False** — a cache must never affect correctness. Caching compiled test output could hide a bug (stale results). Cache only things that are safe to reconstruct identically, like a dependency download.
11. **C** — `${{ secrets.API_TOKEN }}`. The value is injected at runtime and masked in logs.
12. **A status check is informational — it shows on the PR but does not block anything. A required status check is that check promoted by a branch protection rule to a merge requirement**, so the merge button stays disabled until it's green.
13. **D** — the runner OS is irrelevant to whether the gate blocks. A, B, and C are all real, common causes; D is not.
14. **If the required check comes from a workflow with a `paths` filter, a PR that touches no matching path never runs that workflow, so the required check stays *pending forever* and the PR can never satisfy the requirement — stuck.** Fix: keep the required-check workflow unfiltered, or add an always-running fallback job that reports the same check name.

</details>

**Scoring:** 12+ move on. 9–11: re-read the lecture(s) behind your misses. <9: re-read Lectures 2 and 3 from the top, then retry.
