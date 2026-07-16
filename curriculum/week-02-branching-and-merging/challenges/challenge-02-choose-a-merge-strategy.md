# Challenge 2 — Choose and justify a merge strategy

**Scenario:** You're the person a team asks "how should we integrate this?" For each of the three situations below, decide how you'd bring the work together — **fast-forward, three-way merge (`--no-ff`), or rebase** — and write down *why*. There's no universally correct answer; there are defensible answers and indefensible ones. Your grade is in the justification.

**Estimated time:** 1 hour.

## What to produce

A `SOLUTION.md` with one section per situation. Each section states: (1) your chosen strategy, (2) the exact command(s) you'd run, (3) two or three sentences of reasoning that name the trade-off, and (4) one consequence of choosing *differently*.

You may (and should) reproduce each situation in a scratch repo to check your reasoning against reality. But the write-up is the deliverable.

## The three situations

### Situation A — A tiny, private, up-to-date fix

You made a one-commit typo fix on a local branch `fix-readme`. You branched it off `main` five minutes ago and **`main` hasn't moved since**. No one else has seen this branch. You want it on `main`.

- Which strategy? Why?
- What would happen if you used `--no-ff` here instead? Is the extra merge commit worth it?

### Situation B — A long-lived shared feature branch

`feature-checkout` has been alive for three weeks. **Four teammates have pulled it and built commits on top of their copies.** Meanwhile `main` has advanced by ~40 commits. It's time to bring `feature-checkout` up to date with `main` before the final review.

- Merge `main` into `feature-checkout`, or rebase `feature-checkout` onto `main`? Why?
- What specifically goes wrong if you rebase this branch? Name who gets hurt and how.

### Situation C — Your own messy local branch, pre-review

You've got `feature-search` — **only on your machine, never pushed** — with a history like: "wip", "fix", "actually fix", "typo", "wip 2". It works, but the history is embarrassing. You're about to open it for review and want it to read as a clean sequence of logical commits. `main` has moved a little since you branched.

- What's the right tool to clean this up before sharing?
- Is rebasing safe *here*? What makes this case different from Situation B?

## Constraints on your answer

- Every choice must reference **the one rule** about rebasing shared history (from Lecture 2). At least one situation hinges on it.
- Distinguish "what makes the graph *pretty*" from "what is *safe*." Sometimes they conflict; say which wins and why.
- Don't just assert — tie each choice to a concrete consequence (revertibility, reviewer effort, broken teammate clones, linear vs. truthful history).

## Hints

<details>
<summary>A quick heuristic to argue from</summary>

Ask two questions in order: **(1) Has anyone else based work on these commits?** If yes → merge, never rebase. **(2) Do we value a linear history or a truthful one?** Linear → rebase private branches then fast-forward; truthful → `--no-ff` merges. Situation A is trivial; B is a trap; C is the legitimate home of rebase.
</details>

<details>
<summary>What "gets hurt" means in Situation B</summary>

Rebasing `feature-checkout` gives every commit a new hash. Your four teammates still have the *old* commits in their local copies. When they next pull, Git sees two divergent histories with "the same" changes, and they face a painful reconciliation — duplicated commits, phantom conflicts, or a forced reset that discards their local work. Merging avoids all of it.
</details>

## How success is judged

| Criterion | What "great" looks like |
|-----------|-------------------------|
| Correct calls | A → fast-forward; B → merge (not rebase); C → rebase (safe, private) |
| Reasoning quality | Each choice names the *actual* trade-off, not a slogan |
| The rule | You correctly apply "never rebase shared history" to B, and explain why C is exempt |
| Consequences | For each, you state one concrete downside of the alternative |

## Stretch

- Add a **Situation D of your own** drawn from a real project you've worked on, and reason through it the same way.
- For Situation A, actually run all three approaches in scratch repos and paste the three resulting `git log --oneline --graph` outputs side by side. Which would you rather read in six months?
