# Challenge 1 — Broken Code Cannot Merge

**Type:** Open-ended. No single right answer.
**Estimated time:** ~1 hour.

## Scenario

You've been handed a repo that a small team ships from. The team keeps merging changes that break `main` — someone pushes, the tests were red, and now nobody can build. Your job is to make that *impossible*: configure the repo so that **code failing the test suite cannot be merged into `main`, by anyone, ever** — and then act as an attacker trying to defeat your own configuration.

The point of this challenge is not to write a fancy workflow. It's to think adversarially about **all the ways broken code sneaks onto a trunk** and close each one.

## Your task

Starting from your `cc-ci-week6` repo (or a fresh one with a test suite):

1. Build the CI workflow that runs the tests on push and PR.
2. Configure branch protection so the check is **required**.
3. Then systematically try to get broken code onto `main` anyway, and block every route you find.

## Constraints

- `main` must be **impossible** to break through the *normal* merge path.
- The protection must apply to **everyone, including repository admins** — this is an explicit requirement, not an option.
- No direct pushes to `main`; everything goes through a PR.
- The configuration must be reproducible from the repo (export the ruleset JSON with `gh api .../rulesets/<id> > ruleset.json`, or document the exact settings).

## The red-team list (attack your own repo)

For each route below, verify it's blocked. If it isn't, fix your configuration. Record the outcome in `NOTES.md`.

| Attack | Can broken code get in? How do you block it? |
|--------|----------------------------------------------|
| Open a PR with a failing test and click Merge | Required status check should disable the button |
| Push directly to `main`, bypassing PRs | "Require a pull request before merging" |
| Merge as a **repo admin** with the override option | "Do not allow bypassing" / "Include administrators" |
| A PR that passes against an **old** `main`, but breaks once merged | "Require branches to be up to date" (semantic-conflict trap) |
| Delete or edit the workflow file in the same PR to disable CI | Think: does your required check still report? What stops this? |
| A PR whose `paths` filter skips CI so the check never runs | The "check never ran" deadlock — is your required workflow unfiltered? |
| Force-push over `main`'s history | "Restrict force pushes" (usually on by default in protection) |

That fifth row is the interesting one — chew on it. If someone's PR *deletes* the workflow, the required check may simply not run, and depending on your setup the PR could slip through as "no checks to satisfy." Figure out what actually happens and how to defend it (hint: protecting the workflow path, requiring the check by a name a malicious PR can't remove, CODEOWNERS on `.github/`).

## Hints

<details>
<summary>Where to focus</summary>

- The single most-forgotten setting is **applying rules to administrators**. Without it, the whole thing is theatre for anyone with admin.
- "Require branches to be up to date" is what turns "the check passed" into "the check passed *against what will actually be main*."
- Test the workflow-deletion attack for real. The behaviour is subtle and worth seeing rather than guessing.
- CODEOWNERS (Week 5) can force review of any change to `.github/workflows/`, so CI can't be silently gutted.
</details>

## How success is judged

- **Coverage (40%):** Every row in the red-team table is either blocked or has a documented, defensible reason it's acceptable.
- **Admin-proof (20%):** You demonstrably cannot merge a red PR even as the repo owner.
- **Reproducibility (20%):** Someone could recreate your protection from files in the repo.
- **Adversarial write-up (20%):** `NOTES.md` shows you genuinely *tried to break in* and reported what happened — including anything you couldn't fully close.

## Deliverable

A repo where you can hand someone admin, dare them to merge a failing PR, and they can't — plus `NOTES.md` documenting each attack and its defense.
