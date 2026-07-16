# Exercise 1 — Protect and Own

**Goal:** Lock `main` behind a pull request with required review and a required status check, add a `CODEOWNERS` file, and then *prove* the gate works by trying (and failing) to sneak a change past it.

**Estimated time:** 50 minutes.

## Setup

Create a throwaway repo you own. We'll be locking `main` down; don't do this on something you need to push to freely.

```bash
gh repo create w8-protect-demo --public --clone
cd w8-protect-demo
printf "# w8 protect demo\n" > README.md
mkdir -p src
printf "export const ok = true;\n" > src/app.js
git add . && git commit -m "chore: initial commit"
git push -u origin main
```

Add a trivial CI check so there's a status to require. Create `.github/workflows/ci.yml`:

```yaml
name: ci
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: 'echo "pretend tests pass" && test -f src/app.js'
```

```bash
git add .github/workflows/ci.yml
git commit -m "ci: add a placeholder test workflow"
git push
```

Wait for the run to appear once (`gh run list`) so the check context `ci / test` exists.

## Steps

### 1. Add a CODEOWNERS file

Create `.github/CODEOWNERS` (replace `@your-username` with your actual GitHub handle):

```gitignore
# Default owner for the whole repo
*            @your-username

# Anything under src/ needs the same owner (explicit for the demo)
/src/        @your-username

# CI/release config is owned too
/.github/    @your-username
```

Commit and push it to `main` (still allowed — protection isn't on yet):

```bash
git add .github/CODEOWNERS
git commit -m "chore: add CODEOWNERS"
git push
```

### 2. Turn on branch protection

Apply protection with the API so it's reproducible:

```bash
gh api -X PUT repos/:owner/w8-protect-demo/branches/main/protection \
  --input - <<'JSON'
{
  "required_status_checks": { "strict": true, "contexts": ["ci / test"] },
  "enforce_admins": true,
  "required_pull_request_reviews": {
    "required_approving_review_count": 1,
    "require_code_owner_reviews": true,
    "dismiss_stale_reviews": true
  },
  "required_linear_history": true,
  "allow_force_pushes": false,
  "allow_deletions": false,
  "restrictions": null
}
JSON
```

Confirm it's live:

```bash
gh api repos/:owner/w8-protect-demo/branches/main/protection | jq '{admins: .enforce_admins.enabled, checks: .required_status_checks.contexts, reviews: .required_pull_request_reviews.required_approving_review_count}'
```

### 3. Try to push straight to main (this must fail)

```bash
printf "\nsneaky change\n" >> README.md
git commit -am "docs: try to sneak a change"
git push
```

You should be **rejected** — the push is refused because `main` now requires a pull request. Undo the local commit so your branch is clean:

```bash
git reset --hard origin/main
```

### 4. Do it the right way — via a PR

```bash
git switch -c docs-update
printf "\nA proper documented change.\n" >> README.md
git commit -am "docs: add a note the proper way"
git push -u origin docs-update
gh pr create --fill
```

Open the PR in the browser (`gh pr view --web`). Observe:

- CI (`ci / test`) is required and running.
- Because the change touches an owned path, **you are auto-requested as a code owner reviewer.**
- The merge button is disabled until the check is green **and** there's an approving review.

### 5. Note the solo reality

If you're the only collaborator, GitHub won't let you approve your own PR, so you can't fully satisfy "1 approval" alone. That's expected and correct — it proves the gate is real. For the demo, note it in your write-up; on a real team a second person approves. (To finish merging solo, you could temporarily drop the review requirement, merge, then restore it — but don't do that on a real protected repo.)

## Expected result

- A direct push to `main` is refused.
- A PR shows a required status check and an auto-requested code owner.
- You understand exactly which rule blocked the direct push and which rule requested the reviewer.

## Done when…

- [ ] `main` is protected with required PR, 1 approval, code-owner review, and the `ci / test` status check, with `enforce_admins` on.
- [ ] `.github/CODEOWNERS` exists and a PR touching `/src/` or `/.github/` auto-requests the owner.
- [ ] You have captured (screenshot or copied terminal output) the **rejected direct push** and the **PR with its required checks**.
- [ ] `notes.md` explains, in your words, what each protection rule blocks and why "Include administrators" matters.

## Stretch

- Convert the branch protection into an **organization ruleset** targeting `main` across the repo, and note one thing rulesets do that classic protection can't (hint: multiple repos / branch patterns, bypass lists).
- Add a second, more specific CODEOWNERS line and confirm **last-match-wins** by changing a file it covers.

## Submission

Commit `notes.md` and your evidence to your portfolio under `c31-week-08/exercise-01/`.
