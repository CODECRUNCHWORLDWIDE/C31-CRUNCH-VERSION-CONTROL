# Lecture 1 — GitHub Flow, and Why Small PRs Win

> **Duration:** ~2 hours. **Outcome:** You can run the full branch → commit → PR → review → merge loop from memory, and you can defend "keep PRs small" with concrete reasons instead of vibes.

## 1. The problem GitHub flow solves

You can already commit. So why not just commit to `main` and push? On a solo toy project you can, and for a while it's fine. The moment a *second person* shows up, three problems appear at once:

1. **`main` is shared and should always work.** If your half-finished change lands on `main`, everyone who pulls gets your mess. `main` is the branch you deploy from, demo from, and cut releases from. It has to stay green.
2. **Nobody looked at the change before it shipped.** Bugs that a second pair of eyes would catch in thirty seconds now ship to users.
3. **There's no record of *why*.** Six months later someone asks "why is this weird?" and the only answer is a one-line commit message.

**GitHub flow** is a lightweight branching workflow that fixes all three. It's the default for most teams on GitHub because it's simple enough to hold in your head:

> Anything on `main` is deployable. To change anything, branch off `main`, commit, open a pull request, get it reviewed, and merge back. Repeat.

That's the whole model. No `develop` branch, no `release/*` branches, no `git flow` ceremony. (Those exist — you'll meet them in Week 8 — but they solve problems a small team usually doesn't have yet.)

## 2. The loop, step by step

Here's one full trip around GitHub flow, in commands. Assume you've cloned a repo and you're on an up-to-date `main`.

```bash
# 1. Start from a fresh main
git switch main
git pull                                   # get everyone else's merged work

# 2. Branch. Name it for the change, not for you.
git switch -c add-csv-export

# 3. Do the work. Commit in small, sensible chunks.
#    ... edit files ...
git add -p                                 # stage hunks deliberately
git commit -m "Add CSV serializer for reports"
#    ... more work ...
git commit -m "Wire CSV export into the /reports endpoint"

# 4. Push the branch to the remote and set upstream
git push -u origin add-csv-export

# 5. Open the pull request
gh pr create --fill                        # or use the URL GitHub prints on push

# 6. ... review happens (Lectures 2 and 3) ...

# 7. Merge once approved and green
gh pr merge --squash --delete-branch

# 8. Return home and sync
git switch main
git pull
```

Steps 1–4 you already know from Weeks 1–3. Steps 5–7 are new, and they're where the *team* enters the picture. A **pull request** (PR) is a request to merge your branch into another branch, wrapped in a page where people can discuss and review it line by line before it happens.

> **Terminology.** GitHub calls it a "pull request." GitLab and others call the same thing a "merge request" (MR). Same idea: *please pull my branch in.* We'll say PR throughout.

## 3. What actually happens on `gh pr create`

A PR is not a Git concept — Git knows nothing about it. It's a GitHub object that *points at* two Git refs:

| Field | Meaning |
|-------|---------|
| **base** | The branch you want to merge *into* (usually `main`). |
| **head** | The branch you want to merge *from* (your `add-csv-export`). |
| **diff** | Computed live: `git diff base...head` — everything on your branch that isn't on base. |
| **commits** | The commits unique to `head`. |
| **conversation** | Comments, reviews, status checks, labels — all the social metadata. |

Because the diff is computed *live* against the base branch, every time you push another commit to `head`, the PR updates automatically. You never "re-submit" a PR. You just push, and the PR reflects the new state.

The `base...head` (three-dot) range matters: it's "commits reachable from `head` but not from `base`." That's exactly the set of changes a reviewer needs to look at.

## 4. Why small PRs win

Here is the single most important habit in this entire course. **Keep pull requests small.** Not because a style guide says so — because small PRs are faster, safer, and kinder. Let's make that concrete.

### 4.1 Small PRs get reviewed faster

A reviewer will drop what they're doing to review a 20-line PR. They will *avoid* a 900-line PR for two days because it needs a quiet hour they don't have. So counterintuitively, **splitting one big PR into five small ones usually merges faster overall**, because each small one clears in minutes rather than the big one blocking for days.

### 4.2 Small PRs find more bugs

Reviewer attention is a fixed budget. Research on code review (notably the classic Cisco/SmartBear study of ~2,500 reviews) found defect-detection density collapses once a review exceeds a few hundred lines of code — reviewers skim, and skimming misses bugs. Below is the rough shape of what large-scale review data consistently shows:

