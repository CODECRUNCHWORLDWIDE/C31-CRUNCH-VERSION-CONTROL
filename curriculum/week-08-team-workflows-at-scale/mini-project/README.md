# Mini-Project (Capstone) — Design and Run a Team Workflow

> Stand up a real repository that a small team could ship from, then **operate it** through one full cycle — a reviewed, CI-gated change and an automated release — and document the whole thing as a **runbook** a new teammate could follow on day one.

**Estimated time:** 5 hours, spread across the back half of the week.

This is the capstone of C31. Everything from all eight weeks converges here: branching (W2), remotes (W3), PRs and review (W5), CI gates (W6), releases (W7), and this week's strategy, protection, ownership, and automated-release skills. You are not just configuring settings — you are making a **set of decisions** and proving they work by running the workflow for real.

You may build this **solo** (playing both author and reviewer roles, with the noted fallbacks) or, better, with **one partner** so reviews and CODEOWNERS are genuine.

---

## The deliverable

One GitHub repository — call it `c31-capstone-<yourname>` — plus a `RUNBOOK.md` inside it. The repo must actually *demonstrate* the workflow, not just describe it. By the end it contains:

- A tiny but real project in any language with at least one automated test.
- A **chosen and documented branching strategy** (with the reasoning).
- **Branch protection** on the trunk with required review + required CI check.
- A **`CODEOWNERS`** file routing review to the right owner(s).
- A **CI workflow** that runs tests on every PR and is *required* to merge.
- An **automated release** pipeline (release-please or semantic-release) with at least **two real releases** cut from Conventional Commits.
- A **`RUNBOOK.md`** — the operating manual for the whole thing.

---

## Milestones

### Milestone 1 — Decide (45 min)

Before touching a setting, write the top of `RUNBOOK.md`:

- **Team context:** invent a plausible small team (size, product, release cadence, rollback cost). One paragraph.
- **Branching strategy:** pick one (Lecture 1) and justify it in trade-off terms for *your* team.
- **Protection + review policy:** how many approvals, code-owner required, which checks gate merges (Lecture 2).
- **Release model:** which tool and why; automatic vs. Release-PR (Lecture 3).

Decisions first, config second. The reasoning is graded, not just the result.

### Milestone 2 — Build the repo (60 min)

```bash
gh repo create c31-capstone-<yourname> --public --clone
cd c31-capstone-<yourname>
```

- Add the project + a test (e.g., a small script with one unit test).
- Add `.github/workflows/ci.yml` that runs the test on `push` and `pull_request`.
- Add `.github/CODEOWNERS`.
- Commit with Conventional Commit messages from the very first commit.

### Milestone 3 — Protect the trunk (30 min)

Apply branch protection to your trunk (script it with `gh api`, per Exercise 1) matching the policy you wrote in Milestone 1: required PR, required approvals, code-owner review, required `ci` status check, strict/up-to-date branches, `enforce_admins` on. Record the exact command in the runbook.

### Milestone 4 — Run one full change (60 min)

Operate the workflow the way a teammate would:

1. Branch off the trunk.
2. Make a small change (a `feat:`), push, open a PR.
3. Show CI running and required; show the code owner requested.
4. Get the approval (partner, or note the solo fallback).
5. Merge behind the green gate.

Capture evidence (screenshots or terminal logs) — this proves the gate is real.

### Milestone 5 — Automate a release (45 min)

Add the release workflow (Exercise 3). Land at least one `feat:` and one `fix:`, then cut **two releases**, at least one of which is a MINOR or MAJOR driven by commit type. Confirm tags, changelog, and GitHub Releases exist.

### Milestone 6 — Write the runbook (60 min)

Finish `RUNBOOK.md` so a brand-new teammate could operate the repo without asking you anything. See the required sections below.

---

## `RUNBOOK.md` required sections

1. **Team & context** — who this workflow is for.
2. **Branching model** — the chosen strategy, how a normal change flows, branch-naming, how long branches live.
3. **Protection rules** — every rule on the trunk and *what each blocks*, plus the `gh api` command to reproduce it.
4. **Ownership & review** — the CODEOWNERS map, how many approvals, and the escalation path.
5. **CI gates** — what runs, what's required, how to read a failure.
6. **Release process** — how a version is decided (Conventional Commits → SemVer), how a release is cut, and where the changelog lives.
7. **Runbook procedures** — step-by-step for: "ship a normal change," "ship an urgent fix," and "cut a release."
8. **Decisions & trade-offs** — what you chose *not* to do and why (e.g., "we don't require 2 approvals because the team is 4 people").

---

## Acceptance criteria

- [ ] The trunk is protected: direct pushes fail; merges require an approval + a green `ci` check; `enforce_admins` is on.
- [ ] `.github/CODEOWNERS` auto-requests the owner on a PR that touches an owned path.
- [ ] CI runs on every PR and is a **required** status check.
- [ ] At least **two releases** exist, tagged and with generated changelog notes, versioned from commit types.
- [ ] `RUNBOOK.md` has all eight sections and is followable by someone who has never seen the repo.
- [ ] Evidence of one full change flowing through the gate is committed.

---

## Rubric

| Criterion | Weight | "Great" looks like |
|-----------|------:|--------------------|
| Decisions & reasoning | 25% | Strategy, protection, and release choices are justified in trade-off terms specific to the invented team |
| Protection & ownership | 20% | Trunk is genuinely un-break-able; CODEOWNERS routes review correctly; admins included |
| CI gate | 15% | A red check demonstrably blocks merge |
| Automated release | 20% | Two releases, versions computed from commit types, changelog generated — no hand-picked numbers |
| Runbook quality | 20% | A newcomer could operate the repo end-to-end from the doc alone |

---

## Why this matters

This runbook is the single most portable artifact of the entire course. Every other Code Crunch track that involves a team — C15 DevOps, C6 Security, any group project — assumes you can stand up exactly this: a protected, reviewed, CI-gated, releasable repository, documented well enough to hand off. You've now done it end to end. Put the repo link on your résumé.

---

When done: push, then share your `RUNBOOK.md` for peer review. That's the capstone of C31 · Crunch Version Control.
