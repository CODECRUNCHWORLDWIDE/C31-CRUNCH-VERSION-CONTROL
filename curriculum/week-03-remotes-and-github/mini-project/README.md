# Mini-Project — Fork, Contribute, and Sync

> Run the full open-source contribution loop end to end: fork a real repository, make a meaningful change on a branch, open a pull request, and — the part everyone skips — keep your fork in sync with upstream as the original project moves on. This is the deliverable that proves you can collaborate on GitHub, not just push to your own repos.

**Estimated time:** 8 hours, spread across Saturday and Sunday.

This mini-project ties together everything in Week 3: remotes (Lecture 1), pushing and GitHub auth (Lecture 2), and the fork model (Lecture 3). The exercises drilled each piece in isolation; here you do the whole thing on a real project, and you produce a written record that a reviewer could follow to reproduce your work.

---

## Goal

Take one genuine change from idea to open pull request on an upstream repository you don't own, then demonstrate you can keep your fork current. You'll walk away with a real PR link and a repeatable mental model of the contribution loop.

## Deliverable

A directory in your portfolio repo `c31-week-03/mini-project/` containing:

1. `pr-link.md` — the URL of the pull request you opened, plus a one-paragraph summary of the change.
2. `remotes.md` — the output of `git remote -v` and `git branch -vv`, annotated: which remote is which, what tracks what.
3. `sync-log.md` — a transcript of you syncing your fork with upstream (the commands and their output), with a sentence explaining each step.
4. `writeup.md` — the narrative: what you contributed, why, the problems you hit, and how you'd stay in sync long-term.

---

## Requirements

- **A real fork** of a real public repository, with both `origin` (your fork) and `upstream` (the original) configured.
- **A focused change on a topic branch** — never committed to `main`. One logical change.
- **An open pull request** against the upstream repo (a practice repo like `firstcontributions/first-contributions` is fine; the PR being open is what matters).
- **A demonstrated sync**: fetch from upstream, fast-forward your `main`, and push it to your fork — captured in `sync-log.md`.
- **Clean history**: your PR branch shows only your commit(s) on top of current upstream, no unrelated churn.

---

## Suggested workflow

### Milestone 1 — Set up the fork (1h)

Pick a repository (see Challenge 2 for good options). Fork and clone it, then wire up `upstream`:

```bash
gh repo fork OWNER/REPO --clone
cd REPO
git remote add upstream https://github.com/OWNER/REPO.git   # skip if gh already added it
git remote -v
```

Capture `git remote -v` and `git branch -vv` into `remotes.md` and annotate every line. Confirm you understand which remote you push to and which you only fetch from.

### Milestone 2 — Sync, then branch (1h)

Before writing a single change, bring your fork current so your work is based on the latest code:

```bash
git fetch upstream
git switch main
git merge upstream/main
git push origin main
git switch -c my-contribution
```

Record this in `sync-log.md`. This is the habit that prevents 90% of "my PR is a mess" problems.

### Milestone 3 — Make the change (2h)

Read the project's `README.md` and `CONTRIBUTING.md`. Make one focused, genuine improvement. Commit with a clear message:

```bash
# ...edit files...
git diff                              # review before committing
git commit -am "Clear, specific message describing the change"
git log --oneline -5
```

Keep the change small enough that a reviewer understands it in two minutes.

### Milestone 4 — Push and open the PR (1.5h)

```bash
git push -u origin my-contribution
gh pr create --fill
```

Fill in the PR description: what changed, why, how to verify. Complete any PR template. Put the resulting URL in `pr-link.md`.

### Milestone 5 — Prove you can stay in sync (1.5h)

Simulate (or wait for) upstream moving ahead, then sync again and show your PR branch can be rebased onto the new upstream if needed:

```bash
git fetch upstream
git switch main
git merge upstream/main
git push origin main
git switch my-contribution
git rebase upstream/main              # keep your PR based on latest upstream
git push --force-with-lease           # update the PR branch (safe force — Week 4 preview)
```

Append this to `sync-log.md` with an explanation of each command. (Note: `--force-with-lease` here updates *your own* PR branch after a rebase — a legitimate use, unlike Challenge 1's banned force-push over a teammate.)

### Milestone 6 — Write it up and commit (1h)

Write `writeup.md`. Re-read it out loud. Commit all four files to your portfolio and push.

---

## Acceptance criteria

- [ ] `pr-link.md` contains a working URL to a pull request you opened against an upstream repo.
- [ ] `remotes.md` shows and annotates both `origin` (your fork) and `upstream` (the original).
- [ ] Your PR branch contains only your own commit(s) on top of current upstream — verified by `git log --oneline upstream/main..my-contribution`.
- [ ] `sync-log.md` shows at least one full fetch-upstream → merge → push-origin cycle, explained step by step.
- [ ] Nothing was ever committed directly to `main`; all work is on a topic branch.
- [ ] `writeup.md` reads clearly to someone who wasn't there.

---

## Rubric

| Criterion | Weight | "Great" looks like |
|-----------|------:|--------------------|
| Correct two-remote setup | 20% | `origin`/`upstream` split is right and clearly explained |
| Clean PR | 25% | Branch shows only the intended change; message is clear; PR template completed |
| Sync competence | 25% | A real, explained sync cycle; understands fast-forward vs. rebase |
| History hygiene | 15% | Nothing on `main`; no unrelated commits in the PR |
| Writing quality | 15% | Reproducible; a reader could follow it to do the same |

---

## Why this matters

This loop — fork, branch, change, PR, sync — is the single most common collaboration pattern in software. Every open-source contribution, and most internal ones at companies using GitHub, follow it. Doing it once for real, and writing down what happened, converts it from "something I read about" into "something I've done." Week 5 (Pull Requests & Review) builds directly on this: there you'll learn to *review* PRs and run GitHub flow on a team. You can't review the process well until you've lived it once.

---

When done: push your portfolio and move to [Week 4 — Rewriting History](../../week-04-rewriting-history/), where you'll learn to clean up a messy branch *before* it becomes a PR.
