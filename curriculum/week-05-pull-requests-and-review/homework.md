# Week 5 — Homework

Five problems, ~5 hours total. Commit each to your portfolio. These reinforce the lectures and exercises without repeating the mini-project.

---

## Problem 1 — Rewrite three bad PR titles and descriptions (45 min)

Below are three real-world-ugly PRs. For each, write a better **title** (imperative, ≤70 chars) and a full **description** (what / why / how to test), inventing plausible details.

1. Title: `fix` — Diff: adds a `null` check in a payment handler.
2. Title: `stuff` — Diff: renames `getData` to `fetchUserProfile` across 6 files.
3. Title: `WIP do not merge` — Diff: half-built search feature, author wants early feedback.

**Acceptance:**

- `titles.md` with all three rewritten titles + descriptions.
- For #3, your answer notes that this should be a **draft PR** and explains why.
- One sentence per PR on whether it should use a `Closes #` keyword and why.

---

## Problem 2 — Author a PR template and open a PR that uses it (45 min)

In any repo of yours:

1. Add `.github/pull_request_template.md` with sections for What, Why (with `Closes #`), How to test, and a checklist.
2. Open a small real PR (fix a typo, tweak a `README`) and confirm the template pre-fills.

**Acceptance:**

- The template file, committed.
- A screenshot or link showing the new PR pre-filled by the template.
- Two sentences on what you put in the checklist and why those items.

---

## Problem 3 — Review a public open-source PR (in writing) (60 min)

Find a **small, recent PR** on a public GitHub repo (search `is:pr is:open` on a project you use). Read it as if you were the reviewer. Do **not** post your comments to the real PR — write them up privately.

**Acceptance:**

- `review-writeup.md` containing: the PR link, the order in which you read the diff, at least three comments you'd leave (each tagged *correctness / design / test / readability / nit*), and the verdict you'd give (Comment / Approve / Request changes) with one sentence of justification.
- One comment must be phrased as a **suggestion** (write the ` ```suggestion ` block).
- A note on one thing the author did *well* — real praise you'd leave.

---

## Problem 4 — The small-PR argument, in your words (45 min)

Your teammate says: *"Splitting my feature into five PRs is just busywork — it's the same code either way, and now I have five things to babysit instead of one."*

Write a **250–350 word reply** that persuades them, using concrete reasons, not dogma.

**Acceptance:**

- `small-prs.md` with your reply.
- It must name at least **four distinct** benefits (review speed, defect detection, conflict surface, revertability, reviewer kindness — pick four+).
- It must fairly acknowledge the *one* real cost of stacking (overhead / retargeting bases) and answer it.

---

## Problem 5 — Merge-strategy decision table (45 min)

For each situation, state which merge strategy you'd use (**squash**, **merge commit**, or **rebase**) and why in one sentence:

1. A 30-line bug fix made of 6 messy "wip" commits.
2. A feature where each of 4 commits is a clean, meaningful step you want preserved on `main`.
3. A tiny one-commit docs fix.
4. A repo whose team wants a strictly linear history with no merge commits.
5. A PR whose commit history you *don't* trust to be clean and want collapsed.

**Acceptance:**

- `merge-strategies.md` with all five answers.
- A closing paragraph: what's your **default** strategy and why, and when do you deviate?

---

## Time budget

| Problem | Time |
|--------:|----:|
| 1 | 45 min |
| 2 | 45 min |
| 3 | 1 h |
| 4 | 45 min |
| 5 | 45 min |
| **Total** | **~5 h** |

After homework, take the [quiz](./quiz.md), then ship the [mini-project](./mini-project/README.md).
