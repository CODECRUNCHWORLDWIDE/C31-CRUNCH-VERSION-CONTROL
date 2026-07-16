# Mini-Project — Ship a Small Feature via a Reviewed Pull Request

> Take a real, small feature from an issue to merged `main` — entirely through GitHub flow. One protected `main`, one topic branch, one well-formed PR, one honest review round, one clean merge. This is the week made real.

**Estimated time:** 6–9 hours, spread across Friday and Saturday.

This project produces a single artifact you'll be proud to link on a résumé: **a small GitHub repository whose `main` is protected, with at least one feature that reached `main` only through a reviewed pull request** — issue linked, review threads resolved, and a merge you can explain. The code is deliberately tiny. The *process* is the deliverable.

---

## The build

Build a small **command-line "notes" tool** (or anything of similar size — a URL shortener, a tip calculator, a Markdown word-counter; pick what you like). It only needs to do a little:

- `notes add "buy milk"` — append a note to a file.
- `notes list` — print all notes, numbered.
- `notes done 2` — mark note 2 done.

Any language you're comfortable in. The feature set is small on purpose so that **every ounce of your effort goes into the workflow**, not the code.

---

## Requirements

Your repository must demonstrate, with evidence a reviewer can click through:

1. **A protected `main`.** Branch protection (or a ruleset) requiring: changes go through a PR, and at least one approving review before merge. If you're solo, still turn it on and note that you'd need a teammate's approval on a real team; use a second account or document the limitation.
2. **At least two features, each shipped via its own PR.** No commits directly to `main`. Every change is a topic branch → PR → review → merge.
3. **Small, focused PRs.** Each PR does one logical thing and is reviewable in one sitting. If a feature is genuinely big, ship it as a *stack* (Challenge 1 skills).
4. **Great PR hygiene** on every PR: imperative title, a what/why/how-to-test description, a **linked issue** with a closing keyword, and a checklist.
5. **A real review round** on at least one PR: line comments, at least one **suggestion**, a chosen verdict, then the author addressing feedback (applied suggestion + a pushed fix), threads resolved with replies, and merge.
6. **A `.github/pull_request_template.md`** so future PRs start with the right skeleton.
7. **A repo `README.md`** documenting what the tool does and how to run it.

---

## Milestones

### Milestone 1 — Repo + protection (1h)
- Create the repo, add a `README.md` and a `.github/pull_request_template.md`.
- Commit the initial skeleton to `main` (this bootstrap commit is the one exception; everything after goes through PRs).
- Turn on branch protection for `main`: **Settings → Branches → Add branch protection rule** (or **Rulesets**), require a pull request + 1 approval. Try to `git push` to `main` and confirm it's rejected.

### Milestone 2 — Feature 1 via PR (2h)
- Open an issue: "Add `notes add`."
- Branch, build, commit small, push, open a PR that `Closes` the issue.
- Get a review (partner, second account, or self-comments). Address at least one comment. Merge with a deliberate strategy. Confirm the issue auto-closed.

### Milestone 3 — Feature 2 via a reviewed round (3h)
- Open an issue: "Add `notes list` and `notes done`."
- Same loop, but this time make the review *substantive*: the reviewer leaves a `suggestion` and a design comment; you apply the suggestion, push a hand-written fix for the design comment, reply to every thread, re-request review, get approval, merge.

### Milestone 4 — Polish + write-up (1–2h)
- Ensure `main` is green and the `README` documents the finished tool.
- Write `WORKFLOW.md`: a narrated tour of one PR (link it) explaining each step — why it was small, what the review caught, how you responded, why you chose that merge strategy.

---

## Acceptance criteria

- [ ] `main` is protected; a direct push to `main` is rejected (documented with the error).
- [ ] Every feature reached `main` through a PR — `git log main` shows merge/squash commits, no ad-hoc direct commits after bootstrap.
- [ ] At least two PRs, each small and single-purpose, each linked to an issue that auto-closed.
- [ ] At least one PR has a real review: line comments + a suggestion + a verdict, then addressed feedback and resolved threads.
- [ ] `.github/pull_request_template.md` exists and shaped the PR descriptions.
- [ ] `README.md` explains the tool; `WORKFLOW.md` narrates one PR end to end.

---

## Rubric

| Criterion | Weight | "Great" looks like |
|-----------|------:|--------------------|
| GitHub flow discipline | 25% | Nothing hit `main` except through a reviewed PR; protection actually enforced |
| PR quality | 25% | Small, single-purpose PRs; titles/descriptions/links/checklists all present |
| Review depth | 20% | A substantive review round: comments that mattered, a suggestion, honest verdict |
| Responding to feedback | 15% | Applied a suggestion, pushed a fix, replied to every thread — including disagreements |
| Documentation | 15% | `README` + `WORKFLOW.md` let a stranger understand the tool *and* your process |

---

## Why this matters

This is the exact loop you'll run every working day for the rest of your career: branch, PR, review, merge. Employers can't see your `git` knowledge directly — but they can open your repos and see whether your `main` is protected, whether your PRs are small and well-described, and whether your review threads read like a professional's. This mini-project is a portfolio piece that answers "can this person work on a team?" with a clickable yes.

It also sets up **Week 6**: right now your protection requires a *human* approval. Next week you'll add **required status checks** so that broken code can't merge even *with* an approval — the CI gate that turns "please don't break `main`" into "you literally can't."

---

When done: push, confirm the protection holds, and start [Week 6 — CI with GitHub Actions](../../week-06-ci-with-github-actions/).
