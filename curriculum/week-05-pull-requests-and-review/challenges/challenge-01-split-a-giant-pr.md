# Challenge 1 — Split a Giant PR into a Reviewable Stack

**Estimated time:** ~2 hours.

## Scenario

You went heads-down for three days and built a whole feature on one branch. It works. But the PR is a **900-line diff touching 14 files**: a new data model, a serializer, an API endpoint, a UI button, a config change, a dependency bump, *and* an unrelated typo fix you did "while you were in there." Your teammate opened it, sighed, and hasn't come back to it in two days.

Your job: turn that one unreviewable monster into a **stack of small PRs**, each of which a reviewer can approve in one sitting.

## Build the monster (so you have something to split)

Create a branch with a mixed, oversized change. Any repo works — reuse `pr-practice` or make a fresh one. The point is a branch whose diff clearly does **several distinct things**. For example, on one branch:

- Add a new module `models.py` (a data structure).
- Add `serialize.py` that turns the model into CSV (depends on `models.py`).
- Add `api.py` with an `export()` function that uses the serializer (depends on `serialize.py`).
- Add a `README` "Usage" section (independent).
- Fix a typo in an unrelated existing file (totally independent).

Commit it all on one branch, `big-feature`, in a few commits. Now you have a monster to dismantle.

## Your task

Produce a **stack of PRs** where each PR:

- Does **one logical thing**, described in a single sentence with no "and."
- Is independently reviewable — a reviewer doesn't need the other PRs open to understand it.
- Is correct on its own (tests, if any, pass at each step).
- Merges in a sensible order, each building on the last.

A good split of the example above:

```
PR #1: Add the data model (models.py)            ← depends on nothing
PR #2: Add the CSV serializer (serialize.py)     ← depends on #1
PR #3: Add the export() API (api.py)             ← depends on #2
PR #4: Document usage in the README              ← independent, ship anytime
PR #5: Fix the unrelated typo                    ← independent, ship first & fast
```

## Constraints

- **The unrelated typo must be its own PR.** Never smuggle unrelated changes into a feature PR — it's the #1 way to bloat a diff and hide the important part.
- **Each PR's description must state what it depends on** (e.g., "Stacked on #1 — review that first") so the reviewer reads them in order.
- **No PR may be larger than ~200 lines of diff** unless it's genuinely irreducible (a single generated file, say) — and if one is, justify why in its description.

## Hints

<details>
<summary>How do I actually carve one branch into several?</summary>

Two common techniques:

**1. Cherry-pick onto fresh branches.** Branch off `main` for each slice and cherry-pick (or re-apply) just that slice's commits:

```bash
git switch -c pr1-model main
git cherry-pick <commit-that-added-models.py>
git push -u origin pr1-model
gh pr create --fill

git switch -c pr2-serializer pr1-model    # stack on top of pr1
git cherry-pick <commit-that-added-serialize.py>
git push -u origin pr2-serializer
gh pr create --base pr1-model --fill      # base is pr1, not main!
```

Note `--base pr1-model`: a stacked PR targets the PR below it, so its diff shows *only* its own slice, not everything beneath.

**2. `git rebase -i` to reorder/split first**, then branch. If your commits are tangled (one commit touched both the model and the typo), use interactive rebase (Week 4) to split them into clean, single-purpose commits before you carve.
</details>

<details>
<summary>What about the "base" branch trap?</summary>

When you stack PR #2 on PR #1's branch, PR #2's diff is small and clean — *until #1 merges*. After #1 merges into `main`, retarget #2's base to `main` (GitHub often does this automatically, or `gh pr edit 2 --base main`). Merge the stack **bottom-up**: #1, then #2, then #3.
</details>

<details>
<summary>Isn't this a lot of overhead for one feature?</summary>

It's more PRs, but each is reviewed in minutes and merges the same day. The monster sat for two days and would've gotten a rubber-stamp "LGTM" anyway. Net, the stack ships sooner *and* gets real scrutiny. Feature flags (merge dark, flip on last) are the trick teams use to make stacks land continuously without half-features reaching users.
</details>

## How success is judged

| Criterion | What "great" looks like |
|-----------|-------------------------|
| Decomposition | Each PR is one clear logical change, sentence-describable, no "and" |
| Independence | The unrelated typo and the docs are their own PRs, not smuggled in |
| Order & stacking | Dependencies are explicit; bases are set correctly; merge order is bottom-up |
| Size | No PR is a monster; each is reviewable in one sitting |
| Reviewer empathy | Descriptions tell the reviewer what to read first and why |

## Deliverable

A short write-up (`split-writeup.md`) listing your PRs, each with its one-sentence purpose, its dependency, and its diff size — plus two sentences on the hardest slice to separate and how you did it. Include the PR URLs (or screenshots if the repo is private).
