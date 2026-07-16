# Challenge 1 — Migrate a Chaotic Repo to Trunk-Based Development

**Time:** ~90 minutes. **Difficulty:** Hard. **No single right answer.**

## The scenario

You've inherited `legacy-shop`, a repo in a bad state:

- Nine long-lived branches: `develop`, `feature/checkout-v2` (6 weeks old, 200 commits behind `main`), `feature/search`, `bugfix/tax`, `wip-nikhil`, `old-main`, `release-2019`, `temp`, and `main`.
- Nobody is sure which branch production deploys from. Two people think it's `develop`; one insists it's `main`.
- Merges are rare, giant, and conflict-ridden. The last merge of `feature/checkout-v2` was abandoned after a 4-hour conflict fight.
- There is **no CI** and no branch protection. Anyone pushes anywhere.
- The team of 5 wants to move to **trunk-based development** (Lecture 1 §2) but cannot stop shipping during the migration.

Your job: produce a **migration plan** that gets `legacy-shop` to a healthy trunk-based state without losing work and without a "big bang" freeze.

## Constraints

- **No lost work.** Every branch that contains real work must have its work preserved (merged, cherry-picked, or explicitly archived as a tag), or be justified as safe to delete.
- **No multi-day freeze.** The team ships continuously; a plan that says "stop all work for a week" fails.
- **The 6-weeks-behind `feature/checkout-v2` is the hard part.** A single merge is a conflict bloodbath. You must propose a realistic way to land it (hint: break it up, feature-flag it, or rebase-in-slices — Lecture 1 §2).
- **End state:** one trunk (`main`), protected, with CI as a merge gate, short-lived branches only, and a documented rule for how new work flows.

## Deliverable

A `migration-plan.md` with:

1. **Current-state audit** — for each of the nine branches: what is it, does it hold unmerged work, and your disposition (merge / cherry-pick / archive-as-tag / delete). A table is ideal.
2. **Target state** — a one-paragraph description of the trunk-based workflow the team lands on, plus the exact branch-protection rules you'd set (reference Lecture 2).
3. **Sequenced steps** — an ordered, low-risk migration path. What do you do first, and why that order? Where's the point of no return?
4. **The `checkout-v2` plan** — a concrete, specific strategy for landing the 6-week branch without a 4-hour conflict fight. Show the commands or the flag approach.
5. **Rollback** — if a migration step goes wrong, how do you recover? (Tags are cheap; use them.)

## Hints

<details>
<summary>How do you land a 6-weeks-behind branch safely?</summary>

Three viable approaches, each defensible:
- **Slice it:** cherry-pick or re-implement `checkout-v2` as a series of small PRs into `main`, each merged behind a feature flag defaulting to off. The big branch becomes a reference, not a merge target.
- **Rebase in stages:** rebase `checkout-v2` onto `main` in chunks (a few commits at a time), resolving smaller conflict batches, rather than one 200-commit rebase.
- **Flag-and-forward:** merge the branch *now* behind a flag even if imperfect, then fix forward on trunk in small PRs.

Any of these can be right. Pick one and justify it.
</details>

<details>
<summary>What's the safe first step?</summary>

Before deleting anything, **tag every branch tip** (`git tag archive/feature-search feature/search`) and push the tags. Now every branch is recoverable forever, and deleting branches is no longer scary. Cheap insurance; do it first.
</details>

<details>
<summary>How do you protect main without blocking the team mid-migration?</summary>

Turn protection on in stages: first require PRs (stops the worst chaos), then add CI once a workflow exists, then require reviews. Each step is reversible and doesn't require the whole team to change everything at once.
</details>

## How success is judged

- **No work is silently lost** — every branch has an explicit, justified disposition, and archival tags exist before any deletion.
- **The plan is executable** — a real team could follow the ordered steps; it's not "and then adopt trunk-based development" hand-waving.
- **The `checkout-v2` strategy is concrete** — actual commands or a specific flag plan, not "merge it carefully."
- **It respects the no-freeze constraint** — work continues throughout.
- **Trade-offs are named** — you say what your chosen approach costs (e.g., "flag-and-forward ships imperfect code we must clean up on trunk").

## Submission

Commit `migration-plan.md` to your portfolio under `c31-week-08/challenge-01/`. Bonus: actually build a small chaotic repo with a few branches and *run* your plan, committing the command log as `migration-log.md`.
