# Week 5 — Pull Requests & Review

> *A commit is you talking to your future self. A pull request is you talking to your team. This week you learn to say something worth reading — and to read what others send you with generosity and rigor.*

Welcome to **Week 5 of C31 · Crunch Version Control**. Weeks 1–4 lived on your own machine: objects, branches, remotes, and rewriting history. This week the work becomes *social*. You will run **GitHub flow** end to end — branch, commit, open a pull request, get a real review, respond to it, and merge — and you'll learn why a stack of small PRs beats one giant one almost every time.

By the end of the week you will have opened a PR, reviewed someone else's (with line comments and a suggestion they can accept in one click), pushed changes in response to feedback, and merged clean. You'll also have felt the two hard parts of review that no cheat-sheet covers: splitting work small enough to review, and steering a disagreement to a good outcome without it turning personal.

## Learning objectives

By the end of this week, you will be able to:

- **Run GitHub flow** from a clean `main`: create a topic branch, commit, push, open a PR, review, and merge — and explain why this loop scales better than committing straight to `main`.
- **Argue for small PRs** with specifics: review latency, defect-detection rates, merge-conflict surface, and reviewer cognitive load.
- **Write a PR that reviews itself**: a title in the imperative, a description that answers *what / why / how to test*, a linked issue, a checklist, and a draft PR when the work isn't ready.
- **Review a PR well**: read the diff in the right order, leave line-anchored comments, propose a one-click **suggestion**, and choose correctly between *Comment*, *Approve*, and *Request changes*.
- **Respond to review** without ego: push fixes, resolve conversations, reply to what you didn't change and why, and re-request review.
- **Split a large change** into a reviewable stack of dependent PRs.
- **Defuse a contentious review** — separate taste from correctness, escalate on facts, and get to merge.

## Prerequisites

- **C31 Weeks 1–4** completed. You can branch, merge, resolve a conflict, push/pull to a GitHub remote, and rebase without fear.
- A **GitHub account** (free) and the **GitHub CLI** (`gh`) installed and authenticated (`gh auth login`). Everything this week works from the web UI too, but `gh` makes it faster.
- A **partner or a second account** for the review exercises. No partner? A throwaway second GitHub account, or reviewing your *own* PR in a fresh browser profile, both work. The mechanics are identical.

## Topics covered

- GitHub flow vs. committing to `main` vs. long-lived release branches
- Why small PRs win — the research and the day-to-day reasons
- Anatomy of a PR: title, description, linked issues (`Closes #12`), checklists, labels, reviewers
- Draft PRs and the "open early, mark ready later" habit
- Reading a diff: the order to read files, what to look for, what to ignore
- Line comments, multi-line comments, and **suggested changes** (one-click apply)
- The three review verdicts and when each is honest
- Review etiquette — asking vs. demanding, praising, and the "nit:" convention
- Addressing feedback: fixup commits, force-push-with-lease, resolving threads, re-requesting review
- Merge strategies at the PR level: merge commit vs. squash vs. rebase

## Weekly schedule

The schedule below adds up to approximately **28 hours**. Treat it as a target, not a contract.

| Day       | Focus                                     | Lectures | Exercises | Challenges | Quiz/Read | Homework | Mini-Project | Daily Total |
|-----------|-------------------------------------------|---------:|----------:|-----------:|----------:|---------:|-------------:|------------:|
| Monday    | GitHub flow; why small PRs win            |    2h    |    1h     |     0h     |    0.5h   |   1h     |     0h       |     4.5h    |
| Tuesday   | Anatomy of a great PR; open your first    |    2h    |    1.5h   |     0h     |    0.5h   |   1h     |     0h       |     5h      |
| Wednesday | Code review: how to review well           |    2h    |    1.5h   |     0h     |    0.5h   |   1h     |     0h       |     5.5h    |
| Thursday  | Respond to feedback; the challenges       |    0h    |    1h     |     2h     |    0.5h   |   1h     |     0h       |     4.5h    |
| Friday    | Mini-project — ship a reviewed feature    |    0h    |    0h     |     0h     |    0h     |   0h     |     3h       |     3h      |
| Saturday  | Mini-project cont.; polish the review     |    0h    |    0h     |     0h     |    0h     |   0h     |     3h       |     3h      |
| Sunday    | Quiz + reflection                         |    0h    |    0h     |     0h     |    0.5h   |   1h     |     0h       |     1.5h    |
| **Total** |                                           | **6h**   | **6.5h**  | **4h**     | **3h**    | **5h**   | **9h**       | **28h**     |

## How to navigate this week

Work top to bottom. Lectures teach the *why*; exercises build the muscle; challenges stretch you; the mini-project ties it together.

| # | File | What's inside | Time |
|--:|------|---------------|-----:|
| 1 | [lecture-notes/01-github-flow.md](./lecture-notes/01-github-flow.md) | The branch→PR→review→merge loop, and why small PRs win | ~2h |
| 2 | [lecture-notes/02-anatomy-of-a-great-pr.md](./lecture-notes/02-anatomy-of-a-great-pr.md) | Title, description, linked issues, draft PRs, checklists | ~2h |
| 3 | [lecture-notes/03-code-review.md](./lecture-notes/03-code-review.md) | How to review, request changes, suggestions, and etiquette | ~2h |
| 4 | [exercises/exercise-01-open-a-pr.md](./exercises/exercise-01-open-a-pr.md) | Open a PR from a feature branch | ~35m |
| 5 | [exercises/exercise-02-review-a-pr.md](./exercises/exercise-02-review-a-pr.md) | Review a PR with line comments + a suggestion | ~40m |
| 6 | [exercises/exercise-03-address-feedback-and-merge.md](./exercises/exercise-03-address-feedback-and-merge.md) | Address review feedback and merge | ~35m |
| 7 | [challenges/challenge-01-split-a-giant-pr.md](./challenges/challenge-01-split-a-giant-pr.md) | Split one giant PR into a stack of small ones | ~2h |
| 8 | [challenges/challenge-02-navigate-a-contentious-review.md](./challenges/challenge-02-navigate-a-contentious-review.md) | Steer a heated review to a good outcome | ~1.5h |
| 9 | [mini-project/README.md](./mini-project/README.md) | Ship a small feature end-to-end via a reviewed PR | ~6–9h |
| — | [homework.md](./homework.md) | Five practice tasks | ~5h |
| — | [quiz.md](./quiz.md) | 12 self-check questions + answer key | ~30m |
| — | [resources.md](./resources.md) | Official docs + hand-picked further reading | — |

## By the end of this week you can…

- Take a change from idea to merged `main` through GitHub flow without touching `main` directly.
- Open a PR a busy reviewer can approve in ten minutes because the description did the explaining.
- Review someone else's PR with comments that make the code better and the author feel respected.
- Split a scary 900-line diff into four PRs nobody's afraid to review.
- Turn "I disagree with this whole approach" into a merged, better change instead of a stalled thread.

## Up next

[Week 6 — CI with GitHub Actions](../week-06-ci-with-github-actions/) — once your reviewed feature is merged, you'll make it so broken code *can't* merge in the first place.

---

*Part of the Code Crunch Worldwide open curriculum · GPL-3.0 · If you find errors, please open an issue or PR.*
