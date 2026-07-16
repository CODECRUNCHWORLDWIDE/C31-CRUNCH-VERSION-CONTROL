# Week 5 — Resources

Free, public, no signup unless noted. Read the "Required" set; graze the rest.

## Required reading

- **GitHub Flow (official):** the branch → PR → review → merge loop, from the source.
  <https://docs.github.com/en/get-started/using-github/github-flow>
  *Why: the canonical description of the exact workflow this week teaches.*
- **About pull requests (GitHub Docs):** what a PR is, base vs. head, draft PRs, merge methods.
  <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests>
  *Why: the reference for every PR field and behavior you touch this week.*
- **Google Engineering Practices — Code Review Developer Guide:** the best free writing on *how* to review and *how* to author reviewable changes.
  <https://google.github.io/eng-practices/review/>
  *Why: read "The Standard of Code Review," "Small CLs," and "How to write code review comments." It's short and it's the industry reference.*

## Reviewing well

- **Google — "How to do a code review" (reviewer's guide):** what to look for and in what order.
  <https://google.github.io/eng-practices/review/reviewer/>
  *Why: turns "review the PR" into a concrete checklist.*
- **Conventional Comments:** the `nit:` / `suggestion:` / `question:` / `blocking:` vocabulary that makes review comments unambiguous.
  <https://conventionalcomments.org/>
  *Why: a five-minute read that instantly upgrades your comment clarity.*
- **Reviewing proposed changes in a pull request (GitHub Docs):** line comments, batching, the three verdicts.
  <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/reviewing-proposed-changes-in-a-pull-request>
  *Why: the exact buttons for Exercise 2.*

## Suggestions & responding to feedback

- **Incorporating feedback in your pull request (GitHub Docs):** applying and batching suggested changes.
  <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/incorporating-feedback-in-your-pull-request>
  *Why: the author side of Exercise 3 — the one-click apply loop.*
- **Commenting on a pull request (suggestions syntax):** how to write a ` ```suggestion ` block.
  <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/commenting-on-a-pull-request>
  *Why: the exact fenced-block syntax for one-click suggestions.*

## Linking issues, templates, protection

- **Linking a pull request to an issue (closing keywords):** `Closes` / `Fixes` / `Resolves`.
  <https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue>
  *Why: makes your issue tracker close itself on merge.*
- **Creating a pull request template:** `.github/pull_request_template.md`.
  <https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository>
  *Why: raises the quality floor of every future PR in the repo.*
- **About protected branches:** enforcing "PRs only" and "review required" on `main`.
  <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches>
  *Why: turns GitHub flow from a request into a rule (mini-project).*
- **About merge methods on GitHub (squash / merge commit / rebase):**
  <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/about-merge-methods-on-github>
  *Why: pick the right merge strategy on purpose, not by habit.*

## The GitHub CLI

- **`gh pr` manual:** `create`, `view`, `review`, `merge`, `ready`, `checks`.
  <https://cli.github.com/manual/gh_pr>
  *Why: the fast path for every PR action this week; skim `gh pr create` and `gh pr merge`.*

## Deeper / opinion pieces (optional)

- **"Ship / Show / Ask" — Martin Fowler / Rouan Wilsenach:** a nuanced take on when a change even *needs* review.
  <https://martinfowler.com/articles/ship-show-ask.html>
  *Why: challenges the "everything needs review" default with judgment.*
- **SmartBear — "Best Practices for Code Review":** summarizes the Cisco study on review size vs. defect detection.
  <https://smartbear.com/learn/code-review/best-practices-for-peer-code-review/>
  *Why: the data behind "small PRs catch more bugs."*
- **"How to Make Your Code Reviewer Fall in Love with You" — Michael Lynch:**
  <https://mtlynch.io/code-review-love/>
  *Why: a warm, practical guide to being the author reviewers enjoy working with.*

## Glossary

| Term | Definition |
|------|------------|
| **Pull request (PR)** | A request to merge one branch (`head`) into another (`base`), wrapped in a discussion + review page. GitLab calls it a "merge request." |
| **GitHub flow** | Lightweight workflow: branch off `main`, commit, PR, review, merge. `main` stays always-deployable. |
| **base / head** | The branch a PR merges *into* / merges *from*. The diff is `base...head`. |
| **Draft PR** | A PR explicitly not ready to merge; doesn't request review by default. Flip to "Ready for review" when done. |
| **Suggestion** | A review comment containing a ` ```suggestion ` block the author can apply as a commit in one click. |
| **Request changes** | A review verdict that blocks merge (under branch protection) until resolved. Reserve for real problems. |
| **nit** | Prefix marking a comment as minor and non-blocking — the author's call. |
| **Closing keyword** | `Closes` / `Fixes` / `Resolves` + `#N` in a PR body; auto-closes issue N on merge. |
| **Branch protection** | Rules on a branch (e.g. `main`): require PRs, require approvals, require status checks. |
| **Squash merge** | Collapses a PR's commits into one commit on the base branch. |
| **Stacked PRs** | A chain of small PRs, each based on the previous, for shipping a big change reviewably. |

---

*Broken link? Open an issue.*
