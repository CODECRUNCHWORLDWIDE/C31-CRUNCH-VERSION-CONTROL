# Challenge 2 — Contribute a Change Through a Fork

**Time:** ~75 minutes. **Difficulty:** Medium.

## The scenario

You've spotted something you'd improve in an open-source project you don't own — a typo, a broken link, an unclear sentence, a missing example. Take it all the way: fork, branch, change, push, and open a **real pull request** against an upstream repository. No single "correct" change — the skill being judged is executing the full contribution loop cleanly and safely.

This is how open source actually works. Every merged PR you've ever benefited from went through these exact steps.

## Where to contribute (pick one)

You need a repo that *welcomes* practice contributions, so you don't waste a maintainer's time. Good options:

- **`firstcontributions/first-contributions`** — a repo built specifically to teach the PR flow; adding your name to a list is the intended change. <https://github.com/firstcontributions/first-contributions>
- **A "good first issue"** on a project you use — filter issues by the `good first issue` label. Fix something small and real.
- **A typo or broken link** in any project's docs — small, genuinely useful, low-risk.

If you're unsure, use `first-contributions`; it exists for exactly this and your PR is expected.

## What to do

The loop from Lecture 3, executed for real:

1. **Fork and clone**, wiring up `upstream`:
   ```bash
   gh repo fork OWNER/REPO --clone
   cd REPO
   git remote -v      # confirm origin (yours) + upstream (theirs)
   ```

2. **Sync before you start** so your branch is based on the latest code:
   ```bash
   git fetch upstream
   git switch main
   git merge upstream/main
   ```

3. **Branch** — never work on `main`:
   ```bash
   git switch -c my-contribution
   ```

4. **Make a focused change.** One logical change per PR. Keep it small and self-contained. Follow the project's `CONTRIBUTING.md` if it has one (read it — it's part of the challenge).

5. **Commit with a clear message** explaining *what* and *why*:
   ```bash
   git commit -am "Docs: fix broken link to installation guide"
   ```

6. **Push to your fork** and open the PR:
   ```bash
   git push -u origin my-contribution
   gh pr create --fill          # or use the "Compare & pull request" button on GitHub
   ```

7. **Fill in the PR description** properly: what changed, why, and how to verify. If the project has a PR template, complete it.

## Constraints

- The change must be **genuine and small** — a real improvement, not noise. No "test" commits, no whitespace-only churn on unrelated files.
- Your branch must be **based on current `upstream`** (step 2) so the PR shows *only your change*, not a pile of unrelated commits.
- Do all work on a **topic branch**, never on `main`.
- Respect the project: read its `CONTRIBUTING.md` and any PR template. A contribution that ignores the project's rules is not a successful contribution, technically correct or not.
- If the project is `first-contributions` or similar practice repo, opening the PR is success — you don't need it merged.

## Hints

<details>
<summary>My PR shows dozens of commits that aren't mine</summary>

Your branch was cut from a stale `main`. Fix it: `git fetch upstream`, then `git rebase upstream/main` on your branch. The PR will collapse to just your commit(s) once your branch descends cleanly from current upstream.
</details>

<details>
<summary>How small is "small"?</summary>

A reviewer should be able to understand your entire change in under two minutes. One typo, one link, one clarified paragraph, one added example. If you're touching five files for unrelated reasons, that's five PRs.
</details>

<details>
<summary>What if I want to update the PR after opening it?</summary>

Just commit more to the same branch and `git push`. The PR updates automatically — a PR tracks a branch, not a snapshot. This is also how you respond to review feedback.
</details>

## How success is judged

Write `writeup.md` with:

- [ ] The URL of the PR you opened (or a screenshot if it's a private practice setup).
- [ ] `git remote -v` output showing your `origin`/`upstream` split.
- [ ] The `git log --oneline` of your branch showing it contains **only** your commit(s) on top of upstream — no unrelated history.
- [ ] Two or three sentences: what you changed, why it's an improvement, and how a maintainer would verify it.
- [ ] One sentence on what you'd do to keep this PR current if `upstream` moved forward before it's merged.

## Submission

Commit `writeup.md` (with the PR link) to your portfolio under `c31-week-03/challenge-02/`.
