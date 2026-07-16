# Challenge 2 — Track Down a Regression Across Many Commits

**Type:** Open-ended. No single right answer. ~1–1.5 hours.

## Scenario

A user reports that your app's CSV export "sometimes" produces rows in the wrong order. It worked in the last tagged release, `v1.0.0`. Since then, `main` has taken **60+ commits** across refactors, dependency bumps, and unrelated features. Somewhere in there, a change broke the ordering. Reading 60 diffs is a bad use of a day. Your job is to find the exact culprit with `git bisect`, and to handle the two things that make bisect *lie*: commits that don't build, and a test that isn't fully deterministic.

## Your task

Create (or obtain) a repo with a long history containing one planted ordering regression, then find that commit with `git bisect` — ideally automated with `git bisect run` — while correctly handling un-buildable commits and any flakiness.

## Building your test bed

If you don't have a suitable repo, script one. Requirements for a good test bed:

- **40+ commits** between a good tag (`v1.0.0`) and `HEAD`.
- A small program with a **testable behaviour** (e.g. `sort_export.py` that must output rows in ascending ID order).
- **One** commit that introduces the regression (e.g. swaps a sort key, or drops `sorted()`), buried in the middle.
- **At least one** commit in the range that **fails to build/run for an unrelated reason** (e.g. a temporary syntax error later fixed) — so you're forced to use `skip` / exit `125`.
- **Bonus difficulty:** make the buggy behaviour *intermittent* in a naive test (e.g. depends on dict ordering that varies), so a lazy check gives wrong bisect results and you must make the check deterministic.

## Constraints

- The known-good endpoint must be the `v1.0.0` **tag**, not a raw SHA you looked up by reading diffs (that would defeat the point).
- Your check script must live **outside** the repo tree and use the bisect exit-code contract: `0` good, `1–124/126/127` bad, `125` skip.
- You must **not** find the bug by reading commit diffs. Bisect does the finding; you only judge "good or bad" at each stop.
- End with `git bisect reset`. A half-finished bisect is not a solution.

## Hints

<details>
<summary>Handling un-buildable commits</summary>

Have your check script attempt to run the program first. If it can't (non-zero from a build/import step), `exit 125` so bisect **skips** that commit rather than marking it bad. Marking an un-buildable commit "bad" will send bisect to the wrong answer.
</details>

<details>
<summary>Killing flakiness</summary>

A non-deterministic test poisons bisect — a "good" commit that fails once gets marked bad. Make the check deterministic: set `PYTHONHASHSEED=0`, sort explicitly, run the check a few times and only report "good" if it passes every time, or fix whatever nondeterminism the test itself has before you start.
</details>

<details>
<summary>Auditing what bisect did</summary>

`git bisect log` prints every good/bad/skip decision. Save it — it's your proof of how bisect converged and a way to `git bisect replay` if you need to redo the run.
</details>

## How success is judged

| Criterion | What "great" looks like |
|-----------|-------------------------|
| Correct culprit | Bisect names the actual planted commit, verified against your own record |
| Efficiency | ~`log2(N)` steps, not a linear scan; you can state the step count |
| Skip handling | An un-buildable commit was `skip`ped (exit 125), not mis-blamed |
| Determinism | Your check is provably repeatable; you explain how you removed flakiness |
| Discipline | Good endpoint is the `v1.0.0` tag; you reset cleanly; you never read diffs to "cheat" |

## Deliverable

A `HUNT.md` writeup (~250–350 words) containing: the good/bad endpoints you chose, your check script, the `git bisect log` output, the culprit commit's SHA and message, the number of steps taken, and a paragraph on how you handled the un-buildable commit and any flakiness. If you scripted the test bed, include the generator script so the hunt is reproducible.