| Change size | What reviewers actually do | Bugs caught |
|-------------|----------------------------|-------------|
| < 50 lines | Read every line carefully | High density |
| 50–200 lines | Read carefully, some fatigue | Good |
| 200–400 lines | Start skimming | Falling |
| 400+ lines | "LGTM" without real reading | Low — rubber stamp |

A 900-line PR doesn't get 900 lines of scrutiny. It gets a tired "looks fine to me." You lose the entire point of review.

### 4.3 Small PRs have less conflict surface

The longer a branch lives and the more it touches, the more likely `main` moves underneath it and you get merge conflicts. Small PRs merge in hours, so `main` barely moves. Big long-lived branches turn into conflict-resolution archaeology.

### 4.4 Small PRs are easier to revert

If a 30-line PR breaks production, `git revert` of one merge undoes exactly that change. If a 900-line PR that did nine things breaks production, reverting it also reverts the eight things that were *fine*. Small PRs give you a precise undo button.

### 4.5 Small PRs are kinder

A big PR asks a colleague for a large, unpredictable chunk of their day. A small PR asks for ten minutes. Respecting reviewer time is a real professional skill, and it compounds: people review your small PRs quickly *because* you review theirs quickly.

### How small is small?

There's no magic number, but useful anchors: **under ~200–400 lines of diff**, **one logical change per PR**, and **reviewable in one sitting**. If you can't describe the PR in one sentence without the word "and," it's probably two PRs.

## 5. "But my change is genuinely big"

Sometimes it is. A new feature legitimately needs a data model, an API endpoint, and a UI. The answer is not one giant PR — it's a **stack** of small PRs, each building on the last:

```
PR #1: add the CSV serializer (pure function, easy to review, ships behind nothing)
PR #2: add the /reports/export endpoint that uses it
PR #3: add the "Export" button in the UI that calls the endpoint
```

Each PR is independently reviewable and independently correct. PR #1 can merge even if #2 and #3 aren't done. This is the skill you'll practice in [Challenge 1](../challenges/challenge-01-split-a-giant-pr.md). A common enabling trick is a **feature flag**: merge the code dark (disabled), then flip it on in a tiny final PR once the pieces are all in.

## 6. Committing to `main` — when is it OK?

Rarely, and only with guardrails. Some very high-trust, high-discipline teams practice **trunk-based development** where developers commit tiny changes directly to `main` many times a day. That works *only* with strong automated tests, pair programming, and feature flags — the safety net that review otherwise provides. Without that net, direct-to-`main` is how you ship a broken build to everyone. For this course, and for almost every team you'll join, **the PR is the unit of change.** You'll design a trunk-based workflow deliberately in Week 8, once you understand what it's trading away.

## 7. Protecting `main`

The workflow is only as good as its enforcement. GitHub lets you add a **branch protection rule** (or the newer **rulesets**) on `main` so that:

- Direct pushes to `main` are rejected — changes *must* go through a PR.
- A PR needs at least one approving review before it can merge.
- Required status checks (tests, lint) must pass — the subject of Week 6.

You'll turn these on in the mini-project. For now, know that the honor-system version of GitHub flow ("please use PRs") becomes the enforced version ("you literally cannot push to `main`") with a few clicks in **Settings → Branches**.

## 8. A mental model to carry

Think of `main` as the published edition of a book, and each PR as a proposed edit sitting with an editor. The edit is discussed, marked up, revised, and only merged into the published edition when it's ready. `main` is never mid-sentence. Every change has a paper trail — who proposed it, who reviewed it, what was discussed, when it merged.

That paper trail is the quiet superpower of GitHub flow. A year from now, "why is this code like this?" has an answer: click "blame," find the commit, click the PR, read the discussion. The reasoning is preserved next to the code.

## 9. Check yourself

- What are the two Git refs a pull request points at, and what does "base...head" mean?
- Why does splitting one 900-line PR into five small ones often merge *faster* overall?
- Give three distinct reasons small PRs catch more bugs / cause less pain than large ones.
- Your feature needs a model, an endpoint, and a button. How do you ship it in small PRs?
- What has to be true about a team's tests and culture before direct-to-`main` is safe?
- What does a branch protection rule on `main` enforce?

If you can answer all six, move to Lecture 2 and learn to write a PR worth reviewing.

## Further reading

- **GitHub Flow (official guide):** <https://docs.github.com/en/get-started/using-github/github-flow>
- **"Small CLs" — Google Engineering Practices, Code Review Developer Guide:** <https://google.github.io/eng-practices/review/developer/small-cls.html>
- **"Ship / Show / Ask" — a modern branching strategy (Martin Fowler):** <https://martinfowler.com/articles/ship-show-ask.html>
