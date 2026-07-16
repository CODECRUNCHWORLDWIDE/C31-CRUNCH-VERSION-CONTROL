# Exercise 3 — Make a Status Check Required

**Goal:** Promote your CI check from *advice* to a *gate*. You'll configure branch protection so `main` requires the `test` check to pass, then prove the gate works by trying — and failing — to merge broken code.

**Estimated time:** 40 minutes.

## Precondition

Your workflow from Exercises 1–2 must have **run at least once** on the repo, so GitHub knows the check name. Confirm with `gh run list` — you should see completed `CI` runs.

> **Simplify the matrix first (recommended).** A matrix produces many separately-named checks (`test (ubuntu-latest, 20)`, etc.), and you'd have to require each one. For this exercise, temporarily collapse to a single combination so there's exactly one check named `test` to require. Either drop the matrix, or add one representative non-matrix job. You can re-expand later.

## Task A — protect the branch

Using the UI:

1. Repo → **Settings** → **Branches** → **Add branch protection rule** (or **Settings → Rules → Rulesets → New branch ruleset**).
2. Branch name pattern: `main`.
3. Check **Require a pull request before merging**.
4. Check **Require status checks to pass before merging.**
5. In the search box, type `test` and select the check.
6. Check **Require branches to be up to date before merging.**
7. Save.

Or with the CLI (replace `OWNER`):

```bash
gh api -X PUT repos/OWNER/cc-ci-week6/branches/main/protection \
  -H "Accept: application/vnd.github+json" \
  -f "required_status_checks[strict]=true" \
  -f "required_status_checks[contexts][]=test" \
  -F "enforce_admins=false" \
  -f "required_pull_request_reviews[required_approving_review_count]=0" \
  -f "restrictions=null"
```

Confirm it stuck:

```bash
gh api repos/OWNER/cc-ci-week6/branches/main/protection | jq '.required_status_checks'
```

## Task B — try to merge broken code

1. Branch and break a test on purpose:

```bash
git checkout -b try-to-break-main
sed -i.bak 's/sum(2, 3), 5/sum(2, 3), 99/' test/sum.test.js && rm -f test/sum.test.js.bak
git commit -am "Deliberately break the sum test"
git push -u origin try-to-break-main
gh pr create --fill
```

2. Watch the check fail and inspect the merge state:

```bash
gh pr checks           # test → fail
gh pr view             # "Merging is blocked" / required check not met
```

3. **Try to merge anyway** and confirm it's refused:

```bash
gh pr merge --squash   # should be rejected: required status check has not succeeded
```

In the browser, the green **Merge** button is greyed out with "Required statuses must pass before merging."

## Task C — fix it and merge

```bash
sed -i.bak 's/sum(2, 3), 99/sum(2, 3), 5/' test/sum.test.js && rm -f test/sum.test.js.bak
git commit -am "Fix the sum test"
git push
gh pr checks           # test → pass
gh pr merge --squash --delete-branch
```

The merge now succeeds because the required check is green.

## Expected result

- Broken code produces a red check **and** a disabled merge button.
- Merging is impossible until the check passes.
- Fixing the code turns the check green and re-enables merge.

## Done when…

- [ ] `main` has branch protection requiring the `test` check.
- [ ] You observed a merge being **refused** while the check was red.
- [ ] The same PR merged successfully once the check went green.
- [ ] You can state the difference between a status check and a *required* status check in one sentence.

## Stretch

- Enable **Do not allow bypassing** (or **Include administrators**) and confirm that even you, as owner, can't merge a red PR.
- Recreate the "check never ran" deadlock: add a `paths: ['src/**']` filter to the workflow, open a PR that touches only the README, and watch the required check hang **pending** so the PR is stuck. Then remove the filter to unstick it — this is the trap from Lecture 3 §5.1, felt firsthand.
- Re-expand the matrix and require *all* combinations; note how the merge now waits for every leaf.
